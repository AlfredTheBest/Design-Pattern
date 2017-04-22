# 适配器模式(Adapter Pattern)
## 简介
在设计模式中，适配器模式有时候也称包装样式或者包装(wrapper)。将一个类的接口转接成用户所期待的。一个适配使得因接口不兼容而不能在一起工作的类工作在一起，做法是将类自己的接口包裹在一个已存在的类中。

适配器模式包含两种，一种是类适配器，另一种是对象适配器。`类适配器`是通过类的继承实现的适配，而`对象适配器`是通过对象间的关联关系，组合关系实现的适配。二者在实际项目中都会经常用到，由于对象适配器是通过类间的关联关系进行耦合的，因此在设计时就可以做到比较灵活，而类适配器就只能通过覆写源角色的方法进行拓展，在实际项目中，对象适配器使用到的场景相对较多。在iOS开发中也推荐多使用组合关系，而尽量减少继承关系，这是一种很好的编程习惯，因此我在这里只介绍对象适配器，想了解更多的关于类适配器的话，请自行Google之。

```
Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.
将一个类的接口变成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。
```

## 简例

### 适配器模式说明

* Target目标角色 该角色定义把其他类转换为何种接口，也就是我们的期望接口。
* Adaptee源角色 你想把“谁”转换成目标角色，这个“谁”就是源角色，它是已经存在的、运行良好的类或对象。
* Adapter适配器角色 适配器模式的核心角色，其他两个角色都是已经存在的角色，而适配器角色是需要新建立的，他的职责非常简单：把源角色转换为目标角色。

### 适配器模式优点

* 适配器模式可以让两个没有任何关系的类在一起运行，只要适配器这个角色能够搞定他们就成。
* 增加了类的透明性。我们访问的是目标角色，但是实现却在源角色里。
* 提高了类的复用度。源角色在原有系统中还是可以正常使用的。
* 灵活性非常好。不想要适配器时，删掉这个适配器就好了，其他代码不用改。


Target

```
#import <Foundation/Foundation.h>

@protocol Target <NSObject>

- (void)userExpectInterface;

@end
```

Adaptee

```
#import <Foundation/Foundation.h>

@interface Adaptee : NSObject

- (void)doSometing;

@end

@implementation Adaptee

- (void)doSometing
{
    NSLog(@"adaptee doing something!");
}

@end
```

Adapter

```
#import "Target.h"
#import "Adaptee.h"

@interface Adapter : NSObject<Target>

@property (strong, nonatomic) Adaptee *adaptee;

- (id)initWithAdaptee:(Adaptee *)adaptee;

@end

@implementation Adapter

@synthesize adaptee = _adaptee;


- (id)initWithAdaptee:(Adaptee *)adaptee
{
    if (self = [super init]) {
        _adaptee = adaptee;
    }
    return self;
}

- (void)userExpectInterface
{
    [self.adaptee doSometing];
}

@end
```

main

```
#import <Foundation/Foundation.h>
#import "Adapter.h"
#import "Adaptee.h"
#import "Target.h"

int main(int argc, const char * argv[])
{

    @autoreleasepool {

        Adaptee *adaptee = [[Adaptee alloc]init];
        id<Target> object = [[Adapter alloc]initWithAdaptee:adaptee];

        [object userExpectInterface];

    }
    return 0;
}
```

何时用适配器模式？两个类所做的事情相同或者相似，但是具有不同的接口时要使用它。 客户端代码可以统一调用接口就行了，这样应该可以更简单，更直接，更紧凑。***在双方都不太容易修改的时候再食用适配器模式适配。***