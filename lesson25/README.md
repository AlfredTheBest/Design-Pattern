# 中介者模式(Mediator Pattern)

## 简介
在用户与用户直接聊天的设计方案中，用户对象之间存在很强的关联性，将导致系统出现如下问题：

* 系统结构复杂：对象之间存在大量的相互关联和调用，若有一个对象发生变化，则需要跟踪和该对象关联的其他所有对象，并进行适当处理。
* 对象可重用性差：由于一个对象和其他对象具有很强的关联，若没有其他对象的支持，一个对象很难被另一个系统或模块重用，这些对象表现出来更像一个不可分割的整体，职责较为混乱。
* 系统扩展性低：增加一个新的对象需要在原有相关对象上增加引用，增加新的引用关系也需要调整原有对象，系统耦合度很高，对象操作很不灵活，扩展性差。
* 在面向对象的软件设计与开发过程中，根据“单一职责原则”，我们应该尽量将对象细化，使其只负责或呈现单一的职责。
* 对于一个模块，可能由很多对象构成，而且这些对象之间可能存在相互的引用，为了减少对象两两之间复杂的引用关系，使之成为一个松耦合的系统，我们需要使用中介者模式，这就是中介者模式的模式动机。

***中介者模式(Mediator Pattern)定义：用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。中介者模式又称为调停者模式，它是一种对象行为型模式。***


## 案例
安理会做中介

```
//联合国机构
abstract class UnitedNations
{
	//声明
	public abstract void Declare(string message,Country colleagues);
}
```

国家类 相当于Colleague类

```
abstract class Country
{
	protected UnitedNations mediator;
	
	public Country(UnitedNations mediator)
	{
		this.mediator = mediator;
	}
}
```

美国类 相当于ConcreteColleague1类

```
class USA: Country
{
	public USA(UnitedNations mediator): base(mediator)
	{}
	//声明
	public void Declare(string message)
	{
		mediator.Declare(message, this);
	}
	
	//获得消息
	public void GetMessage(string message)
	{
		Console.WriteLine("美国获得对方消息" + message);
	}
}
```

伊拉克类 相当于ConcreteColleague2类

```
class Iraq: Country
{
	public Iraq(UnitedNations mediator): base(mediator)
	{}
	//声明
	public void Declare(string message)
	{
		mediator.Declare(message, this);
	}
	
	//获得消息
	public void GetMessage(string message)
	{
		Console.WriteLine("美国获得对方消息" + message);
	}
}
```

联合国安理会 相当于ConcreteMediator类

```
//联合国安全理事会
class UnitedNationsSecurityCouncil: UnitedNations
{
	private USA colleague1;
	private Iraq colleague2;
	
	//美国
	public USA Colleague1
	{
		set {colleague1 = value;}
	}
	
	//伊拉克
		public Iraq Colleague2
	{
		set {colleague2 = value;}
	}
	
	//声明
	public override void Declare(string message, Country colleague)
	{
		if(colleague == colleague1)
		{
			colleague2.GetMessage(message);
		}else
		{
			colleague1.GetMessage(message);
		}
	}
}
```

客户端调用

```
static void Main(string[] args)
{
	UnitedNationsSecurityCouncil UNSC = new UnitedNationsSecurityCouncil();
	
	USA c1 = new USA(UNSC);
	Iraq c2 = new Iraq(UNSC);
	
	UNSC.Colleague1 = c1;
	UNSC.Colleague2 = c2;
	
	c1.Declare("不准研发核武器");
	c2.Declare("我们没有，但我们不怕侵略");
	
	Console.Read();	
}
```

中介者模式的优点首先是Mediator的出现减少了各个Colleague的耦合,可以毒瘤地改变和复用各个Colleague类和Mediator。其次由于把对象如何协作进行类抽象，将中介作为一个独立的概念并且将其封装在一个对象中，这样关注的对象就从对象本身的行为转移到它们的交互上来，也就是站在一个更宏观的角度去看待系统。

但是，由于ConcreyeMediator控制了集中化，于是就把交互复杂性变为了中介者的复杂性，这就使得中介者会变得比任何一个ConcreteColleague都复杂。






