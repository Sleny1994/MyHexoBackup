---
title: C Sharp高级进阶
date: 2023-08-14
tags: 
- CSharp
categories: 
- CSharp
---
## 特性（Attribute）
定义：用于在运行时传递程序中各种元素（比如类、方法、结构、枚举、组件等）的行为信息的声明标签。一个声明性标签是通过放置在它所应用的元素前面的方括号([])来描述的。
作用：特性用于添加元数据，如编译器指令和注释、描述、方法、类等其他信息。
分类：预定义特性和自定义特性。
### 语法
```c#
[attribute(positional_parameters, name_parameter = value, ...)]
element
// 特性的名称和值是在方括号内规定的，放置在它所应用的元素之前
// positional_parameters 规定必需的信息，name_parameter 规定可选的信息
```
### 预定义特性
.NET框架提供了三种预定义特性：
- AttributeUsage：描述了如何使用一个自定义特性类，规定了可应用到的项目类型
```c#
[AttributeUsage(
   validon,
   AllowMultiple=allowmultiple,
   Inherited=inherited
)]

// validon - 规定特性可被放置的语言元素，是枚举器AttributeTargets的值的组合，默认值：AttributeTargets.All
// allowmultiple - 为特性的AllowMultiple属性提供一个布尔值，若为true，则该特性是多用的，默认为false（单用的）
// inherited - 为特性的Inherited属性提供一个布尔值，若为true，则该特性可被派生类继承，默认是false
e.g:
[AttributeUsage(AttributeTargets.Class |
AttributeTargets.Constructor |
AttributeTargets.Field |
AttributeTargets.Method |
AttributeTargets.Property, 
AllowMultiple = true)]
```
- Conditional：标记了一个条件方法，其执行依赖于指定的预处理标识符，会引起方法调用的条件编译，取决于指定的值，比如Debug或Trace
```c#
[Conditional(
   conditionalSymbol
)]

e.g:
[Conditional("DEBUG")]
// ---------------------------
#define DEBUG
using System;
using System.Diagnostics;
public class Myclass
{
    [Conditional("DEBUG")]
    public static void Message(string msg)
    {
        Console.WriteLine(msg);
    }
}
class Test
{
    static void function1()
    {
        Myclass.Message("In Function 1.");
        function2();
    }
    static void function2()
    {
        Myclass.Message("In Function 2.");
    }
    public static void Main()
    {
        Myclass.Message("In Main function.");
        function1();
        Console.ReadKey();
    }
}

/*
In Main function.
In Function 1.
In Function 2.
*/
```
- Obsolete：标记了不应被使用的程序实体，可以让编译器丢弃某个特定的目标元素，如当一个新方法被用在一个类中，但仍然想要保持类中的旧方法，可以通过显示一个应该使用新方法而不是旧方法的消息，来标记为obsolete（过时的）
```c#
[Obsolete(
   message
)]
[Obsolete(
   message,
   iserror
)]
// message - 是一个字符串，描述项目为什么过时以及该替代使用什么
// iserror - 是一个布尔值，若为true，编译器会将项目的使用当作一个错误，默认false（生成一个警告）
e.g:
using System;
public class MyClass
{
   [Obsolete("Don't use OldMethod, use NewMethod instead", true)]
   static void OldMethod()
   { 
      Console.WriteLine("It is the old method");
   }
   static void NewMethod()
   { 
      Console.WriteLine("It is the new method"); 
   }
   public static void Main()
   {
      OldMethod();
   }
}
// Don't use OldMethod, use NewMethod instead
```
### 自定义特性
.NET框架允许创建自定义特性，用于存储声明性的信息，且可在运行时被检索，该信息根据涉及标准和应用程序需要，可与任何目标元素相关。
创建步骤：
- 声明自定义特性
```c#
// 一个自定义特性 BugFix 被赋给类及其成员
[AttributeUsage(AttributeTargets.Class |
AttributeTargets.Constructor |
AttributeTargets.Field |
AttributeTargets.Method |
AttributeTargets.Property,
AllowMultiple = true)]

public class DeBugInfo : System.Attribute
```
- 构建自定义特性
```c#
// bug的代码编号
// 辨认该bug的开发人员名字
// 最后一次审查该代码的日期
// 一个存储开发人员标记的字符串消息
// 一个自定义特性 BugFix 被赋给类及其成员
[AttributeUsage(AttributeTargets.Class |
AttributeTargets.Constructor |
AttributeTargets.Field |
AttributeTargets.Method |
AttributeTargets.Property,
AllowMultiple = true)]

public class DeBugInfo : System.Attribute
{
  private int bugNo;
  private string developer;
  private string lastReview;
  public string message;

  public DeBugInfo(int bg, string dev, string d)
  {
      this.bugNo = bg;
      this.developer = dev;
      this.lastReview = d;
  }

  public int BugNo
  {
      get
      {
          return bugNo;
      }
  }
  public string Developer
  {
      get
      {
          return developer;
      }
  }
  public string LastReview
  {
      get
      {
          return lastReview;
      }
  }
  public string Message
  {
      get
      {
          return message;
      }
      set
      {
          message = value;
      }
  }
}
```
- 在目标程序元素上应用自定义特性
```c#
[DeBugInfo(45, "Zara Ali", "12/8/2012", Message = "Return type mismatch")]
[DeBugInfo(49, "Nuha Ali", "10/10/2012", Message = "Unused variable")]
class Rectangle
{
  // 成员变量
  protected double length;
  protected double width;
  public Rectangle(double l, double w)
  {
      length = l;
      width = w;
  }
  [DeBugInfo(55, "Zara Ali", "19/10/2012",
  Message = "Return type mismatch")]
  public double GetArea()
  {
      return length * width;
  }
  [DeBugInfo(56, "Zara Ali", "19/10/2012")]
  public void Display()
  {
      Console.WriteLine("Length: {0}", length);
      Console.WriteLine("Width: {0}", width);
      Console.WriteLine("Area: {0}", GetArea());
  }
}
```
- 通过反射访问特性

## 反射
定义：指程序可以访问、检测和修改它本身状态或行为的一种能力
作用：反射提供了封装程序集、模块和类型的对象，可以使用反射动态地创建类型地实例，将类型绑定到现有对象，或从现有对象中获取类型，可以调用类型地方法或访问其字段和属性
### 优缺点
优点：
1. 反射提高了程序的灵活性和扩展性
2. 降低耦合性，提高自适应能力
3. 它允许程序创建和控制任何类的对象，无需提前硬编码目标类
缺点：
1. 性能问题，使用反射基本上是一种解释操作，用于字段和方法接入时要远慢于直接代码；因此反射机制主要应用在对灵活性和拓展性要求很高的系统框架上，普通程序不建议。
2. 使用反射会模糊程序内部逻辑，反射绕过了源码，会带来维护的问题，会比相应的直接代码复杂
### 用途
- 允许在运行时查看特性信息
- 允许审查集合中的各种类型、以及实例化这些类型
- 允许延迟绑定的方法和属性
- 允许在运行时创建新类型，然后使用这些类型执行一些任务
### 查看元数据
```c#
using System;

[AttributeUsage(AttributeTargets.All)]
public class HelpAttribute : System.Attribute
{
   public readonly string Url;

   public string Topic  // Topic 是一个命名（named）参数
   {
      get
      {
         return topic;
      }
      set
      {

         topic = value;
      }
   }

   public HelpAttribute(string url)  // url 是一个定位（positional）参数
   {
      this.Url = url;
   }

   private string topic;
}
[HelpAttribute("Information on the class MyClass")]
class MyClass
{
}

namespace AttributeAppl
{
   class Program
   {
      static void Main(string[] args)
      {
         System.Reflection.MemberInfo info = typeof(MyClass);
         object[] attributes = info.GetCustomAttributes(true);
         for (int i = 0; i < attributes.Length; i++)
         {
            System.Console.WriteLine(attributes[i]);
         }
         Console.ReadKey();

      }
   }
}
// HelpAttribute
e.g:
using System;
using System.Reflection;
namespace BugFixApplication
{
   // 一个自定义特性 BugFix 被赋给类及其成员
   [AttributeUsage(AttributeTargets.Class |
   AttributeTargets.Constructor |
   AttributeTargets.Field |
   AttributeTargets.Method |
   AttributeTargets.Property,
   AllowMultiple = true)]

   public class DeBugInfo : System.Attribute
   {
      private int bugNo;
      private string developer;
      private string lastReview;
      public string message;

      public DeBugInfo(int bg, string dev, string d)
      {
         this.bugNo = bg;
         this.developer = dev;
         this.lastReview = d;
      }

      public int BugNo
      {
         get
         {
            return bugNo;
         }
      }
      public string Developer
      {
         get
         {
            return developer;
         }
      }
      public string LastReview
      {
         get
         {
            return lastReview;
         }
      }
      public string Message
      {
         get
         {
            return message;
         }
         set
         {
            message = value;
         }
      }
   }
   [DeBugInfo(45, "Zara Ali", "12/8/2012",
        Message = "Return type mismatch")]
   [DeBugInfo(49, "Nuha Ali", "10/10/2012",
        Message = "Unused variable")]
   class Rectangle
   {
      // 成员变量
      protected double length;
      protected double width;
      public Rectangle(double l, double w)
      {
         length = l;
         width = w;
      }
      [DeBugInfo(55, "Zara Ali", "19/10/2012",
           Message = "Return type mismatch")]
      public double GetArea()
      {
         return length * width;
      }
      [DeBugInfo(56, "Zara Ali", "19/10/2012")]
      public void Display()
      {
         Console.WriteLine("Length: {0}", length);
         Console.WriteLine("Width: {0}", width);
         Console.WriteLine("Area: {0}", GetArea());
      }
   }//end class Rectangle  
   
   class ExecuteRectangle
   {
      static void Main(string[] args)
      {
         Rectangle r = new Rectangle(4.5, 7.5);
         r.Display();
         Type type = typeof(Rectangle);
         // 遍历 Rectangle 类的特性
         foreach (Object attributes in type.GetCustomAttributes(false))
         {
            DeBugInfo dbi = (DeBugInfo)attributes;
            if (null != dbi)
            {
               Console.WriteLine("Bug no: {0}", dbi.BugNo);
               Console.WriteLine("Developer: {0}", dbi.Developer);
               Console.WriteLine("Last Reviewed: {0}",
                                        dbi.LastReview);
               Console.WriteLine("Remarks: {0}", dbi.Message);
            }
         }
         
         // 遍历方法特性
         foreach (MethodInfo m in type.GetMethods())
         {
            foreach (Attribute a in m.GetCustomAttributes(true))
            {
               DeBugInfo dbi = (DeBugInfo)a;
               if (null != dbi)
               {
                  Console.WriteLine("Bug no: {0}, for Method: {1}",
                                                dbi.BugNo, m.Name);
                  Console.WriteLine("Developer: {0}", dbi.Developer);
                  Console.WriteLine("Last Reviewed: {0}",
                                                dbi.LastReview);
                  Console.WriteLine("Remarks: {0}", dbi.Message);
               }
            }
         }
         Console.ReadLine();
      }
   }
}

/*
Length: 4.5
Width: 7.5
Area: 33.75
Bug No: 49
Developer: Nuha Ali
Last Reviewed: 10/10/2012
Remarks: Unused variable
Bug No: 45
Developer: Zara Ali
Last Reviewed: 12/8/2012
Remarks: Return type mismatch
Bug No: 55, for Method: GetArea
Developer: Zara Ali
Last Reviewed: 19/10/2012
Remarks: Return type mismatch
Bug No: 56, for Method: Display
Developer: Zara Ali
Last Reviewed: 19/10/2012
Remarks: 
*/
```
