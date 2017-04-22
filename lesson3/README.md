# 面向对象六大原则

## 概述
在工作初期，我们可能会经常会有这样的感觉，自己的代码接口设计混乱、代码耦合较为严重、一个类的代码过多等等，自己回头看的时候都觉得汗颜。再看那些知名的开源库，它们大多有着整洁的代码、清晰简单的接口、职责单一的类，这个时候我们通常会捶胸顿足而感叹：什么时候老夫才能写出这样的代码！


## 相关资料

### iOS
[AFNetworking2.0源码解析](http://blog.cnbang.net/tech/2320/)

[AFNetworking源码](https://github.com/AFNetworking/AFNetworking)

## 单一原则(Single Responsibility Principle)
### 简述
SRP:就一个类而言，应该仅有一个引起它变化的原因。
单一职责的划分界限并不是如马路上的行车道那么清晰，很多时候都是需要靠个人经验来界定。当然最大的问题就是对职责的定义，什么是类的职责，以及怎么划分类的职责。**如果一个类承担的职责过多，就等于把这些职责耦合在一起，一个职责的变化可能会削弱或者抑制这个类完成其他职责的能力。这种耦合会导致脆弱的设计，当变化发生时，设计会遭受到意想不到的破坏**。

**当然，软件设计真正要做的许多内容，就是发现职责并把那些职责相互分离，就是抽象的能力。其实要去判断是否应该分离出类来，也不难，那就是如果你能想到多于一个的动机去改变一个类，那么这个类就具有多于一个的职责**。

### 示例
    /**
    * An HTTP stack abstraction.
    */
    public interface HttpStack {
     /**
     * 执行Http请求,并且返回一个HttpResponse
     */ 
    public HttpResponse performRequest(Request<?> request,         Map<String, String> additionalHeaders)
        throws IOException, AuthFailureError;
    }
    
HttpStack中这个函数的职责就是执行网络请求并且返回一个Response。它的职责很单一，这样在需要修改执行网络请求的相关代码时我们只需要修改实现HttpStack接口的类，而不会影响其它的类的代码。如果某个类的职责包含有执行网络请求、解析网络请求、进行gzip压缩、封装请求参数等等，那么在你修改某处代码时你就必须谨慎，以免修改的代码影响了其它的功能。但是当职责单一的时候，你修改的代码能够基本上不影响其它的功能。这就在一定程度上保证了代码的可维护性。**注意，单一职责原则并不是说一个类只有一个函数，而是说这个类中的函数所做的工作必须要是高度相关的，也就是高内聚**。

## 里氏替换原则(Liskov Substitution Principle)
### 简述
肯定有不少人跟我刚看到这项原则的时候一样，对这个原则的名字充满疑惑。其实原因就是这项原则最早是在1988年，由麻省理工学院的一位姓里的女士（Barbara Liskov）提出来的。

定义1：如果对每一个类型为 T1的对象 o1，都有类型为 T2 的对象o2，使得以 T1定义的所有程序 P 在所有的对象 o1 都代换成 o2 时，程序 P 的行为没有发生变化，那么类型 T2 是类型 T1 的子类型。

定义2：所有引用基类的地方必须能透明地使用其子类的对象。

问题由来：有一功能P1，由类A完成。现需要将功能P1进行扩展，扩展后的功能为P，其中P由原有功能P1与新功能P2组成。新功能P由类A的子类B来完成，则子类B在完成新功能P2的同时，有可能会导致原有功能P1发生故障。

解决方案：当使用继承时，遵循里氏替换原则。类B继承类A时，除添加新的方法完成新增功能P2外，尽量不要重写父类A的方法，也尽量不要重载父类A的方法。

继承包含这样一层含义：**父类中凡是已经实现好的方法（相对于抽象方法而言），实际上是在设定一系列的规范和契约，虽然它不强制要求所有的子类必须遵从这些契约，但是如果子类对这些非抽象方法任意修改，就会对整个继承体系造成破坏。而里氏替换原则就是表达了这一层含义**。

**继承作为面向对象三大特性之一，在给程序设计带来巨大便利的同时，也带来了弊端。比如使用继承会给程序带来侵入性，程序的可移植性降低，增加了对象间的耦合性，如果一个类被其他的类所继承，则当这个类需要修改时，必须考虑到所有的子类，并且父类修改后，所有涉及到子类的功能都有可能会产生故障**。

里氏替换原则通俗的来讲就是：子类可以扩展父类的功能，但不能改变父类原有的功能。它包含以下4层含义：

* 子类可以实现父类的抽象方法，但不能覆盖父类的非抽象方法。
* 子类中可以增加自己特有的方法。
* 当子类的方法重载父类的方法时，方法的前置条件（即方法的形参）要比父类方法的输入参数更宽松。
* 当子类的方法实现父类的抽象方法时，方法的后置条件（即方法的返回值）要比父类更严格。

### 示例

还是以HttpStack为例，Volley定义了HttpStack来表示执行网络请求这个抽象概念。在执行网络请求时，我们只需要定义一个HttpStack对象，然后调用performRequest即可。至于HttpStack的具体实现由更高层的调用者给出。示例如下 :
```
  public static RequestQueue newRequestQueue(Context context, HttpStack stack) {
      File cacheDir = new File(context.getCacheDir(), DEFAULT_CACHE_DIR);
      String userAgent = "volley/0";
		// 代码省略
// 1、构造HttpStack对象
      if (stack == null) {
          if (Build.VERSION.SDK_INT >= 9) {
              stack = new HurlStack();
          } else {
              // Prior to Gingerbread, HttpUrlConnection was unreliable.
              // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
              stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
          }
      }
// 2、将HttpStack对象传递给Network对象
      Network network = new BasicNetwork(stack);
// 3、将network对象传递给网络请求队列
      RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
      queue.start();
      return queue;
  }
```
     
 BasicNetwork的代码如下:

```
/**
 * A network performing Volley requests over an {@link HttpStack}.
 */
public class BasicNetwork implements Network {
	// HttpStack抽象对象
    protected final HttpStack mHttpStack;
    protected final ByteArrayPool mPool;
    public BasicNetwork(HttpStack httpStack) {
        this(httpStack, new ByteArrayPool(DEFAULT_POOL_SIZE));
    }
    
    
    public BasicNetwork(HttpStack httpStack, ByteArrayPool pool) {
        mHttpStack = httpStack;
        mPool = pool;
    }
}
```
上述代码中，BasicNetwork构造函数依赖的是HttpStack抽象接口，任何实现了HttpStack接口的类型都可以作为参数传递给BasicNetwork用以执行网络请求。这就是所谓的里氏替换原则，任何父类出现的地方子类都可以出现，这不就保证了可扩展性吗？

### 减少LSP妨碍
#### 契约（Contracts）

处理 LSP 过分妨碍的一个策略是使用契约，契约清单有 2 种形式：执行说明书（executable specifications）和错误处理，在执行说明书里，一个详细类库的契约也包括一组自动化测试，而错误处理是在代码里直接处理的，例如在前置条件，后置条件，常量检查等，可以从 Bertrand Miller 的大作《契约设计》中查看这个技术。虽然自动化测试和契约设计不在本篇文字的范围内，但当我们用的时候我还是推荐如下内容：

检查使用测试驱动开发（Test-Driven Development）来指导你代码的设计
设计可重用类库的时候可随意使用契约设计技术
对于你自己要维护和实现的代码，使用契约设计趋向于添加很多不必要的代码，如果你要控制输入，添加测试是非常有必要的，如果你是类库作者，使用契约设计，你要注意不正确的使用方法以及让你的用户使之作为一个测试工具。
契约（Contracts）

处理 LSP 过分妨碍的一个策略是使用契约，契约清单有 2 种形式：执行说明书（executable specifications）和错误处理，在执行说明书里，一个详细类库的契约也包括一组自动化测试，而错误处理是在代码里直接处理的，例如在前置条件，后置条件，常量检查等，可以从 Bertrand Miller 的大作《契约设计》中查看这个技术。虽然自动化测试和契约设计不在本篇文字的范围内，但当我们用的时候我还是推荐如下内容：

检查使用测试驱动开发（Test-Driven Development）来指导你代码的设计
设计可重用类库的时候可随意使用契约设计技术
对于你自己要维护和实现的代码，使用契约设计趋向于添加很多不必要的代码，如果你要控制输入，添加测试是非常有必要的，如果你是类库作者，使用契约设计，你要注意不正确的使用方法以及让你的用户使之作为一个测试工具。

#### 避免继承

避免 LSP 妨碍的另外一个测试是：如果可能的话，尽量不用继承，在Gamma的大作《Design Patterns – Elements of Reusable Object-Orineted Software》中，我们可以看到如下建议：

Favor object composition over class inheritance

尽量使用对象组合而不是类继承
有些书里讨论了组合比继承好的唯一作用是静态类型，基于类的语言（例如，在运行时可以改变行为），与 JavaScript 相关的一个问题是耦合，当使用继承的时候，继承子类型和他们的基类型耦合在一起了，就是说基类型的改变会影响到继承子类型。组合倾向于对象更小化，更容易向静态和动态语言语言维护。

与行为有关，而不是继承
到现在，我们讨论了和继承上下文在内的里氏替换原则，指示出 JavaScript 的面向对象实。不过，里氏替换原则（LSP）的本质不是真的和继承有关，而是行为兼容性。JavaScript 是一个动态语言，一个对象的契约行为不是对象的类型决定的，而是对象期望的功能决定的。里氏替换原则的初始构想是作为继承的一个原则指南，等价于对象设计中的隐式接口。

