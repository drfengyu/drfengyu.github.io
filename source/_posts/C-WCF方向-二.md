---
title: C#WCF方向(二)
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
abbrlink: 4fbb2a86
date: 2022-05-04 09:24:05
pic:
---

### 主题
1.终结点 Endpoint
2.契约 Contract
3.宿主 Host
4.绑定 Binding
5.Demo:定义契约,终结点,配置绑定
<!--more-->
### 必备知识
1.了解.Net Framework
2.了解Asp.net应用程序开发
3.熟悉Visual Studio
### 可选知识
1.Enterprise Services(COM+)
2..Net Remoting
3.Asp.Net web Service(ASMX)

### 终结点 EndPoint
1.2种类型 终结点
- 元数据终结点 
- 普通终结点

### 地址 Address
- 相对地址
- 绝对地址
- 基地址 BaseAddress 基础地址 (下面[http://localhost:6054](http://localhost:6054)就是基地址)
```bash
<endpoint address="http://localhost:6054/ServiceDemo.svc"
 binding="basicHttpBinding" bindingConfiguration="BasicHttpBinding_IServiceDemo"
 contract="ServiceReferenceDemo.IServiceDemo" name="BasicHttpBinding_IServiceDemo"/>
```

### 绑定 Binding
- 绑定是特殊配置元素(称作“绑定元素”)的集合,服务运行库在每次构造客户端或服务终结点时都对这些元素进行评估
- WCF里的传输协议,安全,事务,消息编码,超时等等都是由绑定来控制
- 控制WCF的一个接口
#### 绑定列表
![](1.jpg)

#### Windows Azure AppFabric绑定
- NetTcpRelayBinding 消息中途转发机制
- NetOneWayRelayBinding 消息单向转发
- NetEventRelayBinding 基于事件的
- webHttpRelayBinding 
- WS2007HttpRelayBinding

#### WCF绑定和通道堆栈
![](5.png)
#### 绑定的结构
![](6.png)
#### 绑定结构例子
![](7.png)
### 契约 Contract
契约是通信参与方之间的约定
- 数据契约 DataContract 实体类
- 操作契约 Operation Contract 服务方法
- 服务契约 Service Contract 服务
- 消息契约 Message Contract 对消息体的完整控制
- 错误契约 Fault Contract 把错误信息包装起来返回给客户端

#### 数据契约
```bash
[DataContract]//数据契约属性声明
    public class UserData
    {

        [DataMember]//数据成员标记
        public string Name{set;get;};

        [DataMember(Name="UserEmail")]//支持别名定义
        public string Email{set;get;};
    }
```
#### 序列化
1.XmlSerialzer
2.DataContractSerializer
3.NetDataContractSerializer NetTcp使用的
4.DataContractJsonSerializer json数据序列化

#### 服务契约
ServiceContract
定义约定的服务
```bash
[ServiceContract]//服务契约
    public interface IServiceDemo
    {
        [OperationContract]//操作契约
        string GetData(int value);

        [OperationContract]
        UserData GetDataUsingDataContract(UserData user);

        // TODO: Add your service operations here
    }
```
#### 操作契约
```bash
[OperationContract]//操作契约
        string GetData(int value);
```
#### 消息契约 MessageContract
1.允许完全控制消息体
2.SOAP消息头 header
3.SOAP 消息体 Body
#### 错误契约 FaultContract
1.为了在客户端和服务端共享错误信息
2.错误处理
### 宿主Host
IIS.EXE.WinForm.Wpf.WindowService.NT Service.COM+
#### 托管进程
![](2.png)
#### 怎么托管WCF服务
![](3.png)
#### Window Server AppFabric
![](4.png)
### WCF绑定学习资源
- 书籍
   - Inside WCF,(Justin Smith) 《WCF技术内幕》
   - 第8章 绑定
- 文章
   - Aaron Skonnard 深入学习WCF绑定

### WCF服务配置管理工具
点击Visual Studio导航栏 工具 WCF配置编辑器
打开配置文件app.config
![](9.png)
### Demo
- 定义契约
- 配置终结点
- 使用绑定
#### 定义契约
1.打开Visual Studio,创建新项目,我们搜索wcf,选择wcf服务库,
![](10.png)
2.自定义项目名称路径创建即可(后面不再图文赘述)
![](11.png)
3.这里做简单更改
- IService1
```bash
 // 注意: 使用“重构”菜单上的“重命名”命令，可以同时更改代码和配置文件中的接口名“IService1”。
    [ServiceContract]
    public interface IService1
    {
        [OperationContract]
        string SayHello(string Name);
        [OperationContract]
        string SayHellotoUser(User user);

    }



    // 使用下面示例中说明的数据约定将复合类型添加到服务操作。
    // 可以将 XSD 文件添加到项目中。在生成项目后，可以通过命名空间“WcfService.ContractType”直接使用其中定义的数据类型。
    [DataContract]
    public class User
    {
        [DataMember]
        public string FirstName { get; set; }
        [DataMember]
        public string SecondName { set; get; }

    }
```
- Service1
```bash
public class Service1 : IService1
    {
        public string SayHello(string Name)
        {
            string result = $"Hello,{Name}";
            Console.WriteLine(result);
            return result;
        }

        public string SayHellotoUser(User user)
        {
            string result = $"Hello,{user.FirstName}{user.SecondName}";
            Console.WriteLine(result);
            return result;
        }
    }
```
#### 配置终结点
1.右键解决方案添加
2.新建项目 搜索控制台 选择控制台应用程序创建WcfHost
![](13.png)
3.添加对定义契约的Wcf服务类库的引用,我们打开Program.cs文件创建打开服务.
```bash
using (ServiceHost host=new ServiceHost(typeof(WcfService.Service1)))
            {
                host.Open();
                Console.WriteLine($"Host is Running,and State is {host.State}");
                Console.ReadLine();
            }
```
4.打开app.config 配置终结点 这里配置了4个终结点
```bash
<system.web>
    <compilation debug="true" />
  </system.web>
  <!-- 部署服务库项目时，必须将配置文件的内容添加到
 主机的 app.config 文件中。System.Configuration 不支持库的配置文件。 -->
  <system.serviceModel>
    <services>
      <service name="WcfService.Service1">
        <host>
          <baseAddresses>
			  <add baseAddress="http://localhost:8001"/>
			  <add baseAddress="net.tcp://localhost:8002"/>
            <!--<add baseAddress = "http://localhost:8733/Design_Time_Addresses/WcfService/Service1/" />-->
          </baseAddresses>
        </host>
        <!-- Service Endpoints -->
        <!-- 除非完全限定，否则地址相对于上面提供的基址-->
		  <endpoint address="http://localhost:8001/WcfService" binding="wsHttpBinding" name="httpEndpoint" contract="WcfService.IService1">
		  </endpoint>
		  <endpoint address="net.tcp://localhost:8002/WcfService" binding="netTcpBinding" name="tcpEndpoint" contract="WcfService.IService1">
		  </endpoint>		 
        <!-- Metadata Endpoints -->
        <!-- 元数据交换终结点供相应的服务用于向客户端做自我介绍。 --> 
        <!-- 此终结点不使用安全绑定，应在部署前确保其安全或将其删除-->
		  <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" name="httpMex"/>
		  <endpoint address="mex" binding="mexTcpBinding" contract="IMetadataExchange" name="tcpMex"/>
      </service>
    </services>
    <behaviors>
      <serviceBehaviors>
        <behavior>
          <!-- 为避免泄漏元数据信息，
          请在部署前将以下值设置为 false -->
          <serviceMetadata httpGetEnabled="True" httpsGetEnabled="True"/>
          <!-- 要接收故障异常详细信息以进行调试，
          请将以下值设置为 true。在部署前设置为 false 
          以避免泄漏异常信息 -->
          <serviceDebug includeExceptionDetailInFaults="False" />
        </behavior>
      </serviceBehaviors>
    </behaviors>
  </system.serviceModel>
```
5.点击导航栏生成重新生成解决方案,设置WcfHost为启动项目
启动项目
![](14.png)
6.我们关闭Visual Studio,找到Visual Studio右键以管理员权限运行,重新打开项目并启动.完成!
![](15.png)
基于控制台的Wcf服务搭建完成,我们新建一个客户端来测试服务.
#### 使用绑定
1.右键解决方案,添加 新建项目 新建控制台应用程序WcfClient
2.点击引用, 点击发现或手动输入添加服务引用
![](16.png)
3.打开Program.cs测试服务
```bash
Service1Client client1 = new Service1Client("httpEndpoint");
            Console.WriteLine(client1.SayHello("浅梦风凌"));
            User user1 = new User();
            user1.FirstName = "浅梦";
            user1.SecondName = "风凌";
            Console.WriteLine(client1.SayHellotoUser(user1));
            Console.Read();
```
4.导航栏生成重新生成解决方案,启动
1.启动前右键解决方案,点击属性,配置多个启动项目
![](17.png)
2.启动
![](18.png)
好了,可以看到服务调用结果了.完成!我们的Wcf从创建服务到配置绑定到测试已经全部完成了。
#### 对tcp终结点的调用如下
```bash
Service1Client client2 = new Service1Client("tcpEndpoint");
            Console.WriteLine(client2.SayHello("小猫"));
            User user2 = new User();
            user2.FirstName = "海边的";
            user2.SecondName = "海边";
            Console.WriteLine(client1.SayHellotoUser(user2));
```
