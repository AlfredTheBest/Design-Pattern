# 工厂方法模式(Factory Method pattern)
## 简介

“工厂方法模式(Factory Method Pattern)又称为工厂模式，也叫虚拟构造器(Virtual Constructor)模式或者多态工厂(Polymorphic Factory)模式，它属于类创建型模式。在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象，这样做的目的是将产品类的实例化操作延迟到工厂子类中完成，即通过工厂子类来确定究竟应该实例化哪一个具体产品类。
## 与简单工厂模式的对比
以计算器为例
### 简单工厂模式
    public class OperationFactory
    {
    	public static operation createOperate(string operate)
    	{
    	Operation oper = null;
    	switch (operate)
    	{
    		case "+":
    			oper = new OperationAdd();
    			break;
    		case "-":
    			oper = new OperationSub();
    			break;
    		case "*":
    			oper = new OperationMul();
    			break;
    		case "/":
    			oper = new OperationDiv();
    			break;
    	}
    	return oper;
    	}
    }
    
客户端代码

    Operation oper;
    oper = OperationFactory.createOperate("+");
    oper.NumberA = 1;
    oper.NumberB = 2;
    double result = oper.GetResult();
    
#### 工厂方法模式

```
interface IFactory
{
	Operation CreateOperation();
}
```

然后加减乘除各建一个具体工厂去实现这个接口。

```
class addFactory: IFactory
{
	public Operation CreateOperation()
	{
		return new OperationAdd();
	}
}

class subFactory: IFactory
{
	public Operation CreateOperation()
	{
		return new OperationSub();
	}
}

class mulFactory: IFactory
{
	public Operation CreateOperation()
	{
		return new OperationMul();
	}
}

class divFactory: IFactory
{
	public Operation CreateOperation()
	{
		return new OperationDiv();
	}
}

```
客户端的实现是这样的。
```
IFactory operFactory = new addFactory();
operation oper = operationFactory.CreateOperation();
oper.NumberA = 1;
oper.NumberB = 2;
double result = oper.GetResult();
```
***简单工厂模式的最大优点在于工厂类中包涵了必要的逻辑判断，根据客户端的选择条件动态实例化相关的类，对于客户端来说，去除了与具体相关产品的依赖。***就像你的计算器，让客户端不用管该用哪个类的实例，只需要把'+'给工厂，工厂自动就给出了相应的实例，客户端只要去做运算就可以了，不同的实例会实现不同的运算。但是问题也就在这里，如你所说，如果要加一个'求M数的N次方'的功能，我们是一定需要给运算工厂类的方法里加'case'的分支条件的，修改原来的类并不是好方法，这就等于说，我们不但对扩展开放了，对修改也开放了，这样违背了开放封闭原则。

工厂方法模式，定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。
其实你仔细观察会发现，工厂方法模式实现时，客户端需要决定实例化哪一个工厂来实现运算类，选择判断的问题还是存在的，也就是说，工厂方法把简单工厂的内部逻辑判断移到了客户端代码来运行，你想要加功能，本来是修改工厂类的，而现在修改客户端。





