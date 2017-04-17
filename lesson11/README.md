# 原型模式(Prototype pattern)
## 简介
原型模式是创建型模式的一种,其特点在于通过「复制」一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的「原型」，这个原型是可定制的。

***原型模式多用于创建复杂的或者耗时的实例，因为这种情况下，复制一个已经存在的实例使程序运行更高效；或者创建值相等，只是命名不一样的同类数据。***

***
Prototype原型模式是一种创建型设计模式，它主要面对的问题是：“某些结构复杂的对象”的创建工作；由于需求的变化，这些对象经常面临着剧烈的变化，但是他们却拥有比较稳定一致的接口。下面我们先来看下关联的的几种设计模式，予以区分，再来说说原型模式。

* Singleton单件模式解决的问题是：实体对象个数问题（这个现在还不太容易混）
* AbstractFactory抽象工厂模式解决的问题是：“一系列互相依赖的对象”的创建工作
* Builder生成器模式解决的问题是：“一些复杂对象”的创建工作，子对象变化较频繁，对算法相对稳定
* FactoryMethor工厂方法模式解决的问题是：某个对象的创建工作。

《设计模式》中说道：使用原型实例指定创建对象的种类，然后通过拷贝这些原型来创建新的对象。


## 例子
```
/** Prototype Class **/
 public class Cookie implements Cloneable {
   
    public Object clone() throws CloneNotSupportedException
    {
        //In an actual implementation of this pattern you would now attach references to
        //the expensive to produce parts from the copies that are held inside the prototype.
        return (Cookie) super.clone();
    }
 }
 
 /** Concrete Prototypes to clone **/
 public class CoconutCookie extends Cookie { }
 
 /** Client Class**/
 public class CookieMachine
 {
 
   private Cookie cookie;//cookie必须是可复制的
 
     public CookieMachine(Cookie cookie) { 
         this.cookie = cookie; 
     } 

    public Cookie makeCookie()
    {
        try
        {
            return (Cookie) cookie.clone();
        } catch (CloneNotSupportedException e)
        {
            e.printStackTrace();
        }
        return null;
    } 

 
     public static void main(String args[]){ 
         Cookie tempCookie =  null; 
         Cookie prot = new CoconutCookie(); 
         CookieMachine cm = new CookieMachine(prot); //设置原型
         for(int i=0; i<100; i++) 
             tempCookie = cm.makeCookie();//通过复制原型返回多个cookie 
     } 
 }
```
现在我们再来看看原型模式的几个要点：
 
* Prototype模式同样用于隔离类对象的使用者和具体类型（易变类）之间的耦合关系，它同样要求这些“易变类”拥有“稳定的接口”。
* Prototype模式对于“如何创建易变类的实体对象”采用“原型克隆”的方法来实现，它使得我们可以非常灵活地动态创建“拥有某些稳定接口”的新对象——所需工作仅仅是注册一个新类的对象（即原型），然后在任何需要的地方不断地Clone。
* Prototype模式中的Clone方法可以利用Object类的MemberwiseClone（）或者序列化来实现深拷贝。

***

### Javascript中的prototype就是使用了原型模式

```
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
}

// 通过原型模式来添加所有实例共享的方法
// sayName() 方法将会被Person的所有实例共享，而避免了重复创建
Person.prototype.sayName = function () {
  console.log(this.name);
};

var person1 = new Person('Weiwei', 27, 'Student');
var person2 = new Person('Lily', 25, 'Doctor');

console.log(person1.sayName === person2.sayName); // true

person1.sayName(); // Weiwei
person2.sayName(); // Lily 
```
正如上面的代码所示，通过原型模式定义的方法sayName()为所有的实例所共享。也就是， person1和person2访问的是同一个sayName()函数。同样的，公共属性也可以使用原型模式进行定义。例如：

```
function Chinese (name) {
    this.name = name;
}

Chinese.prototype.country = 'China'; // 公共属性，所有实例共享
```
由于所有的实例对象共享同一个prototype对象，那么从外界看起来，prototype对象就好像是实例对象的原型，而实例对象则好像"继承"了prototype对象一样。它是浅拷贝。

//TODO clone的原理()

JAVA的clone()实现机制涉及到了反射、IO流操作、序列化等。
