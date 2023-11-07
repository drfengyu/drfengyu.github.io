---
title: C#WCF方向(三)
categories:
  - C#
tags:
  - WCF
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 31480aad
date: 2022-05-06 14:27:39
pic:
---

### 主题
- 序列化机制
- 数据契约
- 消息契约
- Demo
<!--more-->
### 序列化 Serialization
A格式 转换 B格式
### Transformer
#### 序列化器
1.BinaryFormatter 二进制平台
2.SoapFormatter SOAP
3.XmlSerializer Xml强大的控制 类型有限
4.DataContractSerializer WCF优化
5.NetDataContractSerializer WCF二进制
6.DataContractJsonSerializer JSON
#### WCF序列化过程
参数对象 序列化 传输 反序列化 执行操作 返回结果 序列化 传输
### 数据契约
### 消息契约
- 允许完全控制消息体
- SOAP消息头 Header
- SOAP消息体 Body
#### 消息契约示例
```bash
public class MyMessage{
	[MessageHeader]
	public string Operation;
	[MessageHeader]
	public string Name;
	[MessageBodyMember]
	public int Age;
	[MessageBodyMember]
	public string Home;
}
```
#### WCF序列化器 支持的数据类型
- 无参构造函数的Pulbic类
- 数据契约类
- 集合类:Ienumerable,IDictionary,CollectionDataContract
- 枚举类
- .NET基元类: Byte,Sbyte,Int16,Int32等
- SerializableAttribute 属性标记的类型
- 原始XML类型和ADO.NET关系数据的类型

#### WCF使用XmlSerializer
```bash
[ServiceContract]
[XmlSerializerFormat]
public interface MyService{
	[OperationContract]
	[XmlSerializerFormat]
	void MyMethod();
}
```
#### C#使用XmlSerializer
```bash
XmlSerializer serializer=new XmlSerializer(typeof(Order));
TextWriter writer=new StreamWriter(filename);
Order order=new Order();
serializer.Serialize(writer,po);
writer.Close();
``` 
#### C#中使用DataContractSerializer
```bash
FileStream writer=new FileStream(filename,FileMode.Create);
DataContractSerializer ser=new DataContractSerializer(typeof(Person));
ser.WriteObject(writer,pl);
writer.Close();
```
