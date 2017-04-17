# 解释器模式(Interpreter Pattern)

## 简介
解释器模式:给定一个语言，定义它的语法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

解释器模式需要解决的是，如果一种特定类型的问题发生的频率足够高，那么可能就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。

比如说，我们常常会在字符串中搜索匹配字符或判断一个字符串是否符合我们规定的格式，此时一般我们会用正则表达式匹配。解释器为正则表达式定义了一个文法，如何表示一个特定的正则表达式，以及如何解释这个正则表达式。

***当有一个语言需要解释执行，并且你可以将该语言中的句子表示为一个抽象语法树时，可以使用解释器模式。***

用了解释器模式，就意味着可以很容易地改变和扩展文法，因为该模式使用类来表示文法规则，你可以使用继承来改变或者扩展该文法。也比较容易实现文法，因为定义抽象语法树中各个节点的类的实现大体类似，这些类都易于直接编写。

***解释器模式也有不足的，解释器模式为文法中的每一条规则至少定义了一个类，因此包含了许多规则的文法可能难以管理和维护。建议当文法非常复杂时，使用其它的技术如语法分析程序或编译器生成器来处理。***


## 实例
### 音乐解释器


演奏内容类(context)
```
//演奏内容
class PlayContext
{
	//演奏文本
	private string text;
	public string PlayText
	{
		get {return text;}
		set {text = value;}
	}
}
```

表达式类(AbstractExpression)

```
abstract class Expression
{
	//解释器
	public void Interpret(PlayContext context)
	{
		if (context.PlayText.Length == 0)
		{
			return;
		}
		else
		{
			//此方法用于将当前演奏文本第一条命令获得命令字母和其参数值。例如"O 3 E 0.5 G 0.5 A 3"则playKey为O而playValue为3
			string playKey = context.Playtext.subText.subString(0,1);
			context.PlayText = context. PlayText.subString(2);
			doublePlayValue = Convert.ToDouble(context.PlayText.SubString(0, context.PlayText.IndexOf("")));
			context.PlayText = context.PlayText.substring(context.PlayText.IndexOf("") + 1);
			
			Excute(playKey, playValue);
		}
	}
	//执行
	public abstract void Excute(string key, doubel value);
}
```

音符类(TerminalExpression)

```
class Note: Expression
{
	public override void Excute(string key, double value)
	{
		string note ="";
		switch (key)
		{
			case: "C":
				note = "1";
				break;
			case: "D":
				note = "2";
				break;
			case: "E":
				note = "3";
				break;
			case: "F":
				note = "4";
				break;
			case: "G":
				note = "5";
				break;
			case: "A":
				note = "6";
				break;
			case: "B":
				note = "7";
				break;
		}
		Console.Write("{0}", note);
	}
}
```

音符类(TerminalExpression)

```
class Scale: Expression
{
	public override void Excute(string key, double value)
	{
		string scale = "";
		switch (Convert.ToInt32(value))
		{
			case 1:
				scale = "低音";
				break;
			case 2:
				scale = "中音";
				break;
			case 3:
				scale = "高音";	
				break;
		}
		Console.Write("{0}",scale);
	}
}
```

客户端代码

```
static void Main(string[] args)
{
	PlayContext context = new PlayContext();
	//音乐-上海滩
	Console.WriteLine("上海滩:");
	context.PlayText = "O 2 E 0.5 G 0.5 A 3 E 0.5 G 0.5 D 3 E 0.5";
	Expression expression = null;
	try
	{
		while (context.PlayText.Length > 0)
		{
			string str = context.PlayText.Substring(0.1);
			switch (str)
			{
				case "O":
					expression = new Scale();
					break;
				case "C":
				case "D":
				case "E":
				case "F":
				case "G":
				case "A":
				case "A":
				case "B":
				case "P":
					expression = new Note();
					break;
			}
			expression.Interpret(context);
			
		}
	}
	catch (Exception ex)
	{
		Console.WriteLine(ex.Message);
	}
	Console.Read();
}
```

比如说 我现在要增加一个文法，就是演奏速度。

音速类

```
class Speed :Expression
{
	public override void Excute(string key, double value)
	{
		string speed;
		if (value < 500) {
			speed = "快速";
		} else if (value >= 1000)
		{
			speed = "慢速";
		} else {
			speed = "快速";
		}
		Console.Write("{0}",speed);
	}
}
```

客户端代码(局部)

```
	context.PlayText = "T 500 O 2 E 0.5 G 0.5 A 3 E 0.5 G 0.5 D 3 E 0.5";
	Expression expression = null;
	try
	{
		while (context.PlayText.Length > 0)
		{
			string str = context.PlayText.Substring(0.1);
			switch (str)
			{
				case "O":
					expression = new Scale();
					break;
					case "T":
					expression = new Speed();
					break;
				case "C":
				case "D":
				case "E":
				case "F":
				case "G":
				case "A":
				case "A":
				case "B":
				case "P":
					expression = new Note();
					break;
			}
			expression.Interpret(context);
			
		}
	}
	catch (Exception ex)
	{
		Console.WriteLine(ex.Message);
	}
	Console.Read();
}
```
