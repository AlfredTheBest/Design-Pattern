#装饰原则(Decorator pattern)
##简介
Decorator装饰模式是一种结构型模式，它主要是解决：“过度地使用了继承来扩展对象的功能”，由于继承为类型引入的静态特质，使得这种扩展方式缺乏灵活性；并且随着子类的增多（扩展功能的增多），各种子类的组合（扩展功能的组合）会导致更多子类的膨胀（多继承）。继承为类型引入的静态特质的意思是说以继承的方式使某一类型要获得功能是在编译时。所谓静态，是指在编译时；动态，是指在运行时。
GoF《设计模式》中说道：动态的给一个对象添加一些额外的职责。就增加功能而言，Decorator模式比生成子类更为灵活。
下面来看看Decorator模式的结构：

![Decorator1](Decorator1.jpg)

看这个结构好像不是很明白，下面我根据代码讲解一下这个结构。我想了一个场景：我们现在用的手机功能很多，我就用Decorator模式实现一下对某个手机的GSP和蓝牙功能扩展。
首先，我们需要一个手机的接口或者是抽象类，我这里就用抽象类来实现，代码如下：

```
public abstract class AbstractCellPhone
    {
        public abstract string CallNumber();
        public abstract string SendMessage();
}
```

AbstractCellPhone也就是结构图中的Component，然后，我再来实现Nokia和Moto的手机类，这类要继承AbstractCellPhone，也就是图中ConcreteComponent类要继承Component，实现代码如下：

```
public class NokiaPhone : AbstractCellPhone
    {
        public override string CallNumber()
        {
            return "NokiaPhone call sombody";
        }
 
        public override string SendMessage()
        {
            return "NokiaPhone send a message to somebody";
        }
    }
 
    public class MotoPhone : AbstractCellPhone
    {
        public override string CallNumber()
        {
            return "MotoPhone call sombody";
        }
 
        public override string SendMessage()
        {
            return "MotoPhone send a message to somebody";
        }
 } 
```

接下来我需要一个Decorator接口或者抽象类，实现代码如下：

```
public abstract class Decorator:AbstractCellPhone
    {
        AbstractCellPhone _phone;
 
        public Decorator(AbstractCellPhone phone)
        {
            _phone = phone;
        }
 
        public override string CallNumber()
        {
            return _phone.CallNumber();
        }
 
        public override string SendMessage()
        {
            return _phone.SendMessage();
        }
  }
```
正如结构图中，这个Decorator即继承了AbstractCellPhone，又包含了一个私有的AbstractCellPhone的对象。这样做的意义是：Decorator类又使用了另外一个Component类。我们可以使用一个或多个Decorator对象来“装饰”一个Component对象，且装饰后的对象仍然是一个Component对象。在下来，我要实现GSP和蓝牙的功能扩展，它们要继承自Decorator，代码如下：

```
public class DecoratorGPS : Decorator
    {
        public DecoratorGPS(AbstractCellPhone phone)
            : base(phone)
        { }
 
        public override string CallNumber()
        {
            return base.CallNumber() + " with GPS";
        }
 
        public override string SendMessage()
        {
            return base.SendMessage() + " with GPS";
        }
    }
 
    public class DecoratorBlueTooth : Decorator
    {
        public DecoratorBlueTooth(AbstractCellPhone phone)
            : base(phone)
        { }
 
        public override string CallNumber()
        {
            return base.CallNumber() + " with BlueTooth";
        }
 
        public override string SendMessage()
        {
            return base.SendMessage() + " with BlueTooth";
        }
 }
```
最后，用客户端程序验证一下：

```
static void Main(string[] args)
        {
             AbstractCellPhone phone = new NokiaPhone();
            Console.WriteLine(phone.CallNumber());
            Console.WriteLine(phone.SendMessage());
            DecoratorGPS gps = new DecoratorGPS(phone);     //add GSP
            Console.WriteLine(gps.CallNumber());
            Console.WriteLine(gps.SendMessage());
            DecoratorBlueTooth bluetooth = new DecoratorBlueTooth(gps); //add GSP and bluetooth
            Console.WriteLine(bluetooth.CallNumber());
            Console.WriteLine(bluetooth.SendMessage());
            Console.Read();
     }
```

执行结果：
NokiaPhone call sombody
NokiaPhone send a message to somebody
NokiaPhone call sombody with GPS
NokiaPhone send a message to somebody with GPS
NokiaPhone call sombody with GPS with BlueTooth
NokiaPhone send a message to somebody with GPS with BlueTooth
 
从执行的结果不难看出扩展功能已被添加。最后再说说Decorator装饰模式的几点要点：

1. 通过采用组合、而非继承的手法，Decorator模式实现了在运行时动态的扩展对象功能的能力，而且可以根据需要扩展多个功能。避免了单独使用继承带来的“灵活性差”和“多子类衍生问题”。
2. Component类在Decorator模式中充当抽象接口的角色，不应该去实现具体的行为。而且Decorator类对于Component类应该透明——换言之Component类无需知道Decorator类，Decorator类是从外部来扩展Component类的功能。
3. Decorator类在接口上表现为is-a Component的继承关系，即Decorator类继承了Component类所具有的接口。但在实现上又表现为has-a Component的组合关系，即Decorator类又使用了另外一个Component类。我们可以使用一个或多个Decorator对象来“装饰”一个Component对象，且装饰后的对象仍然是一个Component对象。（在这里我想谈一下我的理解：当我们实例化一个Component对象后，要给这个对象扩展功能，这时我们把这个Component对象当作参数传给Decorator的子类的构造函数——也就是扩展方法的功能类。对于引用类型传参时，实际上只是传递对象的地址，这样，在功能扩展是，操作的应该是同一个对象）
4. Decorator模式并非解决“多子类衍生的多继承”问题，Decorator模式应用的要点在于解决“主体类在多个方向上的扩展功能”——是为“装饰”的含义。Decorator是在运行时对功能进行组合。

##实例
