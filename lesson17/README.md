# 状态模式(State Pattern)
## 简介
状态模式，当一个对象在内在状态改变时，允许改变起行为，这个对象看起来像是改变了其类。

状态模式主要解决的是当控制一个对象状态转换的条件表达式过于复杂的情况。把状态的判断逻辑转移到表示不同状态的一系列类中，可以把复杂的判断逻辑简化。

## 简例

### 工作状态－分类板

```
public class Work
{
	private int hour;
	public int Hour
	{
		get {return hour;}
		set {hour = value;}
	}
	
	private bool finish = false;
	public bool TaskFinished
	{
		get {return finish;}
		set { finish = value;}
	}
	
	public void WriteProgram()
	{
		
		if (hour < 12)
		{
			Console.WriteLine("当前时间:{0}点，上午"，hour);
		} else if (hour < 13)
		{
			Console.WriteLine("当前时间:{0}点，午饭"，hour);
		} else if (hour < 17) 
		{
			Console.WriteLine("当前时间:{0}点，下午"，hour);
		} else {
			if (finish) {
			Console.WriteLine("当前时间:{0}点，下班回家"，hour);
			} else {
				if (hour < 21)
				{
					Console.WriteLine("当前时间:{0}点，加班哦，疲累之极"，hour);
				} else {
					Console.WriteLine("当前时间:{0}点，不行了，睡着了"，hour);
				}
			}
		}
	}
}
```

客户端程序如下

```
static void Main(string[[] args)
{
	//紧急项目
	Work emergencyProjects = new Work();
	emergencyProjects.Hour = 9;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 10;
	emergencyProjects.WriteProgram();	
	emergencyProjects.Hour = 12;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 13;
	emergencyProjects.WriteProgram();	
	emergencyProjects.Hour = 14;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 17;
	emergencyProjects.WriteProgram();	
	
	//emergencyProjects.WorkFinished = true;
	emergencyProjects.TaskFinished = false;
	
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 19;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 22;
	emergencyProjects.WriteProgram();	
	
	Console.Read();
		
}
```
### 结果
此段代码有很大的问题。MartinFowler曾在《重构》中写过一个很重要的代码味道，叫做'Long Method',方法如果过长其实极有可能有坏味道了。

'Work'类的'WriteProgram'方法很长，而且有很多判断的分支，这也就意味着它的责任过大了。无论是任何状态，都需要通过它来改变，这实际上是很糟糕的。

***面对对象设计其实就是希望做到代码的责任分解。***这个类违背了'单一职责原则'。而且由于'WriteProgram'的方法里面有这么多判断，使得任何需求的改动或增加，都需要更改这个方法。又违背了'开放－封闭原则'。这类有个解决方案，就是'状态模式'。

***状态模式的好处是将与特定状态相关的行为局部化，并且将不同状态的行为分割开来。***

***将特定的状态相关的行为都放入一个对象中，由于所有与状态相关的代码都存在于摸个ConcreteState中，所以通过定义新的字类可以很容易地增加新的状态与转换。这样做的目的就是为了消除庞大的条件分支语句。状态模式通过把各种状态转移逻辑分布到State的子类之间，来减少相互间的依赖。***

***当一个对象的行为取决于它的状态，并且它必须在运行时刻根据状态改变它的行为时，就可以考虑使用状态模式了。***
	
	
	
### 工作状态－状态模式板

```
//抽象状态

public abstract class State
{
	public abstract void WriteProgram(Work w);
}

```

上午工作状态

```
public class ForenoonState: State
{
	public override void WriteProgram (Work w)
	{
		if (w.hour < 12)
		{
			Console.WriteLine("当前时间:{0}点 上午"，w.Hour);
		} else
		{
			w.SetState(new NoonState());
			w.WriteProgram();
		}
	}
}
```

中午工作状态

```
public class NoonState: State
{
	public override void WriteProgram (Work w)
	{
		if (w.hour < 13)
		{
			Console.WriteLine("当前时间:{0}点 中午"，w.Hour);
		} else
		{
			w.SetState(new AfterNoonState());
			w.WriteProgram();
		}
	}
}
```

下午工作状态

```
public class AfterNoonState: State
{
	public override void WriteProgram (Work w)
	{
		if (w.hour < 13)
		{
			Console.WriteLine("当前时间:{0}点 下午"，w.Hour);
		} else
		{
			w.SetState(new EveningState());
			w.WriteProgram();
		}
	}
}
```

晚间工作状态

```
public class EveningState: State
{
	public override void WriteProgram (Work w)
	{
		if (w.TaskFinished)
		{
			w.SetState(new RestState());
			w.WriteProgeam();
		} else 
		{
			if (w.Hour < 21) 
			{
				Console.WriteLine("当前时间:{0}点 加班哦，疲累"，w.Hour);
			} else
			{
				w.SetState(new SleepState());
				w.WriteProgram();
			}
			
		}
		
	}
}
```

睡眠状态

```
public class SleepingState: State
{
	public override void WriteProgram (Work w)
	{
		if (w.hour < 13)
		{
				Console.WriteLine("当前时间:{0}点 不行了，睡着了。"，w.Hour);
		}
	}
}
```

下班休息状态

```
public class RestState: State
{
	public override void WriteProgram(Work w)
	{
		Console.WriteLine("当前时间:{0}点 下班回家了。"，w.Hour);
	}
}
```

工作类，此时没有了过长的分支判断语句

```
public class Work
{
	private State current;
	public Work()
	{
		current = new ForenoonState();
	}
	
	private double hour;
	public double Hour
	{
		get {return hour;}
		set {hour = value;}
	}
	
	private bool finish = false;
	public bool TaskFinshed
	{
		get { return finsh;}
		set { finished = value;}
	}
	
	public void SetState(State s)
	{
		current = s;
	}
	
	public void WriteProgram()
	{
		current.WriteProgram(this);
	}

}
```

客户端代码，没有任何改动。但我们的程序却更加灵活易变了。

```
static void Main(string[[] args)
{
	//紧急项目
	Work emergencyProjects = new Work();
	emergencyProjects.Hour = 9;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 10;
	emergencyProjects.WriteProgram();	
	emergencyProjects.Hour = 12;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 13;
	emergencyProjects.WriteProgram();	
	emergencyProjects.Hour = 14;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 17;
	emergencyProjects.WriteProgram();	
	
	//emergencyProjects.WorkFinished = true;
	emergencyProjects.TaskFinished = false;
	
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 19;
	emergencyProjects.WriteProgram();
	emergencyProjects.Hour = 22;
	emergencyProjects.WriteProgram();	
	
	Console.Read();
		
}
```

## 总结与分析

  状态模式的主要优点在于封装了转换规则，并枚举可能的状态，它将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为，还可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数；其缺点在于使用状态模式会增加系统类和对象的个数，且状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱，对于可以切换状态的状态模式不满足“开闭原则”的要求。

