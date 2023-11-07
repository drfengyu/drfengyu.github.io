---
title: C#WCF方向(一)
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
abbrlink: e08ab1e4
date: 2022-05-03 13:55:35
pic:
---

## WCF
开始入手WCF了,从零开始,记录一下WCF的学习笔记
### 什么是WCF
摘要
WCF是使用托管代码建立和运行面向服务（Service Oriented）应用程序的统一框架。它使得开发者能够建立一个跨平台的安全、可信赖、事务性的解决方案，且能与已有系统兼容协作。WCF是微软分布式应用程序开发的集大成者，它整合了.Net平台下所有的和分布式系统有关的技术，例如.Net Remoting、ASMX、WSE和MSMQ。以通信(Communiation)范围而论，它可以跨进程、跨机器、跨子网、企业网乃至于 Internet；以宿主程序而论，可以以ASP.NET，EXE，WPF，Windows Forms，NT Service，COM+作为宿主(Host)。
<!--more-->
简述
1.WCF全称Windows Communication Foundation
2.是Miscrosoft为构建面向服务的应用提供的分布式通信编程框架
3.是.Net Framework的重要组成部分.
4.WCF4.0
历史 
ASMX WSE WCF3.0 WCF3.5 WCF4.0 WCFN.0
### 集成微软分布式技术
WCF:WSE. MSMQ.  Enterprise Service COM+ .NET Remoting ASP.NET Web Service
### 托管方式多样化
托管(Host)
IIS EXE Winform wpf Window Service  NT Service COM+
### 统一编程模型
Client Talk to  (Binding,Address,Contract)
1.Address: 每一个WCF的Service都有一个唯一的地址。这个地址给出了Service的地址和传输协议(Transport Protocol)。
2.Binding:绑定制定了服务通过什么形式访问。只要类比传输协议, encoding (text, binary, etc) 以及 WS-* 协议，像transactional支持以及可信任的消息队列。
3.Contract:Contract描述了Service能提供的各种服务。Contract有四种，包括Service Contract, Data Contract, Fault Contract和Message Contract。
### WCF知识点
1.契约 Contract
2.宿主 Host
3.绑定 Binding
4.事务 Transaction
5.消息队列 MSMQ
6.并发控制 Concurrency Control
7.错误处理 Error Handling
8.安全 Security
9.诊断与调试
### 软件工程的发展历史
面向过程PO 面向对象OO 面向组件CO 面向服务SO
### SOA的特征
1.Service Oriented Architecture
2.面向服务的架构
互操作 重用 松耦合
### Web服务类别
SOAP REST
soap:简单对象访问协议 
rest：轻量级WebApi服务
### Web服务使用的领域
开发中间产品 暴露接口服务 集成第三方系统
### 应用系统中的Web服务
表现层  
服务层                                      
业务逻辑层——>  ESB企业服务总线   ——>服务
数据访问层                                
数据 数据
### 系统集成中的Web服务
   电子商务网站订单系统
    |              |                |
银行服务 库存系统服务 短信服务
### Demo 
1.创建一个WCF服务程序 程序并调用
打开Visual Studio开发工具 我这里用的2022
左上角菜单 文件 点击新建 选择项目 打开了创建新项目的对话框 我们在搜索栏搜素wcf
搜索结果如下所示:
![](1.jpg)
这里选择WCF服务应用程序
![](2.jpg)
自定义下项目名 位置 这里选用.Net4.5.2框架 创建即可
![](3.jpg)
创建完成后 默认会添加IService1.cs,Service1.svc文件,.svc扩展文件,它会做一些消息的路由工作,激活服务,会通过文件做一些处理.
IService1.cs
ServiceContract:服务契约
包含两个操作契约OperationContract：GetData,GetDataUsingDataContract 使用数据契约获取数据
DataContract:数据契约:
包含两个值 bool  StringValue
做下自定义更改
1.增加数据契约对象
我们在IService1.cs中创建一个类自定义命名我的如下新增一个用户数据对象,增加以下内容
```bash
[DataContract]//数据契约对象
    public class UserData { 
        [DataMember]
        public string UserName { get; set; }
        [DataMember]
        public string Email { get; set; }
    }
```
2.增加服务契约接口
我们在IService1.cs中interface IService1中新增接口
```bash
// TODO: 在此添加您的服务操作
        /// <summary>
        /// 自定义服务契约
        /// </summary>
        /// <param name="user"></param>
        /// <returns></returns>
        [OperationContract]///注意:增加操作契约标记
        UserData GetUserData(UserData user);
```
3.在Service1.svc中新增实现
```bash
 ///新增实现
        public UserData GetUserData(UserData user) {
            return user;
        }
```
到此，我们已完成了简单的数据服务实现契约,点击导航栏生成——>重新生成解决方案,确认无报错后执行下一步
4.在浏览器中查看
点击Service1.svc右键选择在浏览器中查看
visual Studio打开浏览器,跳转服务界面.
![](4.jpg)
出现这个界面,说明你的服务是正常的.可以发布了.我们看到
```bash
svcutil.exe http://localhost:60217/Service1.svc?wsdl
```
wsdl:服务描述语言,对外公布的服务语言,你想让别人引用你的服务的话,就可以引用这个.
我们可以看到下方有客户端调用示例.后面客户端调用服务可以仿照使用
```bash
C#

class Test
{
    static void Main()
    {
        Service1Client client = new Service1Client();

        // 使用 "client" 变量在服务上调用操作。

        // 始终关闭客户端。
        client.Close();
    }
}

Visual Basic

Class Test
    Shared Sub Main()
        Dim client As Service1Client = New Service1Client()
        ' 使用 "client" 变量在服务上调用操作。

        ' 始终关闭客户端。
        client.Close()
    End Sub
End Class
```
5.新增WPF项目
我们在解决方案下新增一个WPF项目
(也可以新增一个网站项目,Winform项目等)
![](5.jpg)
自定义名字WCFClient创建.
6.引用服务
在创建好的wpf项目中选择引用,右键选择添加服务引用,打开如下:
![](6.jpg)
我们输入步骤4在浏览器打开后生成的公共服务语言
```bash
http://localhost:60217/Service1.svc?wsdl
```
自定义命名空间名称ServiceReference确定.
提示:我们也可以点击发现.发现解决方案中的服务.
服务栏会为我们找到对应服务.
![](7.jpg)
点击Service1.svc打开到IService1,即可查看右侧列表显示的三个服务接口,两个默认的服务接口契约,
和一个我们新增的GetUserData服务契约.(如果没有显示新增的接口,请返回WCF项目检查是否遗漏契约标记)
点击添加后的服务引用可以查看
![](8.jpg)
7.使用Wpf调用服务
- 打开MainWindow新增Button按钮,可以打开工具栏拖动Button到界面上,或编写Xaml文本增加,完成后调整布局.
```bash
<Grid HorizontalAlignment="Center" VerticalAlignment="Center">
        <Button
            Width="100"
            Height="30"
            Content="调用服务" Click="Button_Click"/>
    </Grid>
```
- 点击Click事件F12进入后置代码,这里不做复杂框架处理,后置编写点击实现Wcf服务调用
具体实现如下:
```bash
/// <summary>
        /// 调用WCF服务.
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            Service1Client client = new Service1Client();
            string result1 = client.GetData(1);
            UserData userData = client.GetUserData(new UserData() { Email="Drfengling@163.com",UserName="浅梦风凌"});
            client.Close();
        }
```
8.生成调试服务
- 点击导航栏生成 重新生成解决方案 确认无误后在点击事件处打断点调试
- 选择WCFClient为启动项目,点击导航栏调试开始调试,点击按钮调用服务,观察服务调用结果
![](9.jpg)
可以看到我们在客户端完成了对服务端的调用,调用获取到了WCF服务结果.
快来动手试试搭建简单的服务调用吧.

### WCF的新特性
WCF4.5新特性
1.支持契约优先(Contract First)的开发
2.Building发现配置错误
3.支持新异步流
4.?singleWSDL
5.Websockets支持在端口80和443
6.XML编辑器工具展示
7.ChannelFactory缓存支持
8.二进制编码器压缩支持
9.不管Fire and foget的UDP消息发交换模式
10.支持单个WCF终结点支持多个身份验证模式
11.支持WCF服务使用国际化域名（IDNs）

### WCF学习资源
1.最权威WCF书籍
- Programing WCF Service,(Juval Lowy)
- 《WCF服务编程》第三版
- Inside WCF,(Justin Smith)
- 《WCF技术内幕》
2.论坛
- 微软WCF中文技术论坛
[http://social.msdn.microsoft.com/Forums/zh-CN/wcfzhchs/threads](http://social.msdn.microsoft.com/Forums/zh-CN/wcfzhchs/threads)
- 微软WCF英文技术论坛
- 跟我一起从零开始学WCF系列课程(由于官网视频已经失效了,有兴趣可以看看这篇博客)
[https://www.cnblogs.com/dqj1024/archive/2012/07/21/2602342.html](https://www.cnblogs.com/dqj1024/archive/2012/07/21/2602342.html)