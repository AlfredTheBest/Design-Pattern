# 访问者模式(Visitor Pattern)

## 简介

访问者模式是一种将算法与对象结构分离的软件设计模式。

这个模式的基本想法如下：首先我们拥有一个由许多对象构成的对象结构，这些对象的类都拥有一个accept方法用来接受访问者对象；访问者是一个接口，它拥有一个visit方法，这个方法对访问到的对象结构中不同类型的元素作出不同的反应；在对象结构的一次访问过程中，我们遍历整个对象结构，对每一个元素都实施accept方法，在每一个元素的accept方法中回调访问者的visit方法，从而使访问者得以处理对象结构的每一个元素。我们可以针对对象结构设计不同的实在的访问者类来完成不同的操作。

访问者模式使得我们可以在传统的单分派语言（如Smalltalk、Java和C++）中模拟双分派技术。对于支持多分派的语言（如CLOS），访问者模式已经内置于语言特性之中了，从而不再重要。

```Java
 interface Visitor {
     void visit(Wheel wheel);
     void visit(Engine engine);
     void visit(Body body);
     void visit(Car car);
 }

 class Wheel {
     private String name;
     Wheel(String name) {
         this.name = name;
     }
     String getName() {
         return this.name;
     }
     void accept(Visitor visitor) {
         visitor.visit(this);
     }
 }
  
 class Engine {
     void accept(Visitor visitor) {
         visitor.visit(this);
     }
 }

 class Body {
     void accept(Visitor visitor) {
         visitor.visit(this);
     }
 }

 class Car {
     private Engine  engine = new Engine();
     private Body    body   = new Body();
     private Wheel[] wheels 
         = { new Wheel("front left"), new Wheel("front right"),
             new Wheel("back left") , new Wheel("back right")  };
     void accept(Visitor visitor) {
         visitor.visit(this);
         engine.accept(visitor);
         body.accept(visitor);
         for (int i = 0; i < wheels.length; ++ i)
             wheels[i].accept(visitor);
     }
 }

 class PrintVisitor implements Visitor {
     public void visit(Wheel wheel) {
         System.out.println("Visiting " + wheel.getName()
                             + " wheel");
     }
     public void visit(Engine engine) {
         System.out.println("Visiting engine");
     }
     public void visit(Body body) {
         System.out.println("Visiting body");
     }
     public void visit(Car car) {
         System.out.println("Visiting car");
     }
 }

 public class VisitorDemo {
     static public void main(String[] args) {
         Car car = new Car();
         Visitor visitor = new PrintVisitor();
         car.accept(visitor);
     }
 }
```

## 实例

### 男人和女人

'人'类，是'男人'和'女人'类的抽象类

```
abstract class person
{
	protected string action;
	public string Action
	{
		get {return action;}
		set {action = value};
	}
	//得到结论或者反应
	public abstract void GetConclusion();
}
```

"男人"类

```
class Man: Person
{
	//得到结论或反应
	public override void GetConclusion()
	{
		if (action == "成功")
		{
			Console.WriteLine("{0}{1}时，背后多半有一个伟大的女人",this.GetType().name,action);
		}
		else if (action == "失败")
		{
			Console.WriteLine("{0}{1}时，"蒙头喝酒，谁也不用劝",this.GetType().name,action);
		}
		else if (action == "恋爱")
		{
			Console.WriteLine("{0}{1}时，"凡事不懂也要装懂",this.GetType().name,action);
		}
	}
	
}
```


"女人"类

```
class Woman: Person
{
	//得到结论或反应
	public override void GetConclusion()
	{
		if (action == "成功")
		{
			Console.WriteLine("{0}{1}时，背后多半有一个不成功的男人",this.GetType().name,action);
		}
		else if (action == "失败")
		{
			Console.WriteLine("{0}{1}时，"泪眼汪汪，谁也劝不了",this.GetType().name,action);
		}
		else if (action == "恋爱")
		{
			Console.WriteLine("{0}{1}时，"凡事懂也要装不懂",this.GetType().name,action);
		}
	}
	
}
```

客户端代码

```
static void Main(string[] args)
{
	IList<Person> persons = new List<Person>();
	
	Person man1 = new Man();
	man1.Action = "成功";
	persons.add(man1);
	Person woman1 = new Woman();
	woman1.Action = "成功";
	persons.add(woman1);
	
	Person man2 = new Man();
	man2.Action = "失败";
	persons.add(man2);
	Person woman2 = new Woman();
	woman2.Action = "失败";
	persons.add(woman2);
	
	Person man3 = new Man();
	man3.Action = "恋爱";
	persons.add(man3);
	Person woman3 = new Woman();
	woman3.Action = "恋爱";
	persons.add(woman3);
	
	foreach (Person item in persons)
	{
		item.GetConclusion();
	}
	
	Console.Read();
	
}
```

这个算是面向对象编程，但是'男人'和'女人'类当中的那些if....else....很是碍眼。而且如果我们增加一个'结婚状态',怎么改？

### 用模式实现

'状态'的抽象和'人'的抽象

```
abstract class Action
{
	//得到男人结论或反应
	public abstract void GetManConclusion(Man concreteElementA);
	public abstract void GetWomanConclusion(Woman concreteElementB);
}

abstract class Person
{
	//接受
	public abstract void Accept(Action visitor);
}

```

具体"状态"类

```
//成功
class Success: Action
{
	public override void GetManConclusion(Man concreteElementA)
	{
		Console.WriteLine("{0}{1}时，背后多半有一个伟大的女人",this.GetType().name,action);
	}
	
	public abstract void GetWomanConclusion(Woman concreteElementB)
	{
		Console.WriteLine("{0}{1}时，背后多半有一个不成功的男人",this.GetType().name,action);
	}
}

//失败
class Falling: Action
{
	public override void GetManConclusion(Man concreteElementA)
	{
		Console.WriteLine("{0}{1}时，"蒙头喝酒，谁也不用劝",this.GetType().name,action);
	}
	
	public abstract void GetWomanConclusion(Woman concreteElementB)
	{
		Console.WriteLine("{0}{1}时，"泪眼汪汪，谁也劝不了",this.GetType().name,action);
	}
}

//恋爱
class Amativeness: Action
{
	public override void GetManConclusion(Man concreteElementA)
	{
		Console.WriteLine("{0}{1}时，"凡事不懂也要装懂",this.GetType().name,action);
	}
	
	public abstract void GetWomanConclusion(Woman concreteElementB)
	{
		Console.WriteLine("{0}{1}时，"凡事懂也要装不懂",this.GetType().name,action);
	}
}

```

"男人"类和"女人"类

```
//男人
class Man : Person
{
	public override void Accept(Action visitor)
	{
		//***
		visitor.GetManConclusion(this);
	}
}


//女人
class Woman : Person
{
	public override void Accept(Action visitor)
	{
		//***
		visitor.GetWomanConclusion(this);
	}
}
```

这里需要讲下双派技术，首先客户端将具体状态作为参数传递给"男人"完成了一次分派，然后男人类调用方法"男人反应",同时将自己作为参数传递进去。这便完成了第二次分派。双分派意味着得到执行的操作决定于请求的种类和两种接受者的类型。"接受"方法就是一个双分派的操作，它得到执行的操作不仅决定于'状态'类的具体状态，还决定于它访问的'人'的类别。


```
//对象结构
class ObjectStructure
{
	private IList<Person>elements = new List<Person>();
	
	//增加
	public void Attach(Person element)
	{
		elements.Add(element);
	}
	
	//移除
	public void Detach(Person element)
	{
		elements.Remove(element);
	}	
	
	//查看显示
	public void Display(Action visitor)
	{
		foreach(Person e in elements)
		{
			e.Accept(visitor);
		}
	}	
}
```

客户端代码

```
static void Main(string[] args)
{
	ObjectStructure o = new ObjectStructure();
	o.Attach(new Man());
	o.Attach(new Man());
	
	//成功时反应
	Success v1 = new Success();
	o.Display(v1);

	//失败时反应
	Success v1 = new Success();
	o.Display(v1);

	//恋爱时反应
	Success v1 = new Success();
	o.Display(v1);
	
	Console.Read();
	
}
```


***访问者适合于数据结构相对稳定的系统。它把数据结构和作用于结构之上的操作之间的耦合解脱开来，使得操作集合可以相对自由地y演化。***

***访问者模式的目的是要把处理从数据结构分离出来。如果这样的系统有比较稳定的数据结构，又有易于变化的算法的话，使用访问者模式就是比较适合的，因为访问者模式使得算法操作的增加变得容易。***

***那其实访问者模式的优点就是增加新的操作很容易，因为增加新的操作就意味着增加一个新的访问者,访问者模式将有关的行为集中到一个访问者对象中。***

***那访问者的缺点其实也就是使得增加新的数据结构变的困难了。***

正如《设计模式》的作者GoF对访问者模式的描述：大多数情况下，你并需要使用访问者模式，但是当你一旦需要使用它时，那你就是真的需要它了。当然这只是针对真正的大牛而言。在现实情况下（至少是我所处的环境当中），很多人往往沉迷于设计模式，他们使用一种设计模式时，从来不去认真考虑所使用的模式是否适合这种场景，而往往只是想展示一下自己对面向对象设计的驾驭能力。编程时有这种心理，往往会发生滥用设计模式的情况。所以，在学习设计模式时，一定要理解模式的适用性。必须做到使用一种模式是因为了解它的优点，不使用一种模式是因为了解它的弊端；而不是使用一种模式是因为不了解它的弊端，不使用一种模式是因为不了解它的优点。










