## 代码结构

C#的代码结构如下所示：

```c#
// 命名空间
namespace XXX {
    // 空间中的类
    class Program {
        // 入口方法
        static void Main() {

        }
    }
}
```

1. 命名空间，用于 C#的模块管理，类似于 Java 中的包
2. 类
3. 方法
4. C#与 Java 类似，必须有个 static void 的 Main 方法。

## 变量

声明变量：

```c#
类型 变量名 = 初始值;
```

## 引用类型
### string字符串
在C#中，string基于Object类，也是对象的一种：
```c#
string str = "Hello World";
```

不同于其他语言，C#中多行字符串是在""添加@字符：
```c#
string str = @"<script type=""text/javascript"">
        <!--
        -->
    </script>";
```

#### 字符串格式
在C#中，提供了如下的方式来处理字符串格式的问题：
```c#
string str2 = String.Format("我是{0}, 学习C#{1}", "timotte", 2);
int a = 2;
int b = 3;
int c = 4;
string str3 = $"{a}\t{b}\t{c}";
```

其中$是字符串内插技术，第二个是利用编号来插入对应的内容。

### dynamic
dynamic关键字用于动态类型，在运行时进行校验：
```c#
dynamic num = 2;
num = "sasda";
```

### 类型转换
C#提供了隐式转换与显示转换两种方式：
```c#
byte b = 10;
int i = b; // 隐式转换
```

注意，对于强制转换可能会精度丢失的问题：
```c#
int i = 1000;
byte b = (byte) i;
Console.WriteLine(b); // 232
```

除此之外，C#提供了Convert类来处理转换：
```c#
Console.WriteLine(Convert.ToByte(i));
```
注意，Convert的转换可能会因为过大等原因抛出异常。

对于其他类型转换成华为字符串，每种类型都有toString方法。

### const常量
在c#中，使用const修饰常量：
```c#
using System;

public class ConstTest
{
    class SampleClass
    {
        public int x;
        public int y;
        public const int c1 = 5;
        public const int c2 = c1 + 5;

        public SampleClass(int p1, int p2)
        {
            x = p1;
            y = p2;
        }
    }

    static void Main()
    {
        SampleClass mC = new SampleClass(11, 22);
        Console.WriteLine("x = {0}, y = {1}", mC.x, mC.y);
        Console.WriteLine("c1 = {0}, c2 = {1}",
                          SampleClass.c1, SampleClass.c2);
    }
}
```