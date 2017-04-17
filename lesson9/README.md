# 代理模式(Proxy pattern)
## 简介
代理模式（英语：Proxy Pattern）是程序设计中的一种设计模式。

所谓的代理者是指一个类可以作为其它东西的接口。代理者可以作任何东西的接口：网络连接、内存中的大对象、文件或其它昂贵或无法复制的资源。

著名的代理模式例子为引用计数（英语：reference counting）指针对象。

当一个复杂对象的多份副本须存在时，代理模式可以结合享元模式以减少内存用量。典型作法是创建一个复杂对象及多个代理者，每个代理者会引用到原本的复杂对象。而作用在代理者的运算会转送到原本对象。一旦所有的代理者都不存在时，复杂对象会被移除。

***

在某些情况下，一个客户不想或者不能直接引用一个对象，此时可以通过一个称之为“代理”的第三者来实现 间接引用。代理对象可以在客户端和目标对象之间起到 中介的作用，并且可以通过代理对象去掉客户不能看到 的内容和服务或者添加客户需要的额外服务。

通过引入一个新的对象（如小图片和远程代理 对象）来实现对真实对象的操作或者将新的对 象作为真实对象的一个替身，这种实现机制即 为代理模式，通过引入代理对象来间接访问一 个对象，这就是代理模式的模式动机。

## 讲解

```
#include <iostream>
#include "RealSubject.h"
#include "Proxy.h"

using namespace std;

int main(int argc, char *argv[])
{
	Proxy proxy;
	proxy.request();
	
	return 0;
}
```

```
///////////////////////////////////////////////////////////
//  Proxy.h
//  Implementation of the Class Proxy
//  Created on:      07-十月-2014 16:57:54
//  Original author: colin
///////////////////////////////////////////////////////////

#if !defined(EA_56011290_0413_40c6_9132_63EE89B023FD__INCLUDED_)
#define EA_56011290_0413_40c6_9132_63EE89B023FD__INCLUDED_

#include "RealSubject.h"
#include "Subject.h"

class Proxy : public Subject
{

public:
	Proxy();
	virtual ~Proxy();

	void request();

private:
	void afterRequest();
	void preRequest();	
	RealSubject *m_pRealSubject;

};
#endif // !defined(EA_56011290_0413_40c6_9132_63EE89B023FD__INCLUDED_)
```

```
///////////////////////////////////////////////////////////
//  Proxy.cpp
//  Implementation of the Class Proxy
//  Created on:      07-十月-2014 16:57:54
//  Original author: colin
///////////////////////////////////////////////////////////

#include "Proxy.h"
#include <iostream>
using namespace std;


Proxy::Proxy(){
	//有人觉得 RealSubject对象的创建应该是在main中实现；我认为RealSubject应该
	//对用户是透明的，用户所面对的接口都是通过代理的；这样才是真正的代理； 
	m_pRealSubject = new RealSubject();
}

Proxy::~Proxy(){
	delete m_pRealSubject;
}

void Proxy::afterRequest(){
	cout << "Proxy::afterRequest" << endl;
}


void Proxy::preRequest(){
	cout << "Proxy::preRequest" << endl;
}


void Proxy::request(){
	preRequest();
	m_pRealSubject->request();
	afterRequest();
}
```

### 优点

* 代理模式能够协调调用者和被调用者，在一定程度上降低了系 统的耦合度。
* 远程代理使得客户端可以访问在远程机器上的对象，远程机器 可能具有更好的计算性能与处理速度，可以快速响应并处理客户端请求。
* 虚拟代理通过使用一个小对象来代表一个大对象，可以减少系 统资源的消耗，对系统进行优化并提高运行速度。
* 保护代理可以控制对真实对象的使用权限。

### 缺点

* 由于在客户端和真实主题之间增加了代理对象，因此 有些类型的代理模式可能会造成请求的处理速度变慢。
* 实现代理模式需要额外的工作，有些代理模式的实现 非常复杂。

### 适用环境
根据代理模式的使用目的，常见的代理模式有以下几种类型：

* 远程(Remote)代理：为一个位于不同的地址空间的对象提供一个本地 的代理对象，这个不同的地址空间可以是在同一台主机中，也可是在 另一台主机中，远程代理又叫做大使(Ambassador)。
* 虚拟(Virtual)代理：如果需要创建一个资源消耗较大的对象，先创建一个消耗相对较小的对象来表示，真实对象只在需要时才会被真正创建。
* Copy-on-Write代理：它是虚拟代理的一种，把复制（克隆）操作延迟 到只有在客户端真正需要时才执行。一般来说，对象的深克隆是一个 开销较大的操作，Copy-on-Write代理可以让这个操作延迟，只有对象被用到的时候才被克隆。
* 保护(Protect or Access)代理：控制对一个对象的访问，可以给不同的用户提供不同级别的使用权限。
* 缓冲(Cache)代理：为某一个目标操作的结果提供临时的存储空间，以便多个客户端可以共享这些结果。
* 防火墙(Firewall)代理：保护目标不让恶意用户接近。
* 同步化(Synchronization)代理：使几个用户能够同时使用一个对象而没有冲突。
* 智能引用(Smart Reference)代理：当一个对象被引用时，提供一些额外的操作，如将此对象被调用的次数记录下来等。

## 实例
图片懒加载是一个很典型的代理模式的例子。

```
//抽象日志记录类：抽象主题
interface AbstractLog
{
	public void method();
}
```

```
import java.util.*;

//日志记录代理类：代理主题
class LoggerProxy implements AbstractLog
{
	private BusinessClass business;
	
	public LoggerProxy()
	{
		business = new BusinessClass();
	}
	
	public void method()
	{
		Calendar calendar = new GregorianCalendar();
		int year = calendar.get(Calendar.YEAR);
		int month = calendar.get(Calendar.MONTH) + 1;
		int day = calendar.get(Calendar.DAY_OF_MONTH);
		int hour = calendar.get(Calendar.HOUR) + 12;
		int minute = calendar.get(Calendar.MINUTE);
		int second = calendar.get(Calendar.SECOND);
		String dateTime = year + "-" + month + "-" + day + " " + hour + ":" + minute + ":" + second + "！";
		System.out.println("方法method()被调用，调用时间为" + dateTime);
		try{
			business.method();
			System.out.println("方法method()调用成功！");
		}
		catch(Exception e)
		{
			System.out.println("方法method()调用失败！");
		}
	}
}
```

```
//业务类：真实主题
class BusinessClass implements AbstractLog
{
	public void method()
	{
		System.out.println("真实业务方法！");
	}
}
```

```
//客户端测试类
class Client
{
	public static void main(String args[])
	{
		AbstractLog al;
		al = new LoggerProxy();
		al.method();
	}
}
```

在本实例中，通过代理类LoggerProxy来间接调用真实业务类BusinessClass的方法，可以在调用真实业务方法时增加新功能（如日志记录），此处使用的是代理模式的一种较为简单的形式，类似于保护代理，在实施真实调用时可以执行一些额外的操作。由于代理主题和真实主题实现了相同的接口，因此在客户端可以针对抽象编程，而将具体代理类类名存储至配置文件中，增加和更换代理类和真实类都很方便，无需修改源代码，满足开闭原则。