# 组合模式(Composite Pattern)

## 简介
有个项目，是为一家在全国许多城市都有分销机构的大公司做办公管理系统，总部有人力资源，财务，运营等部门。但是总公司的人力资源部，财务部等办公管理功能在所有的分公司或办事处都需要有。我们可能希望人力资源部，财务部的管理功能可以复用于分公司。这其实就是整体与部分可以被一致对待的问题。

***组合模式:将对象组合成树形结构以表示'部分-整体'的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。***

***当你发现需求中是体现部分与整体层次的结构时，以及你希望用户可以忽略组合对象与单个对象的不同，统一地使用组合结构中的所有对象时，就应该考虑使用组合模式了。***

### 公司管理系统

公司类，抽象类或接口

```
abstract class Company
{
	protected string name;
	
	public Company(string name)
	{
		this.name = name;
	}
	public abstract void Add(Company c);//增加
	public abstract void Remove(Company c);//移除
	public abstract void Display(Company c);//显示	public abstract void LineOfDuty(Company c);//履行职责
}
```

具体公司类 实现接口树枝节点

```
class ConcreteCompany: Company
{
	private List<Company>children = new List<Company>();
	
	public ConcreteCompany(string name)
	{
		: base(name);
	}
	
	public override void Add(Company c)
	{
		children.Add(c);
	}
	public override void Remove(Company c)
	{
		children. Remove(c);
	}
	public override void Display(int depth)
	{
		Console.WriteLine(new string('-', depth) + name);
		
		foreach(Company component in children)
		{
			component.Display(depth + 2);
		}
	}	
	
	public override void LineOfDuty()
	{
		foreach(Company component in children)
		{
			component.LineofDuty();
		}
	}
}
```

人力资源部与财务部类 树叶节点

```
class HRDepartment: Company
{
	public HRDepartment(string name)
	{
		: base(name);
	}

	public override void Add(Company c)
	{}

	public override void Remove(Company c)
	{}
	
	public override void Display(int depth)
	{
		Console.WriteLine(new string('-', depth) + name);
	}
	
	public override void LineOfDuty()
	{
		Console.WriteLine("{0} 员工招聘培训管理"，name);
	}
}
```

财务部

```
class FinanceDepartment: Company
{
	public FinanceDepartment(string name)
	{
		: base(name);
	}

	public override void Add(Company c)
	{}

	public override void Remove(Company c)
	{}
	
	public override void Display(int depth)
	{
		Console.WriteLine(new string('-', depth) + name);
	}
	
	public override void LineOfDuty()
	{
		Console.WriteLine("{0} 公司财务收支管理"，name);
	}
}
```

客户端调用

```
static void Main(string[] args)
{
	ConcreteCompany root = new ConcreteCompany("北京总公司");
	root.Add(new HRDepartment("总公司人力资源部"));
	root.Add(new FinanceDepartment("总公司财务部"));
	
	ConcreteCompany comp = new ConcreteCompany("上海华东分公司");
	comp.Add(new HRDepartment("总公司人力资源部"));
	comp.Add(new FinanceDepartment("总公司财务部"));
	root.Add(comp);
	
		ConcreteCompany comp1 = new ConcreteCompany("南京办事处");
	comp1.Add(new HRDepartment("南京办事处人力资源部"));
	comp1.Add(new FinanceDepartment("南京办事处财务部"));
	comp.Add(comp1);
	
	root.Display(1);
	root.LineOfDuty();
}
```

### 透明方式与安全方式

透明方式，也就是说在Component中声明所有用来管理子对象的方法，其中包括Add,Remove等。这样实现Component接口的所有子类都具备了Add和RFemove。这样做的好处就是叶节点和枝节点对于外界没有区别，它们具备完全一致的行为接口。但是问题也很明显，因为Leaf类本身不具备Add(),Remove()方法的功能，所以实现也是没意义的。

安全方式，也就是在Component接口中不去声明Add和Remove方法，那么字类的Leaf也不需要实现它，而是在Composite声明所有用来管理字类对象的方法。不过由于不透明，所以树枝类和树叶不具有相同的接口，客户端的调用需要做相应的判断，带来了不便。


***组合模式有时候又叫做部分-整体模式，它使我们树型结构的问题中，模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样来处理复杂元素,从而使得客户程序与复杂元素的内部结构解耦。***


