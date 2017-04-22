# 模版方法模式(Template method pattern)
## 简介
模板方法模式定义了一个算法的步骤，并允许子类别为一个或多个步骤提供其实践方式。让子类别在不改变算法架构的情况下，重新定义算法中的某些步骤。在软件工程中，它是一种软件设计模式，和C++模板没有关连。

## 实例
 事实上，模版方法是编程中一个经常用到的模式。先来看一个例子，某日，程序员A拿到一个任务：给定一个整数数组，把数组中的数由小到大排序，然后把排序之后的结果打印出来。经过分析之后，这个任务大体上可分为两部分，排序和打印，打印功能好实现，排序就有点麻烦了。但是A有办法，先把打印功能完成，排序功能另找人做。
 
 ```
 abstract class AbstractSort {  
      
    /** 
     * 将数组array由小到大排序 
     * @param array 
     */  
    protected abstract void sort(int[] array);  
      
    public void showSortResult(int[] array){  
        this.sort(array);  
        System.out.print("排序结果：");  
        for (int i = 0; i < array.length; i++){  
            System.out.printf("%3s", array[i]);  
        }  
    }  
}  
 ```
 
写完后，A找到刚毕业入职不久的同事B说：有个任务，主要逻辑我已经写好了，你把剩下的逻辑实现一下吧。于是把AbstractSort类给B，让B写实现。B拿过来一看，太简单了，10分钟搞定，代码如下：

```
class ConcreteSort extends AbstractSort {  
  
    @Override  
    protected void sort(int[] array){  
        for(int i=0; i<array.length-1; i++){  
            selectSort(array, i);  
        }  
    }  
      
    private void selectSort(int[] array, int index) {  
        int MinValue = 32767; // 最小值变量  
        int indexMin = 0; // 最小值索引变量  
        int Temp; // 暂存变量  
        for (int i = index; i < array.length; i++) {  
            if (array[i] < MinValue){ // 找到最小值  
                MinValue = array[i]; // 储存最小值  
                indexMin = i;   
            }  
        }  
        Temp = array[index]; // 交换两数值  
        array[index] = array[indexMin];  
        array[indexMin] = Temp;  
    }  
}  
``` 
写好后交给A，A拿来一运行：

```
public class Client {  
    public static int[] a = { 10, 32, 1, 9, 5, 7, 12, 0, 4, 3 }; // 预设数据数组  
    public static void main(String[] args){  
        AbstractSort s = new ConcreteSort();  
        s.showSortResult(a);  
    }  
}  
```

排序结果：  0  1  3  4  5  7  9 10 12 32

运行正常。行了，任务完成。没错，这就是模版方法模式。大部分刚步入职场的毕业生应该都有类似B的经历。一个复杂的任务，由公司中的牛人们将主要的逻辑写好，然后把那些看上去比较简单的方法写成抽象的，交给其他的同事去开发。这种分工方式在编程人员水平层次比较明显的公司中经常用到。比如一个项目组，有架构师，高级工程师，初级工程师，则一般由架构师使用大量的接口、抽象类将整个系统的逻辑串起来，实现的编码则根据难度的不同分别交给高级工程师和初级工程师来完成。怎么样，是不是用到过模版方法模式？

### 模版方法的优点及适用场景
`AbstractClass`是一个抽象类，其实也就是一个抽象模版，定义并实现一个模版的方法。这个模版的方法一般是一个具体的方法,它给出了一个顶级逻辑的骨架，而逻辑组成步骤在相应的抽象操作中，推迟到子类实现。顶级逻辑也有可能调用一些具体方法。

```
abstract class AbstractClass
{
	public abstract void PrimitiveOperation1();
	public abstract void PrimitiveOperation2();
	
	public void TemplateMethod()
	{
		PrimitiveOperation1();
		PrimitiveOperation2();
		console.WriteLine("");
	}
}
```

`ConcreteClass`实现夫类所定义的一个或者多个抽象方法。每一个`AbstractClass`都可以有任意多个`ConcreteClass`与之对应，而每一个`ConcreteClass`都可以给出这些抽象方法(也就是顶级逻辑的组成步骤)的不同实现，从而使得顶级逻辑的实现各不相同。

```
class ConcreteClassA: AbstractClass
{
	public override void PrimitiveOperation1()
	{
		Console.writeLine("具体类A方法1实现");
	}
		public override void PrimitiveOperation2()
	{
		Console.writeLine("具体类A方法2实现");
	}
}

class ConcreteClassB: AbstractClass
{
	public override void PrimitiveOperation1()
	{
		Console.writeLine("具体类B方法1实现");
	}
		public override void PrimitiveOperation2()
	{
		Console.writeLine("具体类B方法2实现");
	}
}

```

客户端调用

```
static void Main(string[] args)
{
	AbstractClass c;
	c = new ConcreteClassA();
	c.TemplateMethod();
	
	c = new ConcreteClassB();
	c.TemplateMethod();
	
	Console.Read();
}
```

***模版方法模式是通过把不变的行为搬移到超类，去除子类中的重复代码来体现它的优势。当不变的和可变的行为在方法的子类实现中混合在一起的时候，不变的行为就会在子类中重复出现。我们通过模版方法模式把这些行为版已到单一的地方，这样就帮助子类摆脱重复的不变的行为的纠缠。***