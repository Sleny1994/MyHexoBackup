---
title: ASP.NET Core MVC 序列化
date: 2023-08-22
tags:
- CSharp
- NET
categories:
- CSharp
---

## 序列化和反序列化
- 序列化是将对象状态转换为可保持或传输的形式的过程
- 反序列化是将流转换为对象
- 两个过程一起，则保证了数据的存储和传输

## 应用场景
不仅仅运用于.NET项目中，其它框架的项目中也比较常见。
1. 将内存的对象序列化后保存在本地，上传到某些特定位置，如：共享目录、FTP、供第三方系统识别读取
2. 与第三方进行通信，对方只能接收二进制类型字节流数据
3. 保存Session、Cookie等场景
4. 跨平台，跨语言交互等场景

## 常见序列化格式
1. 整体二进制，将实例对象整体序列化成二进制
2. xml格式，将实例独享序列化成XML数据格式，多用于WebService
3. json格式，将实例化对象序列化成JSON格式，多用于WebService
4. Protobuf，即Protocol Buffers，是Google公司开发的一种跨语言和平台的序列化数据结构方式，是一个灵活的、高效的用于序列化数据的协议

## 示例
[本示例中，为便于比较序列化后内容的大小，将序列化后的内容保存到本地文件，且实现了序列化和反序列化功能。]
1. 安装第三方库</br>
序列化JSON和ProtoBuf格式需要安装第三方库，可通过NuGet包管理器进行安装
- Newtonsoft.Json
- protobuf-net
- protobuf-net.Core
2. 序列化帮助类接口</br>
为统一调用方式，定义序列化帮助类接口，不同实现方式，只需要实现对应接口即可
```c#
// Interfaces/ISerializeHelper.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    /// 序列化帮助类接口
    public interface ISerializeHelper
    {
        /// 序列化
        /// <typeparam name="T"></typeparam>
        /// <param name="t"></param>
        /// <param name="path">序列化后保存路径</param>
        void Serialize<T>(T t, string path) where T : class;

        /// 反序列化
        /// <typeparam name="T"></typeparam>
        /// <param name="path">反序列化文件路径</param>
        /// <returns></returns>
        T Deserialize<T>(string path) where T : class;
    }
}
```
3. 定义序列化模型Person
```c#
// Models/Person 定义一个测试类
using ProtoBuf;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    /// 个人信息
    [ProtoContract]
    [Serializable]
    public class Person
    {
        /// 唯一标识
        [ProtoMember(1)]
        public int Id { get; set; }

        /// 姓名
        [ProtoMember(2)]
        public string Name { get; set; }

        /// 生日
        [ProtoMember(3)]
        public DateTime Birthday { get; set; }

        public override string ToString()
        {
            return $"Id={Id},Name={Name},Birthday={Birthday.ToString("yyyy-MM-dd HH:mm:ss.fff")}";
        }
    }
}
```
【注:】
- 进行整体二进制序列化时，必须将类标记为Serializable，否则会抛出异常
- Protobuf序列化需要将类标记为ProtoContract，并将需要序列化的属性标记为ProtoMember
4. 整体二进制
```c#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.Serialization.Formatters.Binary;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    internal class BinHelper : ISerializeHelper
    {
        public T Deserialize<T>(string path) where T:class
        {
            string filePath = path;
            T t;
            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                BinaryFormatter bf = new BinaryFormatter();
                t = bf.Deserialize(fs) as T;
            }
            return t;
        }

        public void Serialize<T>(T t, string path) where T : class
        {
            string filePath = path;
            using (FileStream fs = new FileStream(filePath, FileMode.Create))
            {
                BinaryFormatter bf = new BinaryFormatter();
                bf.Serialize(fs, t);
            }
        }
    }
}
```
5. XML格式
```c#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Xml.Serialization;

namespace ConsoleApp
{
    public class XmlHelper : ISerializeHelper
    {
        public T Deserialize<T>(string path) where T : class
        {
            string filePath = path;
            T t;
            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                XmlSerializer serializer = new XmlSerializer(typeof(Person));
                object obj = serializer.Deserialize(fs);
                t = obj as T;
            }
            return t;
        }

        public void Serialize<T>(T t, string path) where T : class
        {
            string filePath = path;
            using (FileStream fs = new FileStream(filePath, FileMode.Create))
            {
                XmlSerializer serializer = new XmlSerializer(typeof(Person));
                serializer.Serialize(fs, t);
            }
        }
    }
}
```
6.JSON格式
- JSON(JavaScript Object Notation)是一种轻量级的数据交换格式，适用于数据交互的场景，如网站前后端间的数据交互。
- JSON是比XML更简单的一种数据交换格式，采用完全独立于编程语言的文本格式来存储和表示数据。
- 一般采用第三方库Newtonsoft.Json来实现
```C#
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    internal class JsonHelper : ISerializeHelper
    {
        public T Deserialize<T>(string path) where T : class
        {
            T t;
            using (StreamReader file = File.OpenText(path))
            {
                JsonSerializer serializer = new JsonSerializer();
                t = (T)serializer.Deserialize(file, typeof(T));

            }
            return t;
        }

        public void Serialize<T>(T t, string path) where T : class
        {
            using (StreamWriter file = File.CreateText(path))
            {
                JsonSerializer serializer = new JsonSerializer();
                serializer.Serialize(file, t);
            }
        }
    }
}
```
7. Protobuf格式
- 与XML和JSON相比，Protobuf更小、更快、更便捷
```c#
using ProtoBuf;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.Serialization.Formatters.Binary;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    internal class ProtobufHelper : ISerializeHelper
    {
        public T Deserialize<T>(string path) where T : class
        {
            string filePath = path;
            T t;
            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                t = Serializer.Deserialize<T>(fs);
            }
            return t;
        }

        public void Serialize<T>(T t, string path) where T : class
        {
            string filePath = path;
            using (FileStream fs = new FileStream(filePath, FileMode.Create))
            {
                Serializer.Serialize<T>(fs, t);
            }
        }
    }
}
```
8. 实例测试
```c#
// 序列化
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Person person = new Person()
            {
                Id = 1,
                Name = "Sleny",
                Birthday = DateTime.Now,
            };
            //bin格式序列化
            var binHelper = new BinHelper();
            string binPath = @"D:\serialize\person.bin";
            binHelper.Serialize<Person>(person, binPath);

            //xml格式序列化
            var xmlHelper = new XmlHelper();
            string xmlPath = @"D:\serialize\person.xml";
            xmlHelper.Serialize<Person>(person, xmlPath);

            //json格式序列化
            var jsonHelper = new JsonHelper();
            string jsonPath = @"D:\serialize\person.json";
            jsonHelper.Serialize<Person>(person, jsonPath);

            //protobuf格式序列化
            var protoHelper= new ProtobufHelper();
            var protoPath = @"D:\serialize\person.proto";
            protoHelper.Serialize<Person>(person, protoPath);
        }
    }
}

// 反序列化
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    internal class Program
    {
        static void Main(string[] args)
        {
            //bin格式反序列化
            var binHelper = new BinHelper();
            string binPath = @"D:\serialize\person.bin";
            var p1 = binHelper.Deserialize<Person>(binPath);
            //xml格式反序列化
            var xmlHelper = new XmlHelper();
            string xmlPath = @"D:\serialize\person.xml";
            var p2 = xmlHelper.Deserialize<Person>(xmlPath);
            //json格式反序列化
            var jsonHelper = new JsonHelper();
            string jsonPath = @"D:\serialize\person.json";
            var p3 = jsonHelper.Deserialize<Person>(jsonPath);
            //protobuf格式反序列化
            var protoHelper= new ProtobufHelper();
            var protoPath = @"D:\serialize\person.proto";
            var p4= protoHelper.Deserialize<Person>(protoPath);

            Console.WriteLine($"p1:{p1}");
            Console.WriteLine($"p2:{p2}");
            Console.WriteLine($"p3:{p3}");
            Console.WriteLine($"p4:{p4}");
        }
    }
}
```

## 实例比较
1. 整体二进制格式：person.bin 225字节
2. XML格式：person.xml 242字节
3. JSON格式：person.json 77字节
4. Protobuf格式：person.proto 29字节

综上所述：proto < json < bin < xml