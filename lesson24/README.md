# 责任链模式(Chain-of-responsibility pattern)

## 简介
责任链模式在面向对象程式设计里是一种软件设计模式，它包含了一些命令对象和一系列的处理对象。每一个处理对象决定它能处理哪些命令对象，它也知道如何将它不能处理的命令对象传递给该链中的下一个处理对象。该模式还描述了往该处理链的末尾添加新的处理对象的方法。

以下的日志类(logging)例子演示了该模式。 每一个logging handler首先决定是否需要在该层做处理，然后将控制传递到下一个logging handler。程序的输出是:

```
  Writing to debug output: Entering function y.
  Writing to debug output: Step1 completed.
  Sending via e-mail:      Step1 completed.
  Writing to debug output: An error has occurred.
  Sending via e-mail:      An error has occurred.
  Writing to stderr:       An error has occurred.
```

注意：该例子不是日志类的推荐实现方式。

同时，需要注意的是，通常在责任链模式的实现中，如果在某一层已经处理了这个logger，那么这个logger就不会传递下去。在我们这个例子中，消息会一直传递到最底层不管它是否已经被处理。


```
import java.util.*;

abstract class Logger 
{
    public static int ERR = 3;
    public static int NOTICE = 5;
    public static int DEBUG = 7;
    protected int mask;

    // The next element in the chain of responsibility
    protected Logger next;
    public Logger setNext( Logger l)
    {
        next = l;
        return this;
    }

    public final void message( String msg, int priority )
    {
        if ( priority <= mask ) 
        {
            writeMessage( msg );
            if ( next != null )
            {
                next.message( msg, priority );
            }
        }
    }
    
    protected abstract void writeMessage( String msg );

}

class StdoutLogger extends Logger 
{

    public StdoutLogger( int mask ) { this.mask = mask; }

    protected void writeMessage( String msg )
    {
        System.out.println( "Writting to stdout: " + msg );
    }
}


class EmailLogger extends Logger 
{

    public EmailLogger( int mask ) { this.mask = mask; }

    protected void writeMessage( String msg )
    {
        System.out.println( "Sending via email: " + msg );
    }
}

class StderrLogger extends Logger 
{

    public StderrLogger( int mask ) { this.mask = mask; }

    protected void writeMessage( String msg )
    {
        System.out.println( "Sending to stderr: " + msg );
    }
}

public class ChainOfResponsibilityExample
{
    public static void main( String[] args )
    {
        // Build the chain of responsibility
        Logger l = new StdoutLogger( Logger.DEBUG).setNext(
                            new EmailLogger( Logger.NOTICE ).setNext(
                            new StderrLogger( Logger.ERR ) ) );

        // Handled by StdoutLogger
        l.message( "Entering function y.", Logger.DEBUG );

        // Handled by StdoutLogger and EmailLogger
        l.message( "Step1 completed.", Logger.NOTICE );

        // Handled by all three loggers
        l.message( "An error has occurred.", Logger.ERR );
    }
}
```

## 加薪代码初步

```
//申请
class Request
{
	//申请类别
	private string requestType;
	public string RequestType
	{
		get {return requestType;}
		set {requestType = value;}
	}
	
	//申请内容
	private string requestContent;
	public string RequestContent
	{
		get {return RequestContent;}
		set {RequestContent = value;}
	}
	
	//数量
	private int number;
	public int Number
	{
		get {return number;}
		set {number = value;}
	}
}
```

```
//管理者
class Manager
{
	protected string name;
	public Manager(string name)
	{
		this.name = name;
	}
	
	//得到结果
	public void GetResult(string managerLevel, Request request)
	{
		if (request.RequestType=="经理") {
			if (request.RequestType == "请假" && request.Number <= 2)
			{
				Console.WriteLine("被批准");
			} else
			{
				Console.WriteLine("我无权处理");
			}
		} else if (request.RequestType=="总监") {
			if (request.RequestType == "请假" && request.Number <= 5)
			{
				Console.WriteLine("被批准");
			} else
			{
				Console.WriteLine("我无权处理");
			}
		} else if (request.RequestType=="总经理") {
			if (request.RequestType == "请假“) {
				Console.WriteLine("被批准");
			} else if (request.RequestType == "加薪" && request.Number <= 500) {
				Console.WriteLine("被批准");
			} else if (request.RequestType == "加薪" && request.Number > 500) {
				Console.WriteLine("再说");
			}
		}
	}
}
```

客户端代码如下

```
static void Main(string[] args)
{
	Manager jinli = new Manager("金利");
	Manager zongjian = new Manager("宗剑");
	Manager zhongjingli = new Manager("钟精励");
	
	Request request = new Reuqest();
	request.RequestType = "加薪";
	request.RequestContent = "小菜请求加薪";
	request.Number = 1000;
	
	Request request2 = new Reuqest();
	request2.RequestType = "请假";
	request2.RequestContent = "小菜请假";
	request2.Number = 3;	
	
	jinli.GetResult("经理",request2);
	zongjian.GetResult("总监",request2);
	zhongjingli.GetResult("总经理",request2);
	
	Console.Read();
}
```
代码评判:'管理者'类里面的'结果'方法比较长，加上有太多的分支判断，这种设计很不好。而且会不会增加其它的管理类别，比如说项目经理，部门经理，那就意味者需要去更改这个类，这个类承担了太多的责任，这违背了哪些设计原则？

类有太多的责任，这违背了单一职责原则，增加新的管理类别，需要修改这个类，违背了开放－封闭原则。

子类化加多太改善。

职责链模式:使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系，将这个对象连成一条链，并沿着个链传递请求，直到有一个对象处理为止。

### 加薪代码重构

```
//管理者
abstract class Manager
{
	protected Manager superior;
	
	public Manager(string name)
	{
		this.name = name;
	}
	
	//设置管理者的上级
	public void setSuperior(Manager superior)
	{
		this.superior = superior;
	}
}
```

经理类就可以去继承这个'管理者'类，只需重写'申请要求'的方法就可以了。

```
class CommonManager : Manager
{
	public CommonManager(string name) :base(name)
	{}
	
	public override void RequestApplications(Request request)
	{
		if (request.RequestType == "请假" && request.Number <= 2)
		{
			Console.WriteLine("被批准");
		} else
		{
			if (superior != null) superior.RequestApplications(request);
		}
	}
}
```

总监类同样继承'管理者类'。

```
//总监
class Majordomo :Manager
{
	public Major(string name): base(name)
	{}
	public override void RequestApplications(Request request)
	{
		if (request.RequestType == "请假" && request.Number <= 2)
		{
			Console.WriteLine("被批准");
		} else
		{
			if (superior != null) superior.RequestApplications(request);
		}
	}
}
```

总经理类的权限就是全部都需要处理。

```
//总经理
class GeneralManager :Manager
{
	public GeneralManager(string name): base(name)
	{}
	public override void RequestApplications(Request request)
	{
		if (request.RequestType == "请假")
		{
			Console.WriteLine("被批准");
		} else if (request.RequestType == "加薪" && request.Number <= 500)
		{
			Console.WriteLine("被批准");
		} else if (request.RequestType == "加薪" && request.Number > 500)
		{
			Console.WriteLine("再说吧");
		} 
	}
}
```

由于我们把你原来的一个'管理者'类改成了一个抽象类和三个具体类，此时类之间的灵活性就大大的增加了，如果我们需要扩展新的管理类别，只需要增加子类就可以。当然，还有一个关键，那就是客户端如何编写。

```
static void Main(string[] args)
{
	CommonManager jinli = new CommonManger("金利");
	Majordomo zongjian = new Majordomo("宗建");
	GeneralManager zhongjingli = newGeneralManager("钟精利");
	
	//设置上级
	jinli.SetSuperior(zongjian);
	zongjian.SetSuperior(zhongjingli);
	
	Request request = new Request();
	request.RequestType = "请假";
	request.RequestContent = "小菜请假";
	request.Number = 1;
	//客户端的申请都是有‘经理’发起，但是实际谁来决策都是由具体管理类处理，客户端不知道
	jinli.requestApplications(request);
	
	Request request1 = new Request();
	request1.RequestType = "请假";
	request1.RequestContent = "小菜请假";
	request1.Number = 4;
	jinli.requestApplications(request1);	
	
	Request request2 = new Request();
	request2.RequestType = "请求加薪";
	request2.RequestContent = "小菜请加薪";
	request2.Number = 400;
	jinli.requestApplications(request2);	
	
	Console.Read();
}

```

责任链模式是一种对象的行为模式【GOF95】。在责任链模式里，很多对象由每一个对象对其下家的引用而连接起来形成一条链。请求在这个链上传递，直到链上的某一个对象决定处理此请求。发出这个请求的客户端并不知道链上的哪一个对象最终处理这个请求，这使得系统可以在不影响客户端的情况下动态地重新组织链和分配责任。

### 门框夹核桃

职责链模式想要做到的事情其实就是把多个函数链起来调用。

该模式提出的时候FP并不如今日盛行，其作者选用类来包装需要被链接的多个函数，这无可厚非。

无论是class，还是function，都是为程序员提供抽象的手段。当我们想要链接的东西就是多个function，选择直接用function而非class就会显得更加自然，也更加轻量且合适。

```
object Loggers {
  val ERR = 3
  val NOTICE = 5
  val DEBUG = 7

  case class Event(message: String, priority: Int)

  type Logger = Event => Event

  def stdOutLogger(mask: Int): Logger = event => handleEvent(event, mask) {
    println(s"Writing to stdout: ${event.message}")
  }

  def emailLogger(mask: Int): Logger = event => handleEvent(event, mask) {
    println(s"Sending via e-mail: ${event.message}")
  }

  def stdErrLogger(mask: Int): Logger = event => handleEvent(event, mask) {
    System.err.println(s"Sending to stderr: ${event.message}")
  }

  private def handleEvent(event: Event, mask: Int)(handler: => Unit) = {
    if (event.priority <= mask) handler
    event
  }
}
```
三个log的的等级ERR，NOTICE和DEBUG和之前Java的实现是一样的。

一个case class Event，用来包裹需要被log的事件。

type Logger则是声明了一个函数签名，凡是符合这个签名的函数都可以作为logger被使用。

然后便是三个函数实现，它们将mask通过闭包封进函数内。这三个函数共同依赖一个私有handleEvent函数，其作用和Java代码中的message类似，判断mask和正在发生的事件之间优先级大小关系，并以此决定当前logger是否需要处理该事件。

哎？等一下，这个是职责链模式啊，那个啥，链在哪儿呢？

```
object ChainRunner {

  import chain.Loggers._

  def main(args: Array[String]) {
    val chain = stdOutLogger(DEBUG) andThen emailLogger(NOTICE) andThen stdErrLogger(ERR)

    chain(Event("Entering function y.", DEBUG))
    chain(Event("Step1 completed.", NOTICE))
    chain(Event("An error has occurred.", ERR))
  }
}
```

以上代码中的andThen就可以把三个logger链在一起。







