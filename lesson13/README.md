# 外观模式(Facade pattern)
## 简介
外观模式（Facade pattern），是软件工程中常用的一种软件设计模式，它为子系统中的一组接口提供一个统一的高层接口，使得子系统更容易使用。

## 模式实现
某软件公司欲开发一个可应用于多个软件的文件加密模块，该模块可以对文件中的数据进行加密并将加密之后的数据存储在一个新文件中，具体的流程包括三个部分，分别是读取源文件、加密、保存加密之后的文件，其中，读取文件和保存文件使用流来实现，加密操作通过求模运算实现。这三个操作相对独立，为了实现代码的独立重用，让设计更符合单一职责原则，这三个操作的业务代码封装在三个不同的类中。
现使用外观模式设计该文件加密模块。

(1) FileReader：文件读取类，充当子系统类。

```
//FileReader.cs  
using System;  
using System.Text;  
using System.IO;  

namespace FacadeSample  
{  
    class FileReader  
    {  
        public string Read(string fileNameSrc)
        {  
       Console.Write("读取文件，获取明文：");  
            FileStream fs = null;  
            StringBuilder sb = new StringBuilder();  
       try  
            {  
                fs = new FileStream(fileNameSrc, FileMode.Open);  
                int data;  
               while((data = fs.ReadByte())!= -1)
                {  
            sb = sb.Append((char)data);  
               }  
               fs.Close();  
               Console.WriteLine(sb.ToString());  
       }  
       catch(FileNotFoundException e)
            {  
           Console.WriteLine("文件不存在！");  
       }  
       catch(IOException e)
            {  
           Console.WriteLine("文件操作错误！");  
       }  
       return sb.ToString();  
        }  
    }  
}
```
(2) CipherMachine：数据加密类，充当子系统类。

```
//CipherMachine.cs  
using System;  
using System.Text;  

namespace FacadeSample  
{  
    class CipherMachine  
    {  
       public string Encrypt(string plainText)
       {  
       Console.Write("数据加密，将明文转换为密文：");  
       string es = "";  
            char[] chars = plainText.ToCharArray();  
       foreach(char ch in chars)
            {  
                string c = (ch % 7).ToString();  
           es += c;  
       }  
            Console.WriteLine(es);  
       return es;  
    }  
    }  
}
```

(3) FileWriter：文件保存类，充当子系统类。

```
//FileWriter.cs  
using System;  
using System.IO;  
using System.Text;  

namespace FacadeSample  
{  
    class FileWriter  
    {  
        public void Write(string encryptStr,string fileNameDes)
        {  
       Console.WriteLine("保存密文，写入文件。");  
            FileStream fs = null;  
       try  
            {  
               fs = new FileStream(fileNameDes, FileMode.Create);  
                byte[] str = Encoding.Default.GetBytes(encryptStr);  
                fs.Write(str,0,str.Length);  
                fs.Flush();  
               fs.Close();  
       }
       catch(FileNotFoundException e)
            {  
        Console.WriteLine("文件不存在！");  
       }  
       catch(IOException e)
            {  
                Console.WriteLine(e.Message);  
           Console.WriteLine("文件操作错误！");  
       }
        }  
    }  
}
```

(4) EncryptFacade：加密外观类，充当外观类。

```
// EncryptFacade.cs  
namespace FacadeSample  
{  
    class EncryptFacade  
    {  
        //维持对其他对象的引用  
         private FileReader reader;  
        private CipherMachine cipher;  
        private FileWriter writer;  

        public EncryptFacade()  
        {  
            reader = new FileReader();  
            cipher = new CipherMachine();  
            writer = new FileWriter();  
        }  

        //调用其他对象的业务方法  
         public void FileEncrypt(string fileNameSrc, string fileNameDes)  
        {  
            string plainStr = reader.Read(fileNameSrc);  
            string encryptStr = cipher.Encrypt(plainStr);  
            writer.Write(encryptStr, fileNameDes);  
        }  
    }  
}
```

(5) Program：客户端测试类

```
//Program.cs  
using System;  

namespace FacadeSample  
{  
    class Program  
    {  
        static void Main(string[] args)  
        {  
            EncryptFacade ef = new EncryptFacade();  
            ef.FileEncrypt("src.txt", "des.txt");  
            Console.Read();  
        }  
    }  
}
```
### 结果及分析

```
读取文件，获取明文：Hello world!
数据加密，将明文转换为密文：233364062325
保存密文，写入文件。
```


## 抽象外观类
在标准的外观模式结构图中，如果需要增加、删除或更换与外观类交互的子系统类，必须修改外观类或客户端的源代码，这将违背开闭原则，因此可以通过引入抽象外观类来对系统进行改进，在一定程度上可以解决该问题。在引入抽象外观类之后，客户端可以针对抽象外观类进行编程，对于新的业务需求，不需要修改原有外观类，而对应增加一个新的具体外观类，由新的具体外观类来关联新的子系统对象，同时通过修改配置文件来达到不修改任何源代码并更换外观类的目的。

下面通过一个具体实例来学习如何使用抽象外观类：

如果在应用实例“文件加密模块”中需要更换一个加密类，不再使用原有的基于求模运算的加密类CipherMachine，而改为基于移位运算的新加密类NewCipherMachine，其代码如下：

```
using System;  

namespace FacadeSample  
{  
    class NewCipherMachine  
    {  
        public string Encrypt(string plainText)
        {  
            Console.Write("数据加密，将明文转换为密文：");  
            string es = "";  
            int key = 10;//设置密钥，移位数为10  
            char[] chars = plainText.ToCharArray();  
            foreach(char ch in chars)
            {  
                int temp = Convert.ToInt32(ch);  
                //小写字母移位  
                if (ch >= 'a' && ch <= 'z') {  
                    temp += key % 26;  
                    if (temp > 122) temp -= 26;  
                    if (temp < 97) temp += 26;  
                }  
                //大写字母移位  
                if (ch >= 'A' && ch <= 'Z') {  
                    temp += key % 26;  
                    if (temp > 90) temp -= 26;  
                    if (temp < 65) temp += 26;  
                }  
                es += ((char)temp).ToString();  
            }  
            Console.WriteLine(es);  
            return es;  
        }  
    }  
}
```

如果不增加新的外观类，只能通过修改原有外观类EncryptFacade的源代码来实现加密类的更换，将原有的对CipherMachine类型对象的引用改为对NewCipherMachine类型对象的引用，这违背了开闭原则，因此需要通过增加新的外观类来实现对子系统对象引用的改变。

如果增加一个新的外观类NewEncryptFacade来与FileReader类、FileWriter类以及新增加的NewCipherMachine类进行交互，虽然原有系统类库无须做任何修改，但是因为客户端代码中原来针对EncryptFacade类进行编程，现在需要改为NewEncryptFacade类，因此需要修改客户端源代码。

如何在不修改客户端代码的前提下使用新的外观类呢？解决方法之一是：引入一个抽象外观类，客户端针对抽象外观类编程，而在运行时再确定具体外观类，引入抽象外观类之后的文件加密模块结构图如图5所示：


客户类Client针对抽象外观类AbstractEncryptFacade进行编程，AbstractEncryptFacade代码如下：

```
namespace FacadeSample  
{  
    abstract class AbstractEncryptFacade  
    {  
        public abstract void FileEncrypt(string fileNameSrc, string fileNameDes);  
    }  
}
```

新增具体加密外观类NewEncryptFacade代码如下：

```
namespace FacadeSample  
{  
    class NewEncryptFacade : AbstractEncryptFacade  
    {  
        private FileReader reader;  
        private NewCipherMachine cipher;  
        private FileWriter writer;  

        public NewEncryptFacade()  
        {  
            reader = new FileReader();  
            cipher = new NewCipherMachine();  
            writer = new FileWriter();  
        }  

        public override void FileEncrypt(string fileNameSrc, string fileNameDes)  
        {  
            string plainStr = reader.Read(fileNameSrc);  
            string encryptStr = cipher.Encrypt(plainStr);  
            writer.Write(encryptStr, fileNameDes);  
        }  
    }  
}
```

```
配置文件App.config中存储了具体外观类的类名，代码如下：
<?xml version="1.0" encoding="utf-8" ?>  
<configuration>  
  <appSettings>  
    <add key="facade" value="FacadeSample.NewEncryptFacade"/>  
  </appSettings>  
</configuration>
```


客户端测试代码修改如下：

```
using System;  
using System.Configuration;  
using System.Reflection;  

namespace FacadeSample  
{  
    class Program  
    {  
        static void Main(string[] args)  
        {  
            AbstractEncryptFacade ef; //针对抽象外观类编程  
            //读取配置文件  
            string facadeString = ConfigurationManager.AppSettings["facade"];  
            //反射生成对象  
            ef = (AbstractEncryptFacade)Assembly.Load("FacadeSample"). CreateInstance (facadeString);  
            ef.FileEncrypt("src.txt", "des.txt");  
            Console.Read();  
        }  
    }  
}
```

## 模式适用场景
在以下情况下可以考虑使用外观模式：

1. 当要为访问一系列复杂的子系统提供一个简单入口时可以使用外观模式。
2. 客户端程序与多个子系统之间存在很大的依赖性。引入外观类可以将子系统与客户端解耦，从而提高子系统的独立性和可移植性。
3. 在层次化结构中，可以使用外观模式定义系统中每一层的入口，层与层之间不直接产生联系，而通过外观类建立联系，降低层之间的耦合度。


