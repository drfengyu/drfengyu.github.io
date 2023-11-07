---
title: 深入浅出Windows应用开发(四)
categories:
  - Windows应用开发
tags:
  - UWP
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 8cfe4bab
date: 2022-12-20 13:18:46
pic:
---
#### 网络编程
HTTP协议是建立在TCP协议之上的一种应用.HTTP协议是超文本传送协议的缩写,它是万维网的基础,也是设备联网常用的协议之一
14.1.网络编程之HttpWebRequest类
HttpWebRequest类,另外一个是HttpClient类.HttpWebRequest类的网络请求适合于处理简单的网络请求,而HttpClient类对HTTP请求的支持更加强大,适合复杂的网络请求的封装.
14.11.HttpWebRequest实现Get请求.
HTTP的Get请求是最简单的HTTP请求,Get请求和Post请求的主要区别是:Get请求是从服务器上获取数据;而Post请求是向服务器传送数据;Get请求通过URL提交数据,数据在URL中可以看到,而Post请求是通过写入数据流的方式提交;Get请求提交的数据最多只能有1024字节,而Post请求则没有此限制.
1.获取WebRequest对象.
WebRequest类是请求/响应模型的基类,这是一个用于访问Internet数据的abstract类.HttpWebRequest类提供WebRequest类的HTTP特定的实现.HttpWebRequest类对WebRequest中定义的属性和方法提供支持,使用WebRequest.Create方法初始化新的HttpWebRequest对象.如果统一资源标识符(URI)的方法是http://或https://,则WebRequest.Create返回HttpWebRequest对象.
``` bash
WebRequest request=HttpWebRequest.Create("http://www.baidu.com");
```
2.设置请求的参数,发起GetResponse请求.
网络请求的参数可以通过HttpWebRequest对象的相关属性来设置,比如通过Method属性设置请求的类型"Get",通过Headers属性设置请求头等,然后再使用httpWebRequest对象的BeginGetResponse和EndGetResponse方法对资源发出异步请求.
``` bash
//设置为Get请求
request.Method="GET"
//通过HTTP头设置请求的Cookie
request.Headers["Cookie"]="name==value"
//设置身份验证的网络凭据
reqeust.Credentials=new NetworkCredential("accountKey","accountkeyOrPassword');
//发起GetResponse请求
request.BeginGetResponse(ResponseCallback,request);
//请求回调方法
private async void ResponseCallback(IAsyncResult result){
	HttpWebRequest httpWebRequest=(HttpWebRequest)result.AsyncState;
	WebResponse webResponse=httpWebRequest.EndGetResponse(result);
	... ...获取请求返回的内容
}
```
3.获取请求的内容
在请求回调方法里面,获取到了WebResponse对象,这个对象表示网络请求返回的信息.调用WebResponse对象的GetResponseStream方法则可以获取到网络请求返回的数据流,从该数据流里面就可以解析出网络返回的内容.
``` bash
//请求回调方法
private async void ResponseCallback(IAsyncResult result){
	HttpWebRequest httpWebRequest=(HttpWebRequest)result.AsyncState;
	WebResponse webResponse=httpWebRequest.EndGetResponse(result);
//获取请求返回的内容
using(Stream stream=webResponse.GetResponseStream()){
	using(StreamReader reader=new StreamReader(stream)){
	//请求返回的字符串内容
	string content=reader.ReadToEnd();
}
}
}
```
4.异常处理
在网络请求的过程中,难免会出现一些异常,这些异常可以通过try catch语句来捕获.如果在访问资源时发生错误,则httpWebRequest类将引发WebException.WebException.Status属性包含指示错误源的WebExceptionStatus值,通过该值便可知道哪一种情况导致请求失败.WebExceptionStatus枚举的取值情况如下所示:Success表示成功;ConnectFailure表示远程服务器连接失败,SendFailure表示发送失败.未能将完整请求发送到远程服务器,RequestCanceled表示该请求将被取消,Pending表示内部异步请求挂起;UnknownError表示未知错误.MessageLengthLimitExceeded表示网络请求的消息长度受到限制.
``` bash
	try{
	HttpWebRequest httpWebRequest=(HttpWebRequest)result.AsyncState;
	WebResponse webResponse=httpWebRequest.EndGetResponse(result);
	using(Stream stream=webResponse.GetResponse()){
	using(StreamReader reader=new StreamReader(stream)){
	string content=reader.ReadToEnd();
	
}
}
}catch(WebException e){
	switch(e.Status){
	case WebExceptionStatus.ConnectFailure:
	exceptionInfo="ConnectFailure:远程服务器连接失败.";
	break;
	case WebExceptionStatus.MessageLengthLimitExceeded:
	exceptionInfo="MessageLengthLimitExceeded:网络请求的消息长度受到限制."
	break;
	case WebExceptionStatus.Pending:
	exceptionInfo="Pending:内部异步请求挂起";
	break;
	case WebExceptionStatus.RequestCanceled:
	exceptionInfo="RequestCanceled:该请求将被取消";
	break；
	case WebExceptionStatus.SendFailure:
	exceptionInfo="SendFailure发送失败,未能将完整请求发送到远程服务器";
	break;
	case WebExceptionStatus.UnknownError:
	exceptionInfo="UnknownError:未知错误.";
	break;
	case WebExceptionStatus.Success:
	exceptionInfo="Success:请求成功";
	break;
}
}
```
httpWebRequest类常用属性
Accept 获取或设置Accept HTTP标头的值
AllowReadStreamBuffering 获取或设置一个值,该值指示是否对从Internet资源读取的数据进行缓冲处理
ContentType 获取或设置Content-typeHTTP标头的值
CookieContainer 指定与HTTP请求相关联的CookieCollection对象的集合
CreatorInstance 当在子类中重写时,获取从IWebRequestCreate类派生的工厂对象,该类用于创建为生成对指定URI的请求而实例化的WebRequest
Credentials 挡在子类中被重写时,获取或设置用于对Internet资源请求进行数据验证的网络凭据
HaveResponse 获取一个值,该值指示是否收到了来自Internet资源的响应
Headers 指定构成HTTP标头的名称/值对的集合
Method 获取或设置请求的方法
RequestUri 获取请求的原始统一资源标识符(URI)
14.12.HttpWebRequest实现Post请求
1.发起Post请求
发起Post请求需要通过HttpWebRequest.Create方法获取WebRequest对象,这和Get请求是一样的,注意获取到的WebRequest对象的Method属性要设置为"POST",其他HTTP等属性的设置和Get请求一致.发起Post请求需要调用的是BeginGetRequestStream方法来发起获取发送数据流的请求,然后在回调方法里面通过EndGetRequestStream方法来获取到返回的发送数据流.
``` bash
//创建WebRequest对象
var request=HttpWebRequest.Create("http://www.yourwebsite.com");
//设置请求的方式为Post
request.Method="POST";
//发起获取发送数据流的请求
request.BeginGetRequestStream(ResponseStreamCallbackPost,request);
//发起获取发送数据流的请求的响应回调方法
private async void ResponseStreamCallbackPost(IAsyncResult result){
	HttpWebRequest httpWebRequest=(httpWebRequest)result.AsyncState;
	...接下来通过发送数据流来发送数据
}
```
2.传递Post数据
发起Post请求之后,在回调的方法里面可以通过HttpWebRequest对象来获取发送的数据流,把传递的Post数据写入数据流来实现数据的传递.
``` bash
//发起获取发送数据流的请求的响应回调方法
private async void ResponseStreamuCallbackPost(IAsyncResult result){
	HttpWebRequest httpWebRequest=(HttpWebRequest)result.AsyncState;
	using(Stream stream=httpWebRequest.EndGetRequestStream(result)){
	//需要把待发送的数据通过byte[]数据格式发送出去
	//把字符串的信息转换为byte[]
	byte[] data=Encoding.UTF8.GetBytes(postString);
	stream.Write(data,0,data.length);
}
	...接下来获取请求的响应
}
```
3.获取Post结果
Post请求的数据写入完毕后,需要继续发起BeginGetResponse请求,获取服务器端的响应,这一步骤和Get请求是一致的,服务器需要把相关的结果返回给客户端
``` bash
httpWebRequest.BeginGetResponse(ResponseCallbackPost,httpWebRequest);
//请求回调方法
private async void ResponseCallbackPost(IAasyncResult result){
	HttpWebRequest httpWebRequest=(HttpWebRequest)result.AsyncState;
	WebResponse webResponse=httpWebRequest.EndGetResponse(result);
	//获取请求返回的内容
using(Stream stream=webResponse.GetResponseStream()){
	using(StreamReader reader=new StreamReader(stream)){
	//请求返回的字符串内容
	string content=reader.ReadToEnd();
}
}
}
```
14.13.网络请求的取消
1.网络请求时间过长,取消网络请求;另外一种情况是用户离开了需要显示网络数据的页面,客户端的程序把网络请求取消掉,避免不必要的资源.网络请求的取消可以直接调用HttpWebRequest对象的Abort方法,取消对Internet资源的请求,所以要实现取消请求的功能,必须把创建的HttpWebRequest对象作为一个公共变量来存放,然后通过调用其Abort方法进行取消请求,调用Abort方法取消网络请求的时候,会引发WebException异常,异常的类型是WebExceptionStatus.RequestCanceled表示该请求将被取消,所以网络请求的取消需要结合WebException异常的监控来一起完成.
14.14.超时控制
HttpWebRequest类并没有提供超时控制的属性或方法,它直接依赖于HttpWebRequest内部的HTTP机制来实现超时控制,所以要自定义实现HttpWebRequest类的HTTP请求的超时控制,可以根据使用线程信号类和Abort方法实现超时控制.实现的原理是当超过了一定的时候之后,如果网络请求还没有返回结果,就主动调用HTTP请求对象的Abort方法,取消网络请求.
线程信号类可以使用AutoResetEvent类来辅助实现超时控制,AutoResetEvent类允许线程通过发信号互相通信,线程通过调用AutoResetEvent上的WaitOne来等待信号,如果AutoResetEvent为非终止状态,则线程会被阻止,并等待当前控制资源的线程通过调用Set来通知资源可用,通过调用Set向AutoResetEvent发信号以释放等待线程.AutoResetEvent将保持终止状态,直到一个正在等待的线程被释放,然后自动返回非终止状态.
如果没有任何线程在等待,则状态将无限期的保持为终止状态,如果当AutoResetEvent为终止状态时调用WaitOne,则线程不会被阻止,AutoRestEvent将立即释放线程并返回到非终止状态,如果当AutoResetEvent为终止状态时线程调用WaitOne,则线程不会被阻止,AutoResetEvent将立即释放线程并返回到非终止状态,使用AutoResetEvent来控制HTTP的超时控制代码如下所示
``` bash
//信号量对象
private AutoResetEvent autoResetEvent;
//网络请求对象
private HttpWebRequest request;
//超时控制的方法,3秒钟没有网络返回将取消网络请求
await Task.Factory.StartNew(networkRequest);
//等待3秒钟
autoResetEvent.WaitOne(3000);
if(request!=null){
	//取消网络请求
	request.Abort();
}
```
15.断点续传
断点续传是指文件的上传或下载在数据传输的过程期间发生中断,下次再进行传输时,从上次文件中断的地方开始传输数据,而不是从文件开头传送.使用HttpWebRequest可以实现网络文件的上传和下载,如果要实现断点续传的机制,需要做进一步的处理,处理的方式是通过HTTP请求头来控制请求下载的文件片段,如果流程中断,就可以把当前请求的位置记录下载,下次再从当前的位置开始.
在断点续传的过程中,需要设置请求头的Range和Content-Range实体头的数据,Range用于请求头中,指定第一个字节的位置和最后一个字节的位置
``` bash
Range:(unit=first byte pos)-[last byte pos]
```
Content-Range用于响应头,指定整个实体中的一部分的插入位置,它我也指示整个实体的长度.在服务器向客户返回一个部分响应,它必须描述响应覆盖的范围和整个实体长度,一般格式如下:
Content-Range: bytes (unit first byte pos)-[last byte pos]/[entity legth]
所以,如果是实现断点续传下载文件,就需要分片下载,通过设置Range的请求范围来实现不同片段的数据的请求,比如第一包的请求设置Range的值为"bytes=0-100",下载成功之后再设置为"bytes=100-200",以此类推一直到文件全部下载成功,如果下载终端则下次再从中断的位置下载.如果是文件上传,实现断点续传也是一样的原理,但是必须要服务器支持才行.
``` bash
var request=HttpWebRequest.Create(url);
request.Method="GET";
request.Headers["Range"]="bytes-0-100";
request.BeginGetResponse(ResponseCallbackTimeTest,request);
```
14.16.RSS阅读器
实现一个RSS阅读器,通过输入的RSS地址来获取RSS的信息列表和查看RSS文章中的详细内容.RSS阅读器是使用了HttpWebRequest类来获取网络上的RSS信息,然后转换为自己定义好的RSS实体类对象的列表,最后绑定到页面上.
``` bash
<Page
    x:Class="App1.RssPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel>
            <TextBox Header="请输入合法的RSS阅读源的地址:" x:Name="rssURL" Text="http://www.cnblogs.com/rss"/>
            <Button Content="加载RSS" Click="Button_Click" Width="370"/>
            <ListView x:Name="listbox" SelectionChanged="listbox_SelectionChanged" Height="350"
                      >
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <Grid>
                            <Grid.RowDefinitions>
                                <RowDefinition Height="Auto"/>
                                <RowDefinition Height="Auto"/>
                                <RowDefinition Height="60"/>
                            </Grid.RowDefinitions>
                            <TextBlock Grid.Row="0" Text="{Binding Title}" FontSize="25" TextWrapping="Wrap"/>
                            <TextBlock Grid.Row="1" Text="{Binding PublishedDate}" FontSize="20"/>
                            <TextBlock Grid.Row="2" TextWrapping="Wrap" Text="{Binding PlainSummary}"
                                       FontSize="18" Opacity="0.5"/>
                        </Grid>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackPanel>
        
    </Grid>
</Page>
using App1.Models;
using App1.Services;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class RssPage : Page
    {
        public RssPage()
        {
            this.InitializeComponent();
        }
        /// <summary>
        /// 加载RSS列表
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void Button_Click(object sender, RoutedEventArgs e)
        {
            if (rssURL.Text!=null)
            {
                //调用封装好的RSS请求方法加载RSS列表
                RssService.GetRssItems(rssURL.Text, async (items) =>
                {
                    //请求正常完成,把RSS文章的内容绑定到列表控件
                    await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                    {
                        listbox.ItemsSource = items;
                    });
                },
                async (exception) =>
                {
                    //请求出现异常,把异常信息显示出来
                    await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, async () =>
                    {
                        await new MessageDialog(exception).ShowAsync();
                    });
                },null
                );
            }
            else
            {
                await new MessageDialog("请输入RSS地址").ShowAsync();
            }
        }

        //通过列表选项选中事件来跳转到查看文章详情的页面
        private void listbox_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            //列表控件的选中项
            if (listbox.SelectedItem == null)
                return;
            var template=(RssItem)listbox.SelectedItem;
            //跳转到详情页面,并且把RssItem对象作为参数传递过去
            Frame.Navigate(typeof(DetailPage), template);
            listbox.SelectedItem = null;
        }
    }
}
<Page
    x:Class="App1.DetailPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="TitlePanel" Margin="12,0,0,28">
            <TextBlock Text="Rss阅读器" FontSize="20"/>
            <TextBlock Text="{Binding Title}" FontSize="25" TextWrapping="Wrap"/>
        </StackPanel>
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <StackPanel>
                <TextBlock Text="{Binding PublishedDate}" FontSize="15" Opacity="0.5"/>
                <TextBlock Text="{Binding Url}" FontSize="15" Opacity="0.5"/>
                <ScrollViewer Height="500">
                    <TextBlock Text="{Binding PlainSummary}" FontSize="20" TextWrapping="Wrap"/>
                </ScrollViewer>
            </StackPanel>
        </Grid>
    </Grid>
</Page>
using App1.Models;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class DetailPage : Page
    {
        public DetailPage()
        {
            this.InitializeComponent();
        }
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            RssItem item=e.Parameter as RssItem;
            if (item!=null)
            {
                //把文章对象绑定到当前的页面的上下文来进行显示
                this.DataContext= item;
            }
        }
    }
}

```
14.2.网络编程之HttpClient类
对于简单的请求操作,HttpClient类提供了一个简单的接口来处理最常见的任务,并为身份验证提供了适合于大多数方案的合理的默认设置,对于较为复杂的HTTP操作,更多的功能包括,执行常见操作(DELETE,GET,PUT,POST)方法,获取,设置和删除Cookie的功能,支持常见的身份验证设置和模式,异步方法上提供的HTTP请求进度信息;访问有关传输的安全套接字层(SSL)详细信息;在高级应用中包含自定义筛选器的功能.
14.21.Get请求获取字符串和数据流数据
1.获取字符串数据
HttpClient类使用基于任务的异步模式提供了非常简化的请求操作,可以直接调用HttpClient类GetStringAsync方法便可获取网络返回的字符串数据.
``` bash
Uri uri=new Uri("http://yourwebsite.com");
HttpClient httpClient=new HttpClient();
//获取网络的返回的字符串数据
string result=await httpClient.GetStringAsync(uri);
```
使用GetStringAsync方法是一种简化的HTTP请求,如果要获取HTTP请求返回的整个对象HttpResponseMessage可以使用GetAsync方法.HttpResponseMessage对象是HTTP的相应消息对象,它包含了网络请求相应的HTTP头,数据体等信息.下面使用GetAsync方法来获取网络返回的字符串信息
``` bash
HttpResponseMessage response=await httpClient.GetAsync(uri);
string responseBody=await response.Content.ReadAsStringAsync();
```
2.获取数据流数据
HttpResponseMessage对象的Content属性表示是返回的数据对象,是一个IHttpContent类型的对象,如果要获取的是数据流数据,可以通过它的ReadAsBufferAsync方法获取到返回的IBuffer对象,或者通过ReadAsInputStreamAsync地方获取IInputStream对象,然后再转换为Stream对象
``` bash
using(Stream responseStream=(await response.Content.ReadAsInputStreamAsync()).AsStreamForRead()){
	int read=0;
	byte[] responseBytes=new Byte[1000];
	do{
	//如果read等于0表示Stream的数据以及读取完毕
	read=await responseStream.ReadAsync(responseBytes,0,responseBytes.Length);
}while(read!=0);
}
```
3.取消网络请求
HttpClient类发起的网络请求都是基于任务的异步方法,所以要取消其异步操作可以通过异步任务的取消对象CancellationTokenSource对象来取消,这点和HttpWebRequest类是不同.如果使用CancellationTokenSource对象来取消异步请求会触发TaskCanceledException异常.这个异常需要用try catch语句来捕获,便可以识别到请求是被取消的.
``` bash
private CancellationTokenSource cts=new CancellationTokenSource();
try{
//使用CancellationTokenSource对象来控制异步任务的取消操作
HttpResponseMessage response=await httpClient.GetAsync(new Uri(resourceAddress)).AsTask(cts.Token);
responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
cts.Token.ThrowIfCancellationRequested();
}catch(TaskCanceledException)
responseBody="请求被取消";	
}
//调用Cancel方法取消网络请求
if(cts.Token.CanBeCanceled){
	cts.Cancel();
}
14.22.Post请求发送字符串和数据流数据
使用HttpClient类发起Post请求的编程方式也很简洁,可以调用方法PostAsync(Uri uri,IHttpContent content)来直接向目标的地址Post数据,在该方法里面有两个参数,uri就是网络的目标地址,content是指向目标地址Post的数据对象,在Post数据之前,首先把数据初始化成一个IHttpContent对象,实现了IHttpContent接口的类有HttpStringContent类,HttpStream类和HttpBufferContent类,这三个类分别代表了字符串类型,数据流类型和二进制类型,数据流类型和二进制类型可以互相转换.调用PostAsync方法之后会返回一个HttpResponseMessage对象,通过这个HTTP的相应消息对象就可以获取Post请求之后的返回结果信息.Post请求发送字符串和数据流数据
``` bash
1.Post请求发送字符串数据
HttpStringContent httpStringContent=new HttpStringContent("hello Windows 10");
HttpResponseMessage response=await httpClient.PostAsync(uri,httpStringContent).AsTask(cts.Token);
string responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
2.Post请求发送数据流数据
HttpStreamContent streamContent=new HttpStreamContent(stream.AsInputStrem());
httpResponseMessage response=await httpClient.PostAsync(uri,streamContent).AsTask(cts.Token);
string responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
```
除了使用PostAsync方法之外,还可以使用SendRequestAsync方法来发送网络请求,SendRequestAsync方法来发送网络请求,SendRequestAsync方法既可以使用Get方式也可以使用Post方式.SendRequestAsync方法发送的消息类型是HttpRequestMessage类对象,HttpRequestMessage类表示HTTP的请求消息类,可以通过HttpRequestMessage对象设置请求的类型(Get/Post)和传输的数据对象.使用SendRequestAsync方法
``` bash
//创建HttpRequestMessage对象
HttpStreamContent streamContent=new HttpStreamContent(stream.AsInputStream());
HttpRequestMessage request=new HttpRequestMessage(HttpMethod.Post,new Uri(resourceAddress));
request.Content=streamContent;
//发送数据
HttpResponseMessage response=await httpClient.SendRequestAsync(request).AsTask(cts.Token);
string responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
```
14.23.设置和获取Cookie
Cookie是指某些网站,为了辨别用户身份,进行会话跟踪而储存在用户本地终端上的数据(通常经过加密),当在使用HTTP请求时,如果服务器返回的数据待用Cookie数据,也可以获取出来,存储在本地,下次发起HTTP请求的时候就会带上这些Cookie的数据.
在HttpClient类的网络请求中可以通过HttpBaseprotocolFilter类来获取网站的Cookie信息,HttpBaseProtocolFilter类表示是HttpClient的Http请求的基础协议的过滤器.获取Cookie的代码示例如下所示
``` bash
//创建一个HttpBaseProtocolFilter对象
HttpBaseProtocolFilter filter=new HttpBaseProtocolFilter();
//通过HttpBaseProtocolFilter对象获取使用HttpClient进行过网络请求的地址的Cookie信息
HttpCookieCollection cookieCollection=filter.CookieManager.GetCookies(new Uri(resourceAddress));
//遍历整个Cookie集合的Cookie信息
foreach(HttpCookie cookie in cookieCollection){}
```
在发送HTTP请求的时候也一样可以带上Cookie信息,如果服务器可以识别到Cookie信息就通过Cookie信息来进行一些操作.比如Cookie信息带有用户名和密码的加密信息,就可以免去登录的步骤.在HttpClient的网络请求里面,HttpCookie类表示是一个Cookie对象,创建好Cookie对象之后通过HttpBaseProtocolFilter对象的CookieManger属性来设置Cookie,然后发送网络请求,这时候的网络请求就会把Cookie信息给带上
``` bash
//创建一个HttpCookie对象,"id"标识是Cookie的名称,"localhost"是主机名,"/"是表示服务器的虚拟路径
HttpCookie cookie=new HttpCookie("id","yourwebsite.com","/");
//设置Cookie的值
cookie.Value="123456";
//设置Cookie存活的时间,如果设置为null表示只是在一个会话里面生效
cookie.Expries=new DateTimeOffset(DateTime.Now,new TimeSpan(0,1,8));
//在过滤器里面设置Cookie
HttpBaseProtocolFilter filter=new HttpBaseProtocolFilter();
bool replaced=filter.CookieManager.SetCookie(cookie,false);
...接下来可以向"yourwebsite.com"远程主机发起请求
```
14.24.网络请求的进度监控
HttpClient的网络请求是支持进度监控,通过异步任务的IProgress<T>对象可以直接监控到HttpClient的网络请求返回的进度信息,返回的进度对象是HttpProgress类对象,在进度对象HttpProgress里面包含了下面的一些信息:Stage(当前的状态),BytesSent(已发送的数据大小),BytesReceived(已接收的数据大小),Retries(重试的次数),TotalBytesToSend(总共需要发送的数据大小)和TotalBytesToReceive(总共需要接收的数据大小).网络请求进度监控的代码示例如下所示
``` bash
//创建IProgress<HttpProgress>对象
IProgress<HttpProgress> progress=new Progress<HttpProgress>(ProgressHandler);
//在异步任务中加入进度监控
HttpResponseMessage response=await httpClient.PostAsync(new Uri(resourceAddress),streamContent).AsTask(cts.Token,progress);
//进度监控的回调方法
private void ProgressHandler(HttpProgress progress){
	//这里可以通过progress参数获取进度的相关信息
}

```
14.25.自定义HTTP请求筛选器
HTTP请求筛选器是HttpClient网络请求的一个很强大的功能,它可以把每次网络请求需要的规则封装起来作为一个公共的筛选器来使用,使得特定连接和安全方案的Web请求变得更加简单.我们可以把身份验证,数据加密,连接失败后使用自动重试等逻辑封装在筛选器里面,然后再使用筛选器来初始化一个HttpClient对象进行网络请求.
通常情况下,处理请求期间预期可能会出现的一个网络或安全状况很容易,但要处理多个网络或安全状况可能就比较困难.你可以创建一些简单的筛选器,然后再根据需要将它们链接起来,这样就能够针对预期可能会出现的复杂情况开发出一些Web请求功能,而无须开发非常复杂的程序.
HttpClient是用于通过HTTP发送和接收请求的主类,它使用HttpBaseProtocolFilter类来确定如何发送和接收数据,所以HttpBaseProtocolFilter在逻辑上是所有自定义筛选器链的结尾.每个HttpClient实例都可以有一个不同的筛选器链或管道.
若要编写一个自定义筛选器,需要创建一个自定义的筛选器类实现IHttpFilter接口,通过IHttpFilter.SendRequestAsync方法来指定筛选器的工作方式,也就是把网络请求封装的信息放在该方法里面,在发起网络请求的时候筛选器内部会调用该方法.下面来看一个向HTTP请求和响应添加自定义标头的筛选器的示例代码
//创建一个自定义筛选器,使用该筛选器会在Http请求和响应中都添加一个自定义的Http头信息
``` bash
public class plugInFilter:IHttpFilter{
	private IHttpFilter innerFilter;
	public PlugFilter(IHttpFilter innerFilter){
	if(innerFilter==null){
	throw new ArgumentException("innerFilter cannot be null");
	}
	this.innerFilter=innerFilter;
}
//在SendRequestAsync方法里面添加自定义的HTTP头
public IAsyncOperationWithProgress<HttpResponseMessage,HttpProgress>SendRequestAsync(HttpRequestMessage request){
	return AsyncInfo.Run<HttpResponseMessage,HttpProgress>(async(cancellationToken,progress)=>{
	//添加请求头
	request.Headers.Add("Custom-header","CustomRequestValue");
	HttpResonseMessage response=await innerFilter.SendRequestAsync(request).AsTask(cancellationToken,progress);
	cancellationToken.ThrowIfCancellationRequested();
	//添加响应头
response.Headers.Add("Custom-header","CustomResponseValue");
	return response;
});
}
public void Disponse(){
	innerFilter.Disponse();
	GC.SuppressFinalize(this);
}
}
```
若要使用此筛选器,在创建HttpClient对象时将其接口传递到HttpClient(IHttpFilter)构造方法里,若要设置筛选器链,请将新筛选器链接到之前的筛选器以及位于该链结尾处的HttpBaseProtocolFilter对象.下面使用PlugInFilter筛选器来创建HttpClient对象
``` bash
//先创建一个HttpBaseProtocolFilter对象,这是因为HttpClient默认的最底层的筛选器
var basefilter=new HttpBaseProtocolFilter();
//创建PlugInFilter筛选器对象,链接到HttpBaseProtocolFilter对象上
var myfilter=new PlugInFilter(basefilter);
//使用自定义的筛选器创建HttpClient对象
HttpClient httpClient=new HttpClient(myfilter);
...下面使用httpClient对象来发起网络请求都会自动带上自定义筛选器所添加的HTTP头.
```
14.26.部署IIS服务和实现客户端对服务器的请求
首先需要创建一个Asp.net的网站服务,并在本地的IIS服务上把网站部署好,作为后台的网络服务,然后再创建一个Win10客户端应用程序向后台的网络服务发起请求.创建一个Asp.net项目命名为website,创建一个default.apsx用于处理Get和Post数据请求的测试.
``` bash
<Page
    x:Class="App1.WebPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid Margin="12,0,12,0">
            <StackPanel>
                <Button Content="Get String" Click="Button_Click" Width="370"/>
                <Button Content="Get Stream" Click="Button_Click_1" Width="370"/>
                <Button Content="Post String" Click="Button_Click_2" Width="370"/>
                <Button Content="Post Stream" Click="Button_Click_3" Width="370"/>
                <Button Content="请求进度监控" Click="Button_Click_4" Width="370"/>
                <Button Content="Cookie设置" Click="Button_Click_5" Width="370"/>
                <Button Content="Cookie获取" Click="Button_Click_6" Width="370"/>
            </StackPanel>
        </Grid>
        <!--操作的进度,网络信息的展示面板,一开始隐藏,发起网络操作时显示出来,完成后再隐藏-->
        <Grid Grid.RowSpan="2" x:Name="waiting" Visibility="Collapsed">
            <Grid Background="Black" Opacity="0.8">
            </Grid>
            <StackPanel Background="Black" HorizontalAlignment="Center" VerticalAlignment="Center"
                        Grid.RowSpan="2">
                <TextBlock x:Name="information" Text="正在请求数据... ..." FontSize="30" TextWrapping="Wrap"/>
                <Button Content="取消操作" x:Name="cancel" Click="cancel_Click" Width="370"/>
            </StackPanel>
        </Grid>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;

using System.Runtime.InteropServices.WindowsRuntime;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Security.Cryptography;
using Windows.Storage.Streams;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;
using Windows.Web.Http;
using Windows.Web.Http.Filters;







// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class WebPage : Page
    {
        public WebPage()
        {
            this.InitializeComponent();
            //初始化HTTP请求对象
            httpClient = new HttpClient();
            //初始化异步任务取消对象
            cts = new CancellationTokenSource();
        }
        //服务器地址
        private string server = "http://localhost:8081/default.aspx";
        //HTTP请求对象
        private HttpClient httpClient;
        //异步任务取消对象
        private CancellationTokenSource cts;
        private async void HttpRequestAsync(Func<Task<string>> httpRequestFuncAsync) {
            string responseBody;
            waiting.Visibility = Visibility.Visible;
            try
            {
                responseBody = await httpRequestFuncAsync();
                cts.Token.ThrowIfCancellationRequested();
            }
            catch (TaskCanceledException)
            {
                responseBody = "请求被取消";

            }
            catch (Exception ex)
            {
                responseBody = "异常消息" + ex.Message;
            }
            finally {
                waiting.Visibility = Visibility.Collapsed;
            }
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, async () =>
            {
                await new MessageDialog(responseBody).ShowAsync();
            });
        }
        //Get请求获取string类型数据
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () =>
            {
                string resourceAddress = server + "?cacheable=1";
                HttpResponseMessage response = await httpClient.GetAsync(new Uri(resourceAddress));
                string responseBody = await response.Content.ReadAsStringAsync().AsTask(cts.Token);
                return responseBody;
            });
        }
        //get请求获取Stream类型数据
        private void Button_Click_1(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () => {
                string resourceAddress = server + "?extraData=2000";
                StringBuilder responseBody = new StringBuilder();
                HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get,new Uri(resourceAddress));
                HttpResponseMessage response = await httpClient.SendRequestAsync(request, HttpCompletionOption.ResponseHeadersRead).AsTask(cts.Token);
                using (Stream responseStream=(await response.Content.ReadAsInputStreamAsync()).AsStreamForRead())
                {
                    int read = 0;
                    byte[] responseBytes = new byte[1000];
                    do
                    {
                        read = await responseStream.ReadAsync(responseBytes,0,responseBytes.Length);
                        responseBody.AppendFormat("Bytes read from stream:{0}",read);
                        responseBody.AppendLine();
                        //把byte[]转换为IBuffer类型,IBuffer接口是Win10标准的二进制数据接口
                        IBuffer responseBuffer=CryptographicBuffer.CreateFromByteArray(responseBytes);
                        responseBuffer.Length = (uint)read;
                        //转换为Hex字符串,ASCII文本这些记录由对应机器语言码和/或常量数据的十六进制编码数字组成
                        responseBody.AppendFormat(CryptographicBuffer.EncodeToHexString(responseBuffer));
                        responseBody.AppendLine();
                    } while (read!=0);
                }
                return responseBody.ToString();
            });
        }
        /// <summary>
        /// Post请求发送String类型数据
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_2(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () =>
            {
                string resourceAdderss = server;
                string responseBody;
                HttpResponseMessage response = await httpClient.PostAsync(new Uri(resourceAdderss), new HttpStringContent("hello Windows Phone")).AsTask(cts.Token);
                responseBody= await response.Content.ReadAsStringAsync().AsTask(cts.Token);
                return responseBody;
            });
        }
        /// <summary>
        /// Post 请求发送Stream类型数据
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_3(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () =>
            {
                string resourceAddress = server;
                string responseBody;
                const int contentLegnth = 1000;
                //使用Stream数据初始化一个HttpStreamContent对象
                Stream stream = GenerateSampleStream(contentLegnth);
                HttpStreamContent streamContent = new HttpStreamContent(stream.AsInputStream());
                //初始化一个Post类型的HttpReqeustMessage对象
                HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Post, new Uri(resourceAddress));
                request.Content = streamContent;
                //发送Post请求
                HttpResponseMessage response=await httpClient.SendRequestAsync(request).AsTask(cts.Token);
                //获取请求的结果
                responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
                return responseBody;
            });
        }
        /// <summary>
        /// 获取测试的数据流对象
        /// </summary>
        /// <param name="contentLegnth"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private Stream GenerateSampleStream(int size)
        {
            byte[] subData= new byte[size];
            for (int i = 0; i < subData.Length; i++)
            {
                //ASCII编码40表示是字符“（”
                subData[i] = 40;
            }
            return new MemoryStream(subData);
        }
        /// <summary>
        /// 监控Post请求的进度情况
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_4(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () => { 
                string resourceAddress = server;
                string responseBody;
                const uint streamLength = 1000000;
                HttpStreamContent streamContent = new HttpStreamContent(new SlowInputStream(streamLength));
                streamContent.Headers.ContentLength = streamLength;
                //创建进度对象
                IProgress<HttpProgress> progress= new Progress<HttpProgress>(ProgressHandler);
                HttpResponseMessage response=await httpClient.PostAsync(new Uri(resourceAddress),streamContent).AsTask(cts.Token);
                responseBody = "完成";
                return responseBody;
            });
        }

        private void ProgressHandler(HttpProgress progress)
        {
            string infoString = "";
            infoString = progress.Stage.ToString();
            //需要发送的数据
            ulong totalBytesToSend = 0;
            if (progress.TotalBytesToSend.HasValue)
            {
                totalBytesToSend = progress.TotalBytesToSend.Value;
                infoString += "发送数据:" + totalBytesToSend.ToString(CultureInfo.InvariantCulture);
            }
            //已接收的数据
            ulong totalBytesToReceive = 0;
            if (progress.TotalBytesToReceive.HasValue) { 
                totalBytesToReceive= progress.TotalBytesToReceive.Value;
                infoString += "接收数据:" + totalBytesToReceive.ToString(CultureInfo.InvariantCulture);
            }
            double requestProgress = 0;
            //前面50%是发送进度,后面50%是接收进度
            if (progress.Stage==HttpProgressStage.SendingContent && totalBytesToSend>0)
            {
                requestProgress = progress.BytesSent * 50 / totalBytesToSend;
                infoString += "发送进度";
            }
            else if (progress.Stage==HttpProgressStage.ReceivingContent)
            {
                requestProgress += 50;
                if (totalBytesToReceive>0)
                {
                    requestProgress += progress.BytesReceived * 50 / totalBytesToReceive;
                }
                infoString += "接收进度:";
            }
            else
            {
                return;
            }
            infoString += requestProgress;
            information.Text= infoString;

        }
        /// <summary>
        /// 设置网络请求的Cookie
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_5(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () => {
                string resourceAddress = server;
                string responseBody;
                //创建一个HttpCookie对象,"id"表示是Cookie的名称,"localhost"是主机名,"/"是表示服务器的虚拟路径
                HttpCookie cookie = new HttpCookie("id","localhost","/");
                cookie.Value = "123456";
                //设置为null表示只是在一个会话里面生效
                cookie.Expires = null;
                HttpBaseProtocolFilter filter= new HttpBaseProtocolFilter();
                bool replaced = filter.CookieManager.SetCookie(cookie,false);
                HttpResponseMessage response = await httpClient.PostAsync(new Uri(resourceAddress),new HttpStringContent("hello Windows Phone")).AsTask(cts.Token);
                responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
                return responseBody;
            });
        }
        /// <summary>
        /// 获取网络请求的Cookie
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_6(object sender, RoutedEventArgs e)
        {
            HttpRequestAsync(async () =>
            {
                string resourceAddress = server+"?setCookies=1";
                string responseBody;
                //发送网络请求
                HttpResponseMessage response=await httpClient.GetAsync(new Uri(resourceAddress)).AsTask(cts.Token);
                responseBody=await response.Content.ReadAsStringAsync().AsTask(cts.Token);
                cts.Token.ThrowIfCancellationRequested();
                //获取基础协议筛选器
                HttpBaseProtocolFilter filter= new HttpBaseProtocolFilter();
                //获取网络请求下载到的Cookie数据
                HttpCookieCollection cookieCollection = filter.CookieManager.GetCookies(new Uri(resourceAddress));
                //遍历Cookie数据的内容
                responseBody = cookieCollection.Count + "cookies:";
                foreach (HttpCookie cookie in cookieCollection)
                {
                    responseBody += "Name:" + cookie.Name + "";
                    responseBody += "Domain:" + cookie.Domain + "";
                    responseBody += "Path:" + cookie.Path + "";
                    responseBody += "Value:" + cookie.Value + "";
                    responseBody += "Expries:" + cookie.Expires + "";
                    responseBody += "Secure:" + cookie.Secure + "";
                }
                return responseBody;
            });
        }
        /// <summary>
        /// 取消网络请求
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void cancel_Click(object sender, RoutedEventArgs e)
        {
            cts.Cancel();
            cts.Dispose();
            cts=new CancellationTokenSource();
        }
    }
}

```
14.3.使用WebService进行网络编程
WebService是构建互联网分布式系统的基本部件.
WebService是一种标准化的实现网络服务和异构程序之间方法调用的机制.是为了使原来各孤立的站点之间的信息能够相互通信,共享而提出的一种接口,WebService也叫做Xml Web Service.可以接纳从Internet或Intranet上的其他设备中传送的请求,是一种轻量级的独立的通信技术,这种技术通过SOAP在Web上提供软件服务,运用WSDL文件停止阐明,并通过UDDI停止注册,WebService所使用的是Internet上统一,开放的标准,如HTTP,XML,SOAP,WSDL等,所以WebService可以在任何支持这些标准的环境中使用.它通过XML格式的文件来描述方法,参数,调用和返回值,这种格式的XML文件称为WSDL.WebService采用的通信协议是SOAP.SOAP协议是一个用于分散和分布式环境下网络信息交换的基于XML的通信协议,在此协议下,软件组件或应用程序能够通过标准的HTTP协议进行通信,它的设计目标就是简单性和扩展性,这有助于大量异构程序和平台之间的互操作,从而使存在的应用程序能够被用户广泛访问.
XML:扩展性可标志言语.面向短期的暂时数据处理,面向万维网络,是SOAP的根底.
SOAP:简单对象存取协议.是XML Web Service的通信协议调用用户建立的Web服务中的一个或多个操纵.SOAP是XML文档方式的调用方法的标准,它可以支撑不同的底层接口,像HTTP(S)或SMTP.
WSDL:网络描述语言.WSDL文件是一个XML文档,用于阐明一组SOAP音讯以及如何交流这些音讯.
UDDI:统一描述,发现和集成协议,是一个次要针对Web服务供应商和运用者的新项目.在用户可以调用Web服务之前,必须肯定这个服务内包括哪些商务办法,找到被调用的接口定义,还要在服务端来编制软件,UDDI是一种依据描绘文档来指导设备查找相应服务的机制.UDDI应用SOAP音讯机制(标准的XML/HTTP)来公布,编辑,阅读以及查找注册音讯.它采用XML格式来封装各种不同类型的数据,并且发送到注册核心前往需求的数据.
14.32.手机号码归属地查询
在实例中会使用到手机号码归属地查询WebService接口:http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx这个WebService接口是http://www.webxml.com.cn网站提供的一个免费的WebService接口,可以在应用程序里使用它实现一些功能.
1.接口的方法
通过getMobileCodeInfo获得国内手机号码归属地省份,地区和手机卡类型信息
2.输入参数
mobileCode=字符串(手机号码,最少前7位数字),userID=字符串(商业用户ID)免费用户为空字符串;返回数据:字符串(手机号码:省份城市手机卡类型)
3.返回的信息
``` bash
<?xml version="1.0" encoding="UTF-8">
<string xmlns="http://WebXml.com.cn/"></string>
```
1.在项目中引入WebService服务
创建一个Win10项目工程,在工程中添加webservice的引用,将webservice服务加入,这时生成了上述web服务在本地的一个代理.
2.调用WebService
``` bash
<Page
    x:Class="App1.MobileCodePage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel>
            <TextBlock x:Name="des" Text="请输入你需要查询的手机号码"/>
            <TextBox x:Name="No" Text=""/>
            <Button Content="查询" Name="search" Click="search_Click"/>
            <TextBlock x:Name="information" Text=""/>
        </StackPanel>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class MobileCodePage : Page
    {
        public MobileCodePage()
        {
            this.InitializeComponent();
        }

        private async void search_Click(object sender, RoutedEventArgs e)
        {
            //实例化一个webservice代理的对象
            MobileCodeService.MobileCodeWSSoapClient proxy=new MobileCodeService.MobileCodeWSSoapClient();
            //调用webservice的方法获取电话号码的归属地
            information.Text = await proxy.getMobileCodeInfoAsync(No.Text,"");
        }
    }
}

```
14.4.使用WCF Service进行网络编程
WCF是微软为构建面向服务的应用提供的分布式通信编程框架.使用该框架,开发人员可以构建跨平台,安全，可靠和支持事务处理的企业级互联应用解决方案.WCF是建立在.Net framework上的类型集合,整合了微软分布式应用程序开发中的众多成熟技术,如Enterprise Sevices(COM+),.Net Remoting,Web Service(ASMX),WSE和MSMQ消息队列,并且存在于微软WIndows操作系统上,在面向服务的世界和面向对象的世界里起着桥梁的作用.
1.WCF的通信范围:可以跨进程,跨机器,跨子网,企业网乃至于Internet.
2.WCF的宿主:可以是ASP.NET(IIS或WAS),EXE,WPF,Windows Forms,NT Service,COM+
3.WCF的通信协议:TCP,HTTP,跨进程以及自定义.
14.42.创建WCF Service
1.创建一个WCF服务应用程序
2.修改Service1.svc和IService1.cs文件
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Web;
using System.Text;

namespace WcfService1
{
    // 注意: 使用“重构”菜单上的“重命名”命令，可以同时更改代码和配置文件中的接口名“IService1”。
    [ServiceContract]
    public interface IService1
    {

        [OperationContract]
        string GetData(int value);

        [OperationContract]
        CompositeType GetDataUsingDataContract(CompositeType composite);

        // TODO: 在此添加您的服务操作
    }


    // 使用下面示例中说明的数据约定将复合类型添加到服务操作。
    [DataContract]
    public class CompositeType
    {
        bool boolValue = true;
        string stringValue = "Hello ";

        [DataMember]
        public bool BoolValue
        {
            get { return boolValue; }
            set { boolValue = value; }
        }

        [DataMember]
        public string StringValue
        {
            get { return stringValue; }
            set { stringValue = value; }
        }
    }
}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Web;
using System.Text;

namespace WcfService1
{
    // 注意: 使用“重构”菜单上的“重命名”命令，可以同时更改代码、svc 和配置文件中的类名“Service1”。
    // 注意: 为了启动 WCF 测试客户端以测试此服务，请在解决方案资源管理器中选择 Service1.svc 或 Service1.svc.cs，然后开始调试。
    public class Service1 : IService1
    {
        public string GetData(int value)
        {
            return string.Format("You entered: {0}", value);
        }

        public CompositeType GetDataUsingDataContract(CompositeType composite)
        {
            if (composite == null)
            {
                throw new ArgumentNullException("composite");
            }
            if (composite.BoolValue)
            {
                composite.StringValue += "Suffix";
            }
            return composite;
        }
    }
}
创建好的项目默认文件目录如上所示.在Service1.svc中添加方法:
public string HelloWCF(){
	return "Hello WCF";
}
在IService1.cs中添加接口:
[OperationContract]
string HelloWCF();
```
3.建一个网站的虚拟目录,指向WCF的项目工程
在控制面板中找到IIS并打开,在Default Web Site节点下创建一个虚拟目录,命名为wcf,路径指向本例子的web应用程序的代码,并单击确定按钮,右键刚刚建好的虚拟目录wcf,转换为应用程序,然后在win10中启用WCF相关的服务.
4.打开浏览器输入地址http://localhost/wcf/Service1.svc
14.43.调用WCF Service
1.在win10项目中添加WCF服务引用
2.调用WCF服务
``` bash
 <TextBlock x:Name="tb1" Text="" FontSize="25"/>
  ///WCF Service
           WcfService.Service1Client proxy=new WcfService.Service1Client();
            tb1.Text = await proxy.HelloWCFAsync();
```
14.5.推送通知
推送通知:第三方程序与推送通知的服务器保持连接,等有新内容需要提供给设备后,推送通知的服务器就会将数据推送到设备上.推送通知是一个统一的通知服务,使用推送通知服务可以确保用户得到最新信息.很多类型的程序都可以使用这个服务.该服务会维持一个持久的IP连接从而在程序没有运行时也能通知用户.
14.51.推送通知的原理和工作方式
win10中的推送通知服务是一款异步,尽力型服务,可向第三方开发人员提供一个采用高效节能的方法将数据从云服务发送到Win10应用的通道.开发者可以利用Win10提供的推送通知的服务,来实现网络的服务器端向Win10客户端程序推送一些通知或者消息
推送通知涉及的3个重要的服务:
1.Web Service(云端服务)
这是通知消息的出发点,也就是你要推送什么样的通知,什么内容的消息,就是从这里提供的,使用推送通知的应用程序需要通过Microsoft Push Notification Service注册一个唯一的Channel,然后把这个唯一的Channel告诉云端服务,这时候云端服务就可以将消息搭载这个唯一的Channel,然后把这个唯一的Channel告诉云端服务,这时候云端服务就可以将消息搭载这个唯一的Channel,通过Windows Push Notification Service传送到Windows 10的客户端应用程序.
2.Windows Push Notification Service(微软提供的推送通知服务)
推送通知的中介角色,为Win10客户端和服务端的交流提供了一条特殊的通道,一种情况是,它接受Win10应用程序通过Push Client创建的Channel来作为整个推送通知过程的通道,另一种情况是,它也接受云端服务所申请的Service Name来进行注册,让Push Client在建立Channel时指定云端服务所注册的Service.
3.Push Client(Win10推送通知的客户端)
这是推送通知在Win10系统里面的客户端的支持,直接跟设备客户端打交道,Push Client要取得资料的话,则需要向Windows Push Notification Service建立起独有的Channel,因此Push Client会向Windows Push Notification Service送出询问是否存在指定的Service Name与专用的Channel名称.
整个推送通知的工作流程
1.你的应用从推送客户端服务请求推送通知URI.
2.推送客户端服务与Windows推送通知服务(WNS)通信并且WNS向推送客户端服务返回一个通知URI.
3.推送客户端服务向你的应用返回通知URI.
4.应用向云服务发送通知URI.
5.当云服务要向应用发送信息时,它将使用通知URI向WNS发送推送通知.
6.MPNS将推送通知路由到应用.
14.52.推送通知的分类
4种不同类型的通知
分别是原生通知(Raw Notification),吐司通知(Toast Notification),磁贴通知(Tile Notification)和徽章通知(Badge Notification).
1.原生通知(Raw Notification)
原生通知是一种只针对正在运行的应用程序而提供的通知,如果使用原生通知的应用程序并没有运行,而服务器端又给应用程序发送了消息,这一条原生通知将会被微软的推送通知服务所丢弃.原生通知一般是用于给正在运行的应用程序发送消息
原生通知的特点:
1.可以发送任何格式的数据,有效的载荷最大为1KB.
2.只有在使用原生通知运行的情况下才能接收到消息
3.允许在用户使用时更新用户界面
原生通知的传送格式可以为任意的字符串格式.
2.吐司通知(Toast Notification)
吐司通知是一种直接在屏幕最上弹出来的系统提示栏通知,一般是用于一些比较重要的通知提示
吐司通知的特点:
1.发送的数据为指定的Xml格式
2.如果程序正在运行,内容发送到应用程序中.
3.如果程序不在运行,弹出Toast消息框显示消息
4.会临时打断用户的操作.
5.消息的内容为应用程序图表加上两个标题描述,标题为粗体字显示的字符串,副标题为非粗体字显示的字符串,也可以只显示内容;
6.用户可以点击消息进行跟踪
在使用吐司通知之前,我们需要确保在项目配置文件Package.appxmanifest已经设置支持吐司通知.设置的方式为把uap:VisualElements节点的ToastCapable属性设置为"true".
``` bash
<uap:VisualElements DisplayName="PushNotificationDemo"
Square150x150Logo="Assets/Logo.png"
... ...
ToastCapable="true"
/uap:VisualElements>
```
吐司通知的传送格式如下,关于吐司通知格式更加详细的说明可以参考Win10吐司通知格式说明
``` bash
<toast>
	<visual>
		<binding template="ToastText02">
			<text id="1">headlineText</text>
			<text id="2">bodyText</text>		
</binding>
	</visual>
</toast>
```
3.磁贴通知(Tile Notification)
磁贴通知是一种针对桌面中的应用程序提供的通知,如果用户并没有把应用程序的磁贴添加在桌面,那么应用程序是不会接收到磁贴通知的.
磁贴通知的传送格式如下:
``` bash
<tile>
<visual version="3">
<binding template="TileWide310x150IconWithBadgeAndText">
<image id="1" src=""/>
<text id="1"/>
<text id="2"/>
<text id="3"/>
</binding>
</visual>
</tile>
```
4.徽章通知(Badge Notification)
徽章通知是在磁贴右上角的数字通知,通常用于表示应用程序未读消息数量或者新消息数量这种类型的信息.
例如未读短信在短信图标右上角显示就是徽章通知的表现形式.
``` bash
<badge value=""3"/>
```
14.53.推送通知的发送机制
在Window10应用程序使用推送通知的服务之前,必须要使用开发者账号在提交应用的页面上注册应用程序所对应的推送通知服务,通过注册可获取3个很重要的信息,分别是应用的标识值,程序包安全标识符和密钥,注册推送通知服务
1.应用的标识符
应用的标识值为"<Identity Name="229Geek-Space.PushNotificationDemoeeee Publisher="CN=748B11E2-8CD3-41F4-9670-D945180F31FC">",当获取到该标识值的时候,需要把使用该推送通知的Windows10应用程序的清单文件Package.appxmanifest里面的Identity节点改为获取的内容.
2.程序包安全标识符
程序包安全标识符:"ms-app://s-1-15-2-4017......",在推送通知的云服务中需要使用到
3.密钥
密钥:"dmKrq... ..."，在推送通知的云服务中需要使用到
推送通知的发送机制
1.请求和接收访问令牌
将HTTP请求发送至推送通知服务以对云服务进行验证,然后反过来检索访问令牌,通过使用安全套接字层(SSL)将请求发布至微软提供的完全限定的域名(https://login.live.com/accesstoken.srf).
云服务(也就是服务后台)在HTTP请求正文中提交了这些所需参数,采用的格式为"application/x-www-form-0urlencoded".必须确保所有参数进行URL编码,在URL编码中有4个参数,
1.grant_type:必须设置为"client_credentials"
2.client_id:向应用商店注册应用时已分配的云服务程序包安全标识符(SID)
3.client_secret:向应用商店注册应用时已分配的云服务密钥.
4.scope:必须设置为"notify.windows.com".
``` bash
grant_type=client_credentials&client_id=...&client_secrt=......&scope=...
```
云服务通过一个使用"application/x-www-for-urlencoded"格式的HTTPS身份验证请求提供它的凭据(程序包安全标识符和客户端密钥).
``` bash
POST /acceddtoken.srf HTTP/1.1
Content-Type:application/x-www-form-urlencoded
Host:https://login.live.com
Content-Length:211
```
推送通知服务随即向你的服务器发送对身份验证请求的响应.如果响应代码为"200 OK".则身份验证成功,响应包含一个访问令牌,云服务器必须保存这个令牌,并且用在它发送的任何通知中,直到该访问令牌过期.
``` bash
HTTP/1.1 200 OK
Cache-Control:no-store
Content-Length:422
Content-Type:application/json
{
	"access_token":"",
	"token_type":"bearer"
}
```
其中,access_token表示云服务在发送通知时使用的访问令牌,token_type始终作为"bearer"返回.
2.发送通知请求和接收响应
调用应用发送通知请求时,会通过SSL发出HTTP请求,将该请求发送至信道统一资源标识符.
"Content-Length"是标准的HTTP标头,必须在请求中指定.所有其他标准标头可选,或i不受支持.
另外此处所列的自定义请求头可用在通知请求中,某些头必需,而其他头可选,这些请求头说明如下:
1.Authorization(必须):标准HTTP授权头用于对通知请求进行验证.云服务在此头中提供了其访问令牌.格式为Authorization:Bearer<access-token>,字符串文字"Bearer",后面是空格,再后面是你的访问令牌.通过发布上述的访问令牌请求检索此访问令牌,同一访问令牌可用于后续通知请求中,直至该令牌过期.
2.Content-Type(必填):标准HTTP授权头,对于吐司,磁贴，徽章通知,此标头应设置为"text/xml".对于原生通知,此标头应设置为"application/octet-stream".
3.Content-Length(必填):表示请求负载大小的标准HTTP授权头.
4.X-WNS-Type(必须):定义负载中的通知类型:磁贴,吐司,徽章或原生通知,这些是推送通知服务支持的通知类型.此头表示通知类型及推送通知服务处理该通知时应采用的方式.当通知到达客户端后,针对此指定的类型验证实际的负载,X-WNS-Type的格式为"X-WNS-Type:wns/toast/wns/badge/wns/tile/wns/raw"，按照顺序分别表示吐司,徽章,磁贴和原生通知.
5.X-WNS-Cache-Policy(可选):启用或禁用通知缓存.仅应用于磁贴,徽章和原生通知,设置这个格式为”X-WNS-Cache-Policy:cache|no-cache“.当通知目标设备处于脱机状态时,推送通知服务会为每个应用缓存通知.如果启用了通知循环,至多会缓存5个磁贴通知.如果启用了原始通知缓存,则将会缓存一个原始通知.项不会无期限的保留在缓存中,它们会再一段适度长的时间后丢弃,否则,下次联机时,将传递缓存内容.
6.X-WNS-RequestForStatus(可选):通知响应中的请求设备状态和推送通知服务连接状态.X-WNS-RequestForStatus的格式为"X-WNS-RequestForStatus:true|false",true表示返回响应中的设备状态和通知状态,false是默认值.
7.X-WNS-Tag(可选):用于为通知提供识别标签,用作支持通知队列的磁贴的字符串,此头仅应用于磁贴通知.X-WNS-Tag的格式为:"X-WNS-Tag:<string value>",string value表示不超过16个字符的字母数字字符串.
8.X-WNS-TTL(可选):指定生存时间(TTL)的整数值(用秒数表示).X-WNS-TTL:的格式为"X-WNS-TTL:<integer value>",integer value表示接收请求后的通知生存期跨度(以秒为单位).
上面的是发送HTTP的请求头,请求之后会获取到HTTP的响应.HTTP响应码200 OK表示通知发送成功,失败的响应码有400(错误的请求),401(未授权),403(已禁止),404(未找到),405(方法不允许).
除了响应码外,响应头也会带上相关的信息:
1.X-WNS-Debug-Trace(可选):报告问题时应记录用于帮助解决问题的调试信息.
2.X-WNS-DeviceConnectionStatus(可选):设备状态,仅当通过X-WNS-RequestForStatus头在通知请求中请求时返回.
3.X-WNS-Error-Description(可选):应记录用于帮助调试的人工可读错误.
4.X-WNS-Msg-ID(可选):通知的唯一标识符,用于调试目的,报告问题时,应记录此信息以有助于故障诊断.
5.X-WNS-NotificationStatus(可选):指示推送通知服务是否成功接收通知并处理通知,报告问题时,应记录此信息以有助于故障诊断.
下面给出使用Windows窗体应用程序发送推送通知的示例:
``` bash
[DataContract]
public class OAuthToken{
[DataMember(Name="access_token")]
public string AccessToken{get;set;}
[DataMember(Name="token_type")]
public string TokenType{get;set;}
}
using App1.Models;
using System;
using System.IO;
using System.Net;
using System.Runtime.Serialization.Json;
using System.Text;

namespace App1.Behaviors
{
    public class OAuthTokenHelper
    {

        public OAuthToken GetAccessToken(string secret, string sid) {
            var urlEncodedSecret = UrlEncode(secret);
            var urlEncodedSid = UrlEncode(sid);
            //拼接请求的参数
            var body = String.Format("grant_type=client_credentials&client_id={0}&client_secret={1}&scope=notify.windows.com",urlEncodedSid,urlEncodedSecret);
            string response;
            //发起网络请求获取access-token
            using (WebClient client=new WebClient())
            {
                client.Headers.Add("Content-Type","application/x-www-form-urlencoded");
                response = client.UploadString("https://login.live.com/accesstoken.srf",body);
            }
            return GetOAuthTokenFromJson(response);
        }


        /// <summary>
        /// json字符串转换为对象的方法
        /// </summary>
        /// <param name="jsonString"></param>
        /// <returns></returns>
        private OAuthToken GetOAuthTokenFromJson(string jsonString)
        {
            using (var ms=new MemoryStream(Encoding.Unicode.GetBytes(jsonString)))
            {
                var ser = new DataContractJsonSerializer(typeof(OAuthToken));
                var oAuthToken=(OAuthToken)ser.ReadObject(ms);
                return oAuthToken;
            }
        }
        /// <summary>
        /// Url参数序列化的方法
        /// </summary>
        /// <param name="secret"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private object UrlEncode(string str)
        {
           StringBuilder sb=new StringBuilder();
            byte[] byStr= Encoding.UTF8.GetBytes(str);
            for (int i = 0; i < byStr.Length; i++)
            {
                sb.Append(@"%" + Convert.ToString(byStr[i],16));
            }
            return sb.ToString();
        }
    }
}
<Page
    x:Class="App1.NotificationPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel Grid.Row="0" Orientation="Horizontal">
            <TextBlock Text="通知地址" Margin="0,0,30,0"/>
            <TextBox x:Name="NotiAddress" Text="" Width="300"/>
        </StackPanel>
        <StackPanel Grid.Row="1" Orientation="Horizontal">
            <TextBlock Text="通知内容" Margin="0,0,30,0"/>
            <TextBox x:Name="NotiContent" Text="" Width="300"/>
        </StackPanel>
        <StackPanel Grid.Row="2" Orientation="Horizontal">
            <TextBlock Text="通知状态" Margin="0,0,30,0"/>
            <RadioButton x:Name="radioButton1" IsChecked="True" Content="Raw通知"/>
            <RadioButton x:Name="radioButton2" Content="Tile通知"/>
            <RadioButton x:Name="radioButton3" Content="Toast通知"/>
            <RadioButton x:Name="radioButton4" Content="badge通知"/>
        </StackPanel>
        <StackPanel Grid.Row="3" Orientation="Horizontal" HorizontalAlignment="Center">
            <Button Content="发送通知" Click="Button_Click" Width="120"/>
        </StackPanel>
        <StackPanel Grid.Row="4">
            <TextBlock Text="当前的状态" Margin="150,0,20,0"/>
            <TextBlock x:Name="tb2" Text=""/>
        </StackPanel>
    </Grid>
</Page>
using App1.Behaviors;
using App1.Models;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Runtime.InteropServices.WindowsRuntime;
using System.Text;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class NotificationPage : Page
    {
        public NotificationPage()
        {
            this.InitializeComponent();
        }
        /// <summary>
        /// 发送通知
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            sendNotificationType(NotiContent.Text,NotiAddress.Text);
        }
        /// <summary>
        /// 发送推送通知
        /// </summary>
        /// <param name="text1"></param>
        /// <param name="text2"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void sendNotificationType(string message, string notifyUrl)
        {
            //程序包安全标识符(SID)
            string sid = "ms-app://s-1-15-2-4017463433-3104818020-3212661602-2100054673-1509338986-1481803562-2878777805";
            //客户端密钥
            string secret = "dmKrqkwpNF1Bd1L0RDTW1AWkxotlwsqu";//"bs08Acs1RG7jB7pkGVMh8EmGKCG3pH+3";
            OAuthTokenHelper oauth = new OAuthTokenHelper();
            //获取访问的令牌
            OAuthToken token = oauth.GetAccessToken(secret,sid);
            try
            {
                //创建Http对象
                HttpWebRequest myReqeuest = (HttpWebRequest)WebRequest.Create(notifyUrl);
                //toast,tile,badge为text/xml;raw为application/octet-stream
                myReqeuest.ContentType = "text/xml";
                //设置access-token;
                myReqeuest.Headers.Add("Authorization",String.Format("Bearer {0}",token.AccessToken));
                string message2 = "test";
                if ((bool)radioButton1.IsChecked)
                {
                    message2 = message;
                    //推送raw消息
                    myReqeuest.Headers.Add("X-WNS-Type", "wns/raw");
                    //注意raw消息为application/octet-stream
                    myReqeuest.ContentType = "application/octet-stream";
                }
                else if ((bool)radioButton2.IsChecked)
                {
                    message2 = NotifyTile(message);
                    //推送tile消息
                    myReqeuest.Headers.Add("X-WNS-Type", "wns/tile");
                }
                else if ((bool)radioButton3.IsChecked) {
                    message2 = NotifyToast(message);
                    //推送toast消息
                    myReqeuest.Headers.Add("X-WNS-Type","wns/toast");
                }
                else if ((bool)radioButton4.IsChecked)
                {
                    message2 = NotifyBadge(message);
                    //推送badge消息
                    myReqeuest.Headers.Add("X-WNS-Type","wns/badge");
                }
                else
                {
                    //默认的消息
                    myReqeuest.Headers.Add("X-WNS-Type","wns/raw");
                    myReqeuest.ContentType = "application/octet-stream";
                }
                byte[] buffer = Encoding.UTF8.GetBytes(message2);
                myReqeuest.ContentLength = buffer.Length;
                myReqeuest.Method = "POST";
                using (Stream stream=myReqeuest.GetRequestStream())
                {
                    stream.Write(buffer,0,buffer.Length);
                }
                using (HttpWebResponse webResponse=(HttpWebResponse)myReqeuest.GetResponse())
                {
                    /*响应代码说明
                    200 OK,WNS已经接收到通知
                    400 错误的请求
                    401 未授权,token可能无效
                    403 已禁止,mainfest中的identity可能不对
                    404 未找到
                    405 方法不允许
                    406 无法接受
                    410 不存在,信道不存在或过期
                    413 请求实体太大,限制为5000字节
                    500 内部服务器错误
                    503 服务不可用
                    */
                    tb2.Text = webResponse.StatusCode.ToString();
                }
                

            }
            catch (Exception ex)
            {

                tb2.Text = "异常" + ex.Message;
            }
        }
        /// <summary>
        /// 封装Badge消息格式
        /// </summary>
        /// <param name="message"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private string NotifyBadge(string badge)
        {
            string badgemessage = (@"<badge value="""+badge+@"""/>");
            return badgemessage;
        }
        /// <summary>
        /// 封装Tile消息格式
        /// </summary>
        /// <param name="message"></param>
        /// <returns></returns>
        private string NotifyTile(string message)
        {
            string tilemessage = 
                @"<tile>
                    <visual>
                        <binding template=""TileWideText03"">
                            <text id=""1"">"+message+@"</text>
                        </binding>
                    </visual>
                  </tile>";
            return tilemessage;
        }
        /// <summary>
        /// 封装Toast消息格式
        /// </summary>
        /// <param name="message"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private string NotifyToast(string message)
        {
            string toastmessage = 
        @"<toast launch=""" + Guid.NewGuid().ToString() + @""">
            <visuallang=""en-US"">
                <binding template=""ToastText01"">
                    <text id=""1"">" + message + @"</text>
                </binding>
            <visual>
          </toast>";
            return toastmessage;
        }
    }
}
```
14.54.客户端程序实现推送通知的接收
在客户端程序实现推送通知主要有两个步骤,一个是请求通道URI,另外一个是将通道URI发送至服务器,请求通道URI是指Win10客户端平台发出此请求,然后该平台依次从推送通知服务请求通道URI,请求完成后,实现的方法是直接调用PushNotificationChannelManger类的CreatePushNotificationChannelForApplicationAsync静态方法,返回的值为包含URI的PushNotificationChannel对象,将通道URI发送至服务器则是要把这个通道存储起来,用来向当前的应用程序发送消息通知,发送的实现应该采用安全的方式将此信息发送至服务器,对信息进行加密并使用安全的协议,如HTTPS.下面来看下对于推送通知通道的一些处理情况
1.请求通道
每次调用你的应用时，应该使用以下逻辑请求一个新的通道：
 1.请求通道
 2.将新通道与前一个通道相比较,如果相同,则不需要采取进一步的操作.注意,在应用每次成功将通道发送到服务时,都对该通道进行本地存储,以便将该通道与后一个通道相比较.
3.如果该通道已更改,请将新通道发送给web服务.
对CreatePushNotificationChannelForApplicationAsync方法的不同调用不会始终返回不同的通道,如果自上次调用后通道未改变,则应用不必重新向服务发送此相同的通道以节省资源和Internet流量.一个应用可同时拥有多个有效的通道URI,由于每个唯一的通道直到其到期前均有效,因此请求新的通道也无妨,因为他不会影响任何以前通道的到期时间.
通过在每次调用应用时请求一个新通道,最大化的保证了有效通道,如果你担心用户在30天内运行你的应用的次数不超过一次,你可以实施一个后台任务来定期执行你的通道请求代码.
2.处理通道请求中的错误
如果Internet不可用,则调用CreatePushNotificationChannelForApplicaitonAsync方法可能会失败.若要处理这种情况,可以进行重试,建议尝试三次,在每次尝试不成功后,延迟10秒,如果三次均失败,则必须等到该用户下次启动应用后再次重试.
3.关闭通道
通过调用PushNotificationChannel.Close方法,你的应用可立即停止所有通道上的通知传递.虽然此项操作在实际的业务中比较少见,但是可能存在某些情景,你希望停止及那个所有通知传递到你的应用.例如,如果你的应用有用户账户概念,且某个用户已从该应用注销,则磁贴不再显示该用户的个人信息应该是合理的行为.若要成功清除磁贴的内容并停止通知传递,你必须执行以下操作:
1.通过在向用户传递磁贴,吐司,徽章或原生通知的任何云通知通道上调用PushNotificationChannel.Close方法,停止所有磁贴更新,调用Close方法可确保不会再将该用户的任何通知传递到客户端.
2.通过调用TileUpdater.Clear方法清除磁贴内容,以便从磁贴中删除之前用户的数据.
下面给出测试推送通知的示例,创建一个Win10的应用程序,在应用程序里面注册推送通知的频道,然后使用上一小节的Windows窗体应用程序利用注册的频道来发送推送通知.
首先需要把使用开发者账号获取到的Identity信息,替换掉当前应用程序清单文件Package.appxmanifest里面的Identity元素.
``` bash
<Identity Name="229Geek-Space.PushNotificationDemoeeee" Publisher="CN=748B11E2-8CD3-41F4-9670-D945180F31FC" Version="1.0.0.0"/>
<StackPanel>
	<Button x:Name="bt_open" Content="注册推送通知" Click="bt_open_Click">
	</Button>
	<TextBlock x:Name="info" TextWrapping="Wrap"></TextBlock>
</StackPanel>
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Networking.PushNotifications;
using Windows.UI.Notifications;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class ReceiveNotificationPage : Page
    {
        public ReceiveNotificationPage()
        {
            this.InitializeComponent();
        }
        /// <summary>
        /// 打开推送通知的频道
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_open_Click(object sender, RoutedEventArgs e)
        {
            //创建一个频道
            PushNotificationChannel channel=await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            //获取频道的地址,实际上是需要把频道的地址发送到云端服务来存储起来
            string uri=channel.Uri;
            //在该测试例子里面我们把地址复制到Windows窗体程序进行发送通知.
            Debug.WriteLine(uri);
            //接收到通知后触发的事件
            channel.PushNotificationReceived += Channel_PushNotificationReceived;
        }
        /// <summary>
        /// 通知接收事件,如果有推送通知,当前应用程序正在运行则会触发该事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="args"></param>
        /// <exception cref="NotImplementedException"></exception>
        private async void Channel_PushNotificationReceived(PushNotificationChannel sender, PushNotificationReceivedEventArgs args)
        {
            switch (args.NotificationType)
            {
                case PushNotificationType.Toast://toast通知
                    ToastNotificationManager.CreateToastNotifier().Show(args.ToastNotification);
                    break;
                case PushNotificationType.Tile://tile通知
                    TileUpdateManager.CreateTileUpdaterForApplication().Update(args.TileNotification);
                    break;
                case PushNotificationType.Badge://badge通知
                    BadgeUpdateManager.CreateBadgeUpdaterForApplication().Update(args.BadgeNotification);
                    break;
                case PushNotificationType.Raw://raw通知
                    string msg = args.RawNotification.Content;
                    await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => info.Text = msg);
                    break;
                case PushNotificationType.TileFlyout:
                    break;
                default:
                    break;
            }
        }
    }
}

```
#### Socket编程
Socket是网络通信的一种方式,使用Socket可以实现比HTTP协议更加复杂和高效的网络编程.Win10基于Windows运行时的架构提供了一套Socket编程的API,这套API不仅仅可以实现互联网上的Socket的TCP和UDP协议,还可以支持蓝牙编程和近场通信编程的消息传输.
Socket是应用层与TCP/IP协议族通信的中间软件抽象层,它是一组接口.在设计模式中,Socket其实就是一个门面模式,它把复杂的TCP/IP协议隐藏在Socket接口后面,对用户来说,一组简单的接口就是全部,让Socket去组织数据,以符合指定的协议,应用程序通常通过Socket向网络发出请求或者应答网络请求,Socket是一种用于表达两台机器之间连接"终端"的软件抽象,对于一个给定的连接,在每台机器上都有一个Socket
网络上的两个程序通过一个双向的通信连接实现数据交换,这个双向链路的一端称为一个Socket,Socket通常用来实现客户方和服务方的连接,Socket是TCP/IP协议的一个十分流行的编程界面,一个Socket由一个IP地址和一个端口号唯一确定,但是Socket所支持的协议种类不光是TCP/IP一种,因此两者之间没有必然的联系.
一台机器上的Socket同另一台机器通话创建一个通信信道,程序员可以用这个信道在两台机器间发送数据.当你发送数据时,TCP/IP协议栈的每一层都给你的数据里添加适当的报头,Socket像电话听筒一样在电话的任意一端---你和我通过一个专门的信道来进行通话,会话将一直进行下去直到我们决定挂断电话。
使用Socket编程的API可以使我们更方便的创建出FTP,电子邮件,聊天系统和流媒体等类型的网络应用.
1.端口
网络中可以被命名和寻址的通信端口,是操作系统可分配的一种资源,按照OSI七层协议的描述,传输层与网络层在功能上的最大区别是传输层提供进程通信能力,从这个意义上讲,网络通信的最终地址就不仅仅是主机地址了,还包括可以描述进程的某种标识符.为此,TCP/IP协议提出了协议端口的概念,用于标识通信的进程.
端口是一种抽象的软件结构(包括一些数据结构和I/O缓冲区).应用程序(即进程)通过系统调用与某端口建立连接(绑定)后,传输层传递给该端口的数据都被相应的进程接收,相应的进程发给传输层的数据都通过该端口输出.
类似于文件描述符,每个端口都拥有一个叫端口号(port number)的整数型标识符,用于区别不同的端口,由于TCP/IP传输层的两个协议TCP和UDP是完全独立的软件模块,因此各自的端口号也相互独立,如TCP有一个255号端口,UDP也可以有一个255号端口,两者并不冲突.
端口号的分配是一个重要问题.有两种基本分配方式:第一种叫全局分配,这是一种集中控制方式,由一个公认的中央机构根据用户进行统一分配,并将结果公布于众,第二种是本地分配,又称动态连接,即进程需要访问传输层服务时,向本地操作系统提出申请,操作系统返回一个本地唯一的端口号,进程再通过合适的系统调用将自己与该端口号联系起来.TCP/IP端口号的分配综合了上述两种方式,TCP/IP将端口分为两部分,少量的作为保留端口,剩余的为自由端口,以本地方式进行分配.TCP和UDP均规定,小于256的端口才能作为保留端口.
2.地址
网络通信中通信的两个进程分别在两台地址不同的机器上,在互联网络上,两台机器可能位于不同位置的网络上,这些网络通过网络互连设备(网关,网桥,路由器等)连接.因此需要三级寻址:
1.某一主机可与多个网络相连,必须指定一个特定网络地址
2.网络上每一台主机应有唯一的地址
3.每一台主机上的每一个进程应有在该主机上的唯一标识符.
主机地址通常由网络ID和主机ID组成,在TCP/IP协议中用32位整数值表示;TCP和UDP均使用16位端口号标识用户进程.
3.IPV4和IPV6
IPV4是互联网协议的第4版,也是第一个被广泛使用,构成现代互联网技术的基础协议.
1981年,Jon Postel在RFC791中定义了IP,IPV4可以运行在各种各样的底层网络上,比如端对端的串行数据链路(PPP协议和SLIP协议),卫星链路等.最常用的局域网是以太网,IPV6是IETF设计的用于替代现行版本IP协议的下一代IP协议.目前IP协议的版本号是4(简称为IPV4),下一版本就是IPV6.
4.广播
广播是指在一个局域网中向所有的网络节点发送信息.广播有一个广播组,即只有一个广播组内的节点才能收到发往这个广播组的信息.什么决定了一个广播组呢,就是端口号,局域网内一个节点,如果设置了广播属性并监听了端口A,那么他就加入了A组广播,这个局域网内所有发往广播端口A的信息它都收的到,在广播的实现中,如果一个节点想接受A组广播信息,那么就要先将它绑定给地址和端口A,然后设置这个Socket的属性为广播属性,如果一个节点不想接受广播信息,而只想发送广播信息,那么不用绑定端口,只需要先为socket设置广播属性,然后向广播地址的A端口发送信息即可.
5.TCP协议
TCP是Tranfer Control Protocol的简称,是一种面向连接的保证可靠传输的协议.通过TCP协议传输,得到的是一个顺序的,无差错的数据流,发送方和接收方的两个成对的socket之间必须建立连接,以便在TCP协议的基础上进行通信,当一个socket(通常都是server socket)等待建立连接时,另一个socket可以要求进行连接,一旦这两个socket连接起来,它们就可以进行双向数据传输,双方都可以进行发送或接收操作.
6.UDP协议.
UDP是User Datagram Protocol的简称,是一种无连接的协议,每个数据报都是一个独立的信息,包括完整的源地址或目的地址,它在网络上以任何可能的路径传往目的地,因此能否到达目的地,到达目的地的时间以及内容的正确性都是不能被保证的.
7.TCP协议和UDP协议的区别
UDP:
1.每个数据报中都给出了完整的地址信息,因此无需要建立发送方和接收方的连接.
2.UDP传输数据时是有大小限制的,每个被传输的数据报必须限定在64KB之内.
3.UDP是一个不可靠的协议,发送方所发送的数据报并不一定以相同的次序到达接收方.
4.TCP在网络通信上有极强的生命力,例如远程连接(Telnet)和文件传输(FTP)都需要可靠的传输不确定长度的数据.但是可靠的传输是要付出代价的,对数据内容正确性的检验必须占用计算机的处理时间和网络的带宽,因此TCP传输的效率不如UDP高.
1.面向连接的协议,在Socket之间进行数据传输之前必须要建立连接,所以在TCP中需要连接时间.
2.TCP传输数据大小限制,一旦连接建立起来,双方的Socket就可以按统一的格式传输大数据.
3.TCP是一个可靠的协议,它确保接收方完全正确的获取发送方所发送的全部数据.
4.UDP操作简单,而且仅需要较少的监护,因此常用于局域网高可靠性的分散系统中的client/server应用程序,
8.Socket连接与HTTP连接的区别
通常情况下,Socket连接就是TCP连接,因此Socket连接一旦建立,通信双方即可相互发送数据内容,直到双方连接断开,但在实际网络应用中,客户端到服务器之间的通信往往需要穿越多个中间节点,例如路由器,网关,防火墙等,大部分防火墙默认会关闭长时间处于非活跃状态的连接而导致Socket连接中断,因此需要通过轮询告诉网络,该连接处于活跃状态.而HTTP连接使用的是"请求——响应"的方式,不仅在请求时需要先建立连接,而且需要客户端向服务器发出请求后,服务器端才能回复数据.很多情况下,需要服务器端主动向客户端推送数据,保持客户端与服务器数据的实时与同步.若双方建立的是HTTP连接,则服务器需要等到客户端发送一次请求后才能将数据传回给客户端.因此,客户端定时向服务器端发送连接请求,不仅可以保持在线,同时也是在"询问"服务器是否有新的数据,如果有就将数据传给客户端.
15.12.Socket通信的过程
Socket通信在客户端和服务器进行的,建立Socket连接至少需要一对Socket,其中一个运行于客户端.称为ClientSocket,另一个运行于服务器端,称为ServerSocket.
Socket之间的连接过程分为三个步骤:服务器监听,客户端请求,连接确认.
服务器监听:服务器端Socket并不定位具体的客户端Socket,而是处于等待连接的状态,实时监控网络状态,等待客户端的连接请求.
客户端请求:指客户端的Socket提出连接请求,要连接的目标是服务器端的Socket.为此,客户端的Socket必须首先描述它要连接的服务器的Socket,指出服务器端Socket的地址和端口号,然后再向服务器端Socket提出连接请求.
连接确认:当服务器端Socket监听到或者说接收到客户端Socket的连接请求时,就响应客户端Socket的请求,建立一个新的线程,把服务器端Socket的描述发给客户端,一旦客户确认了此描述,双方就正式建立连接.而服务器端Socket继续处于监听状态,继续接收其他客户端Socket的连接请求.
整个Win10应用程序Socket通信的过程包括了七个步骤
第一步:创建一个客户端和服务器端的Socket连接
第二步:客户端发送消息的过程,客户端向服务器端发送消息,服务器端接收客户端发过来的消息.
第三步:客户端接收消息的过程，客户端接收服务端返回来的消息.
第四步:连接继续保持,将可以不断地重复第二步和第三步地发送消息和接受消息地动作.
第五步:关闭发送接收通道,可以只关闭发送通道或者接收通道,也可以两者同时关闭.
第六步:关闭Socket连接.
第七步:整个通信过程到此终止.
15.2.Socket编程之TCP协议
在Win10里不仅仅可以实现客户端的Socket编程,还可以实现服务器端的Socket监听客户端的TCP协议主要依赖于StreamSocket类来实现相关的功能,对应的服务器端的监听则可以使用StreamSocketListener类
15.21.StreamSocket介绍及TCP Socket编程步骤
StreamSocket类在Windows.Networking.Sockets空间下,表示对象连接到网络资源,以使用异步方法发送数据,
StreamSocket类成员
StreamSocket() 
创建新的StreamSocket对象
StreamSocketControl Control 
获取StreamSocket对象上的套接字控件数据;返回某一StreamSocket对象上的套接字控件数据.
StreamSocketInformation Information 
获取StreamSocket对象上的套接字信息:返回该StreamSocket对象的套接字信息
IIputStream InputStream 
获取要从StreamSocket对象上的远程目标读取的输入流,返回要从远程目标读取的有序字节流
IOutputStream OutputStream 
获取StreamSocket对象上的远程目标读取的输出流,返回要写入远程目标的有序字节流
IAsyncAction ConnectAsync(EndpointPair endpointPair) 
启动Stream对象连接到被指定为EndPointPair对象的远程网络目标的异步操作,endPointPair:指定本地主机名或IP地址,本地服务名或UDP端口,远程主机名或IP地址,以及远程网络目标的远程服务名或远程TCP端口的EndpointPair对象:返回StreamSocket对象的异步连接操作.
IAsyncAction ConnectAsync(EndpointPair endpointPair,SocketProtectionLevel protectionLevel)
启动StreamSocket对象连接到被指定为EndpointPair对象和SocketProtectionLevel枚举的远程网络目标的异步操作,protectionLevel:表示StreamSocket对象的完整性和加密的保护级别,返回StreamSocket对象的异步连接操作
IAsyncAction ConnectAsync(HostName remoteHostName,string remoteServiceName)
在StreamSocket上启动远程主机名和远程服务名所指定的远程网络目标的连接操作;remoteHostName:远程网络目标的主机名或IP地址;remoteServiceName:远程网络目标的服务名称或TCP端口号,返回StreamSocket对象的异步连接操作.
IAsyncAction ConnectAsync(HostName remoteHostName,string remoteServiceName,SocketProtectionLevel protectionLevel)
启动StreamSocket对象连接远程主机名,远程服务名以及SocketProtectionLevel所指定的远程目标的异步操作
IASyncAction UpgradeToSslAsync(SocketProtectionLevel protectionLevel,HostName validationHostName)
启动StreamSocket对象将连接的套接字升级到使用SSL的异步操作;protectionLevel:表示StreamSocket对象上完整性和加密的保护级别,validationHostName:在升级到SSL时用于验证的远程网络目标的主机名,返回StreamSocket对象升级到使用SSL的异步操作.
使用StreamSocket类进行TCP Socket编程的步骤如下:
1.使用StreamSocket类创建TCP协议的Socket对象.
2.使用StreamSocket.ConnectAsync方法之一建立与TCP网络服务器的网络连接.
3.使用Streams.DataWriter对象将数据发送到服务器,该对象允许程序员在任何流上写入常用类型(例如整数和字符串)
4.使用Streams.DataReader对象从服务器接收数据,该对象允许程序员在任何流上读取常用类型(例如整数和字符串).
15.22.连接Socket
连接Socket是使用Socket编程的第一步,创建StreamSocket并连接到服务器,在这个步骤里还会定义主机名和服务名(TCP端口)以连接到服务器.连接的过程是采用异步任务的模式,当连接成功时会继续执行到await连接服务器后面的代码,如果连接失败,ConnectAsync方法将会抛出异常,表示无法与网络服务器建立TCP连接,连接失败,需要捕获异常的信息来获取是什么类型的异常,然后判断是否需要重新连接还是释放掉资源.
``` bash
public async void Connect() {
            //创建一个StreamSocket对象
            StreamSocket clientSocket = new StreamSocket();
            try
            {
                //创建一个主机名字
                HostName serverHost = new HostName(serverHostName);
                //开始链接
                await clientSocket.ConnectAsync(serverHost,serverPort);
                //链接成功
            }
            catch (Exception ex)
            {
                //获取错误的类型 SocketError.GetStatus(exception.HResult)
                //错误消息 exception.Message
                //如果关闭Socket则释放资源
                clientSocket.Dispose();
                clientSocket = null;
            }
        }
```
15.23.发送和接收信息
Socket连接成功之后便可以发送和接收Socket消息,发送消息需要使用Streams.DataWriter对象将数据发送到服务器,接收消息使用Streams.DataReader对象从服务器接收数据.
``` bash
 /// <summary>
        /// 发送消息
        /// </summary>
        public async void Send() {
            try
            {
                //发送消息
                string sendData = "Test Message str";
                //创建一个DataWriter对象
                DataWriter writer = new DataWriter(clientSocket.OutputStream);
                //获取UTF-8字符串的长度
                Int32len = writer.MeasureString(sendData);
                //存储数据到输出流里面
                await writer.StoreAsync();
                //发送成功释放资源
                writer.DetachStream();
                writer.Dispose();
            }
            catch (Exception ex)
            {
                //获取错误的类型SocketError.GetStatus(exception.HResult)
                //错误消息exception.Message
                //如果关闭Socket则释放资源
                clientSocket.Dispose();
                clientSocket = null;
                
            }
            
        }
        public async void Receive() {
            try
            {
                //接收数据
                DataReader reader = new DataReader(clientSocket.InputStream);
                //设置接收数据的模式
                reader.InputStreamOptions = InputStreamOptions.Partial;
                await reader.LoadAsync(reader.UnconsumedBufferLength);
                //接收成功
            }
            catch (Exception excepiton)
            {

               //... ...
            }
        }

```
15.24.TCP协议服务器端监听消息
在Win10应用程序里面,不仅仅是创建客户端的TCP程序,还可以创建服务器端的服务,相当于在本地创建了Socket的服务器,服务器端监听消息表示服务器端程序对客户端的Socket连接和发送消息的监听,在Win10里可以通过Windows.Networking.Sockets空间下的StreamSocketListener类来实现监听的操作,StreamSocketListener的使用需要在配置文件中添加privateNetworkClientServer的权限,TCP协议服务器端监听消息实现的步骤如下:
1.注册ConnectionReceived事件获取成功建立监听的消息.
2.使用BindServiceNameAsync方法建立起本地服务器的监听.
3.循环获取监听的消息,用监听成功的Socket对象创建DataReader,如DataReader reader=new DataReader(args.Socket.InputStream),然后循环等待监听.
4.向客户端发送消息,用监听成功的socket对象创建DataWriter,如DataWriter server=new DataWriter(args.Socket.OutputStream);
StreamSocketListener类成员
StreamSocketListener() 
创建新的StreamSocketListener对象
StreamSocketListenerControl Control 
获取StreamSocketListener对象上的套接字控件数据;返回某一StreamSocketListener
对象上的套接字控件数据.
StreamSocketListenerInformation Information
获取该StreamSocketListener对象的套接字信息;返回该StreamSocketListener对象的套接字信息
eventTypedEventHandler<StreamSocketlistener,StreamSocketListenerConnectionReceivedEventArgs> ConnectionReceived
指示在StreamSocketListener对象上收到连接的事件
IAsyncAction BindEndpointAsync(HostName localHostName)
启动StreamSocketListener本地主机名和本地服务名的绑定操作.
localHostName:用于绑定StreamSocketListener对象的本地服务器名称或TCP端口号,返回StreamSocketListener对象的异步绑定操作.
IAsyncAction BindServiceNameAsync(string localService) 
启动StreamSocketListener本地服务名的绑定操作.localServiceName:用于绑定StreamSocketListener对象的本地服务名称或TCP端口号.返回StreamSocketListener对象的异步绑定操作.
``` bash
 /// <summary>
        /// 监听的连接事件处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="args"></param>
        /// <exception cref="NotImplementedException"></exception>
        private async void Listener_ConnectionReceived(StreamSocketListener sender, StreamSocketListenerConnectionReceivedEventArgs args)
        {
            DataReader reader = new DataReader(args.Socket.InputStream);
            try
            {
                //循环接收数据
                while (true)
                {
                    //读取监听到的消息
                    uint stringLength = reader.ReadUInt32();
                    uint actualStringLength = await reader.LoadAsync(stringLength);
                    //通过reader去读取监听到的消息内容 如reader.ReadString(actualStringLength)

                }
            }
            catch (Exception ex)
            {
                //异常处理
            }
        }
```
15.25.实例:模拟TCP协议通信过程
下面给出模拟TCP协议通信过程的示例:在Win10上模拟TCP协议的客户端和服务器端编程的实现.
``` bash
<Page
    x:Class="App1.TcpPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel>
            <Button Content="开始监听" Margin="12" x:Name="btStartListener" Click="btStartListener_Click"/>
            <Button Content="连接socket" Margin="12" x:Name="btConnectSocket" Click="btConnectSocket_Click"/>
            <TextBox Text="hello" x:Name="tbMsg"/>
            <Button Content="发送消息" Margin="12" x:Name="btSendMsg" Click="btSendMsg_Click"/>
            <Button Content="关闭" Margin="12" x:Name="btClose" Click="btClose_Click"/>
            <ScrollViewer>
                <StackPanel x:Name="lbMsg">
                    <TextBlock Text="收到的消息:" FontSize="20"/>
                </StackPanel>
            </ScrollViewer>
        </StackPanel>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Networking;
using Windows.Networking.Sockets;
using Windows.Storage.Streams;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class TcpPage : Page
    {
        public TcpPage()
        {
            this.InitializeComponent();
        }
        //监听器
        StreamSocketListener listener;
        //Socket数据流对象
        StreamSocket socket;
        //输出流的写入数据对象
        DataWriter writer;

        /// <summary>
        /// 监听
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btStartListener_Click(object sender, RoutedEventArgs e)
        {
            if (listener!=null)
            {
                await new MessageDialog("监听已经启动了").ShowAsync();
                return;
            }
            listener= new StreamSocketListener();
            listener.ConnectionReceived += Listener_ConnectionReceived;
            //开始监听操作
            try
            {
                await listener.BindServiceNameAsync("22112");
                await new MessageDialog("正在监听中").ShowAsync();
            }
            catch (Exception ex)
            {
                listener = null;
                //未知错误
                if (SocketError.GetStatus(ex.HResult)==SocketErrorStatus.Unknown)
                {
                    throw;
                }
            }
        }
        /// <summary>
        /// 监听成功后的连接事件处理
        /// </summary>
        /// <param name="sender">连接的监听者</param>
        /// <param name="args">连接的监听到的数据参数</param>
        private async void Listener_ConnectionReceived(StreamSocketListener sender, StreamSocketListenerConnectionReceivedEventArgs args)
        {
            DataReader reader = new DataReader(args.Socket.InputStream);
            try
            {
                //循环接收数据
                while (true)
                {
                    //读取数据前面的4个字节,代表的是接收到的数据的长度
                    uint sizeFieldCount = await reader.LoadAsync(sizeof(uint));
                    if (sizeFieldCount!=sizeof(uint))
                    {
                        //在socket被关闭之前才可以读取全部的数据
                        return;
                    }
                    //读取字符串
                    uint stringLength=reader.ReadUInt32();
                    uint actualStringLength=await reader.LoadAsync(stringLength);
                    if (stringLength!=actualStringLength) {
                        //在socket被关闭之前才可以读取全部数据
                        return;

                    }
                    string msg = reader.ReadString(actualStringLength);
                    //通知到界面监听到的消息
                    await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                    {
                        TextBlock tb = new TextBlock { Text = msg, FontSize = 20 };
                        lbMsg.Children.Add(tb);
                    });
                }
            }
            catch (Exception ex)
            {
                //未知异常
                if (SocketError.GetStatus(ex.HResult)==SocketErrorStatus.Unknown)
                {
                    throw;
                }
               
            }
        }
        //连接Socket
        private async void btConnectSocket_Click(object sender, RoutedEventArgs e)
        {
            if (socket!=null)
            {
                await new MessageDialog("已经连接了Socket").ShowAsync();
                return;
            }
            HostName hostName= null;
            string message = "";
            try
            {
                hostName = new HostName("localhost");
            }
            catch (ArgumentException)
            {
                message = "主机名不可用";
            }
            if (message!="")
            {
                await new MessageDialog(message).ShowAsync();
                return;
            }
            socket = new StreamSocket();
            try
            {
                //连接到socket服务器
                await socket.ConnectAsync(hostName,"22112");
                await new MessageDialog("连接成功").ShowAsync();
            }
            catch (Exception ex)
            {
                //未知异常
                if (SocketError.GetStatus(ex.HResult)==SocketErrorStatus.Unknown)
                {
                    throw;
                }
                
            }
        }
        /// <summary>
        /// 发送消息
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btSendMsg_Click(object sender, RoutedEventArgs e)
        {
            if (listener==null)
            {
                await new MessageDialog("监听未启动").ShowAsync();
                return;
            }
            if (writer==null)
            {
                writer = new DataWriter(socket.OutputStream);
            }
            //先写入数据的长度,长度的类型为UInt32,然后再写入数据
            string stringToSend = tbMsg.Text;
            writer.WriteUInt32(writer.MeasureString(stringToSend));
            writer.WriteString(stringToSend);
            //把数据发送到网络
            try
            {
                await writer.StoreAsync();
                await new MessageDialog("发送成功").ShowAsync() ;
            }
            catch (Exception ex)
            {
                if (SocketError.GetStatus(ex.HResult)==SocketErrorStatus.Unknown)
                {
                    throw;
                }
            }
        }
        /// <summary>
        /// 关闭连接,清空资源
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void btClose_Click(object sender, RoutedEventArgs e)
        {
            if (writer!=null)
            {
                writer.DetachStream();
                writer.Dispose();
                writer= null;
            }
            if (socket!=null)
            {
                socket.Dispose();
                socket= null; 
            }
            if (listener!=null)
            {
                listener.Dispose();
                listener= null;
            }
        }
    }
}

```
15.3.Socket编程之UDP协议
UDP协议和TCP协议都是Socket编程的协议,但是与TCP协议不同,UDP协议并不提供超时重传,出错重传等功能,也就是说它是不可靠的协议.UDP适用于一次只传送少量数据,对可靠性要求不高的应用环境,既然UDP是一种不可靠的网络协议,在有些情况下,UDP协议可能会变得非常有用,因为UDP具有TCP所望尘莫及的速度优势,虽然TCP协议中植入了各种安全保障功能,但是在实际执行的过程中会占用大量的系统开销,无疑使速度受到严重影响,反观UDP,由于排除了信息可靠传递机制,将安全和排序等功能移交给上层应用来完成,极大的降低了执行时间,使速度得到了保证.Win10里的UDP协议的通信是通过DatagramSocket类来实现消息的发送,接受和监听等功能的.
15.31.发送和接收消息
使用UDP协议进行消息发送和接收跟TCP协议是有区别的,UDP协议不一定要进行连接的操作,它可以直接通过主机地址进行消息发送和接收.使用UDP协议进行消息发送和接收也一样要依赖DataWriter类和DataReader类来分别进行数据发送和接收.下面来看一下Win10中使用UDP协议进行发送和接收信息的两种方式.
1.使用主机名和端口号直接发送和接收消息
创建一个DatagramSocket类对象,调用GetOutputStreamAsync方法获取输出流IOutputStream对象创建DataWriter对象进行消息的发送,接收消息直接订阅DatagramSocket对象的MessageReceived事件接收消息,使用DataReader对象获取消息的内容.
``` bash
 public async void GetUDPMessage() {
            //主机名
            HostName hostName = new HostName("localhost");
            DatagramSocket datagramSocket= new DatagramSocket();
            //订阅接收消息的事件
            datagramSocket.MessageReceived += DatagramSocket_MessageReceived;
            //获取输出流
            IOutputStream outputStream = await datagramSocket.GetOutputStreamAsync(hostName,"22112");
            //创建DataWriter对象发送消息
            DataWriter writer= new DataWriter(outputStream);
            writer.WriteString("test");
            await writer.StoreAsync();
           
          
        }
 //接受消息的事件处理程序
        private void DatagramSocket_MessageReceived(DatagramSocket sender, DatagramSocketMessageReceivedEventArgs args)
        {
           //获取DataReader对象,读取消息内容
           DataReader dataReader=args.GetDataReader();
            uint length = dataReader.UnconsumedBufferLength;
            string Content = dataReader.ReadString(length);
        }
```
2.先连接Socket再发送接收消息
DatagramSocket类提供了ConnectAsync方法来负责Socket的连接,连接成功之后可以使用该DatagramSocket对象进行消息的发送,消息的接收和第一种方式的实现是一样的.
``` bash
 public async void ConnectSend() { 
            ///创建DatagramSocket
            DatagramSocket datagramSocket= new DatagramSocket();
            datagramSocket.MessageReceived += DatagramSocket_MessageReceived;
            //连接服务器
            await datagramSocket.ConnectAsync(new HostName("localhost"),"22112");
            //发送消息
            DataWriter writer = new DataWriter(datagramSocket.OutputStream);
            writer.WriteString("test");
            await writer.StoreAsync();

        }
```
15.32.UDP协议服务器端监听消息
UDP协议在实现服务器端监听消息的功能也是使用DatagramSocket类去实现,
实现的步骤如下:
1.注册DatagramSocket对象的MessageReceived事件接收消息(注意和TCP的ConnectionReceived事件的区别);
2.使用BindServiceNameAsync方法建立本地服务器的监听
3.使用GetOutputStreamAsync方法传入服务器地址和端口号,获取IOutputStream对象,从而创建DataWriter对象向客户端发送消息.
UDP协议服务器端监听消息的示例
``` bash
<Page
    x:Class="App1.UdpPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel>
            <Button Content="开始监听消息" Margin="12" x:Name="listener"
                    Click="listener_Click"/>
            <Button Content="发送消息" Margin="12" x:Name="send" Click="send_Click"/>
            <Button Content="关闭" Margin="12" x:Name="close" Click="close_Click"/>
            <ScrollViewer Height="300">
                <StackPanel x:Name="msgList">
                </StackPanel>
            </ScrollViewer>
        </StackPanel>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Networking;
using Windows.Networking.Sockets;
using Windows.Storage.Streams;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class UdpPage : Page
    {
        //客户端的DatagramSocket对象
        DatagramSocket datagramSocket;
        //客户端的DataWriter对象
        DataWriter writer;

        public UdpPage()
        {
            this.InitializeComponent();
        }
        /// <summary>
        /// 启动本地服务器的监听
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void listener_Click(object sender, RoutedEventArgs e)
        {
            DatagramSocket datagramSocket = new DatagramSocket();
            datagramSocket.MessageReceived += DatagramSocket_MessageReceived;
            try
            {
                await datagramSocket.BindServiceNameAsync("22112");
                msgList.Children.Add(new TextBlock { Text = "监听成功", FontSize = 20 });
            }
            catch (Exception ex)
            {
                if (SocketError.GetStatus(ex.HResult)==SocketErrorStatus.AddressAlreadyInUse)
                {
                    //异常消息,使用SocketErrorStatus枚举来判断Socket的异常类型
                }
                
            }
        }
        /// <summary>
        /// 本地服务器的消息接收事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="args"></param>
        /// <exception cref="NotImplementedException"></exception>
        private async void DatagramSocket_MessageReceived(DatagramSocket sender, DatagramSocketMessageReceivedEventArgs args)
        {
            DataReader dataReader=args.GetDataReader();
            uint length = dataReader.UnconsumedBufferLength;
            string content = dataReader.ReadString(length);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                msgList.Children.Add(new TextBlock() { Text = "服务器受到的消息:" + content, FontSize = 20 });

            });
            //通过远程的地址和端口号,回应给相应的客户端的消息
            IOutputStream outputStream = await sender.GetOutputStreamAsync(
                args.RemoteAddress,args.RemotePort);
            DataWriter writer= new DataWriter(outputStream);
            writer.WriteString(content+"(服务器发送)");
            try
            {
                await writer.StoreAsync();
                await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,()=>msgList.Children.Add(new TextBlock { Text="服务器发送的消息:"+content+"(服务器发送)",FontSize=20}));
                
            }
            catch (Exception ex)
            {

                
            }
        }
        /// <summary>
        /// 向本地服务器发送消息
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void send_Click(object sender, RoutedEventArgs e)
        {
            if (writer==null)
            {
                HostName hostName = new HostName("localhost");
                datagramSocket=new DatagramSocket();
                datagramSocket.MessageReceived += DatagramSocket_MessageReceived1;
                IOutputStream outputStream = await datagramSocket.GetOutputStreamAsync(hostName,"22212");
                writer= new DataWriter(outputStream);
            }
            else
            {
                writer = new DataWriter(datagramSocket.OutputStream);
            }
            //写入消息
            writer.WriteString("test");
            try
            {
                //发送消息
                await writer.StoreAsync();
                msgList.Children.Add(new TextBlock()
                {
                    Text = "客户端发送的消息:"+"test",
                    FontSize=20
                }) ;
            }
            catch (Exception)
            {

               
            }
        }
        /// <summary>
        /// 客户端接收消息的事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="args"></param>
        private async void DatagramSocket_MessageReceived1(DatagramSocket sender, DatagramSocketMessageReceivedEventArgs args)
        {
            try
            {
                DataReader dataReader = args.GetDataReader();
                uint length = dataReader.UnconsumedBufferLength;
                string content = dataReader.ReadString(length);
                await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    msgList.Children.Add(new TextBlock { Text = "客户端收到的消息:" + content, FontSize = 20 });
                });
            }
            catch (Exception)
            {

             
            }
        }
        /// <summary>
        /// 释放Socket资源
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void close_Click(object sender, RoutedEventArgs e)
        {
            if (datagramSocket!=null)
            {
                datagramSocket.Dispose();
                datagramSocket = null;
            }
            if (writer!=null)
            {
                writer.Dispose();
                writer= null;
            }
        }
    }
}

```
#### 蓝牙和近场通信
在win10上的蓝牙和近场通信的编程也是使用Socket相关的API来进行消息的发送和接收的
16.1 蓝牙原理
蓝牙是一种低成本,短距离的无线通信技术,蓝牙技术支持设备短距离通信(一般10m内),能在包括移动电话,PDA,无线耳机,笔记本电脑,相关外设等众多设备之间进行无线信息交换,利用"蓝牙"技术,能够有效的简化设备与因特网之间的通信,从而数据传输变得更加迅速高效,为无线通信扩宽道路.蓝牙采用分散式网络结构以及快跳频和短包技术,支持点对点及点对多点通信,工作在全球通用的2.4GHz ISM(即工业,科学和医学)频段,其数据速率为1Mbps.采用时分双工传输方案实现全双工传输.
蓝牙技术是一种无线数据与语音通信的开放性全球规范,它以低成本的近距离无线连接为基础,为固定设备与移动设备通信环境建立一个特别的连接,其程序写在一个9X9mm的微芯片中.
蓝牙协议栈允许采用多种方法,包括RFCOMM和Object Exchange(OBEX),在设备之间发送和接收文件
协议栈的细节
TCP/IP
IP OBEX
BNEP RFCOMM SDP AVCTP AVDTP
L2CAP
HCI
栈的最底层是HCI,即主机控制器接口,就是主机和控制器(蓝牙设备)之间的接口,可以看到,其他层都要经过HCI,HCI上面的一层是L2CAP,即逻辑链接控制器适配协议(Logical Link Controller Adaptation Protocol).
这一层充当其他所有层的数据多路复用器,接下来一层是BNEP,即蓝牙网络封装协议(Bluetooth Network Encapsulation Protocol).使用BNEP可以在蓝牙上运行其他网络协议,例如,IP,TCP和UDP,RFCOMM称作虚拟串口协议(virtual serial port protocol),因为它允许蓝牙设备模拟串口的功能,OBEX协议层是在RFCOMM层上面实现的,如果想把数据以对象的形式传输,那么OBEX很有用.SDP是服务防线协议(Service Discovery Protocol)层,用于在远程蓝牙设备上寻找服务.最后两层是AVCTP和AVDTP,用于蓝牙上音频和视频的控制和发布.AVCTP和AVDTP是蓝牙协议中增加的相对较新的层,如果要控制媒体播放器的功能或想以立体声播放音频流,则要使用它们
Win10蓝牙技术支持两个蓝牙方案:一个是应用程序到应用程序的通信,另外一个是应用程序到设备的通信.
在每种方案中,都在应用或设备之间建立StreamSocket连接,然后再进行数据的发送和接收.
1.应用程序到应用程序的通信
应用程序到应用程序的通信过程是:应用程序使用蓝牙去查找正在广播蓝牙服务的对等的应用程序,如果在应用程序提供服务的范围内发现一个应用程序,那么该程序可以发起连接请求,当这两个应用程序接受连接,它们之间就可以进行通信了,通信的过程是使用Socket的消息发送接收机制,在Win10中使用应用程序到应用程序的蓝牙通信技术,需要在项目的Package.appxmanifest文件中添加Proximity的功能选项,表示支持临近的设备通信能力,否则程序会出现异常.
2.应用程序到设备的通信
在应用程序到设备的通信过程中,应用程序使用蓝牙去查找提供服务的设备,如果提供的服务范围之内发现一个可以连接的蓝牙设备,那么该应用程序可以发起连接请求,当应用程序和设备同时接受该连接,它们之间就可以进行通信了,通信的过程也是使用Socket的消息发送接受机制,类似于应用程序到应用程序的通信.
16.13.蓝牙编程类
在Win10中,使用蓝牙编程主要会用到PeerFinder类,PeerInformation类,StreamSocket类和ConnectionRequestedEventArgs类,因为蓝牙也是基于TCP协议进行消息传递,所以需要用到StreamSocket类
蓝牙编程类的说明
PeerFinder 
用于查找附近的设备是否有运行和当前应用程序相同的应用程序,并且可以在两个应用程序之间建立Socket连接,从而可以进行通信,对等应用程序是在其他设备上运行的应用程序的另一个新实例
PeerInformation
包含对等应用程序或设备的识别信息
StreamSocket
支持使用一个TCP的Socket流的网络通信
ConectionRequestedEventArgs
表示传递到一个应用程序的ConnectionRequested事件的属性
PeerFinder类的成员
bool AllowBluetooth
指定PeerFinder类的此实例是否可以通过使用Bluetooth来连接ProximityStreamSocket对象,如果PeerFinder的此实例可以通过使用Bluetooth来连接ProximityStreamSocket对象,则为true,否则为false,默认为true.
bool AllowWiFiDirect
指定PeerFinder类的此实例是否可以通过Wifi Direct来连接ProximityStreamSocket对象,如果PeerFinder的此实例可以通过使用Wifi Direct来连接ProximityStreamSocket对象,则为true,否则为false,默认为true
IDictionary<string,string> AlternateIdentities 获取要与其他平台上的对等应用程序匹配的备用AppId值列表,
IDictionary<string,string> AlternateIdentities
获取要与其他平台上的对等应用程序匹配的备用AppId值列表,返回要与其他平台的对等应用程序匹配的备用AppId值列表
string DisplayName 
获取或设置标识计算机到远程对等类的名称
PeerDiscoveryTypes SupportedDiscoveryTypes 
获取一个值,该值指示哪些发现选项可与PeerFinder类一同使用.
eventTypedEventHandler<object,ConnectionRequestedEventArgs> ConnectionRequested
远程对等类使用ConnectAsync方法请求连接时发生
IAsyncOperation<StreamSocket> ConnectAsync(PeerInformation peerInformation)
连接已发现了对FindAllPeersAsync方法的调用的对等类,peerInformation:表示连接到的对等类的对等类信息对象.返回通过使用所提供的临近StreamSocket对象连接远程对等类的异步操作
IAsyncOperation<IReadOnlyList<PeerInformation>> FindAllPeers.Async()
适用于无线范围内运行相同应用程序的对等计算机的异步浏览.返回通过使用Wifi直连技术浏览对等类的异步操作.
void Start(string peerMessage) 
向临近设备上的对等类应用程序传递信息
void Stop()
停止查找对等类应用程序或广播对等类连接的过程
16.14.查找蓝牙设备和对等类
查找在服务范围内的蓝牙设备和对等项是蓝牙编程的第一步,会用到PeerFinder类的FindAllPeersAsync方法去进行查找,然后以异步的方式返回查找到的对等项列表的信息IReadOnlyList<PeerInformation>,注意要使查找对等的应用程序时,在调用FindAllPeersAsync方法前必须先调用PeerFinder类的Start方法,主要的目的是启动广播服务,让对方的应用程序也能查找到自己.PeerInformation包含三个属性:一个是DisplayName表示对等项的名字,这个名字一般都是由对方的设备的名称或者查找到的应用程序自身设置的现实名字,一个是HostName表示主机名字或者IP地址,还有一个属性是ServiceName表示服务名称或者TCP协议的端口号,然后可以利用查找到的PeerInformation信息进行连接和通信
查找对等的应用程序的代码示例
``` bash
 /// <summary>
        /// 查找对等的应用程序
        /// </summary>
        async void AppToApp() { 
            //启动查找服务
            PeerFinder.Start();
            //开始查找
            ObservableCollection<PeerInformation> peers= (ObservableCollection<PeerInformation>)await PeerFinder.FindAllPeersAsync();
            if (peers.Count==0)
            {
                //未找到任何的对等项
            }
            else
            {
                //处理查找到的对等项,可以使用PeerFinder类的ConnectAsync方法来连接选择的要进行通信的对等项
            }
        }
        /// <summary>
        /// 查找蓝牙设备
        /// </summary>
        async void AppToDevice() {
            //设置查找所匹配的蓝牙设备
            PeerFinder.AlternateIdentities["Bluetooth:Paired"] = "";
            //开始查找
            ObservableCollection<PeerInformation> pairedDevices= (ObservableCollection<PeerInformation>)await PeerFinder.FindAllPeersAsync();
            if (pairedDevices.Count==0)
            {
                //没有找到可用的蓝牙设备
            }
            else
            {
                //处理查找到的蓝牙设备,可以新建一个StreamSocket对象,然后使用StreamSocket类的ConnectAsync方法通过HostName和ServiceName来连接蓝牙设备
            }

        }
```
16.15.蓝牙发送消息
蓝牙编程的发送消息机制使用的是TCP的StreamSocket的方式,原理与Socket的一致.在蓝牙连接成功后,可以获取到一个StreamSocket类的对象,然后我们使用该对象的OutputStream属性来初始化一个DataWriter对象,通过DataWriter对象来发送消息,OutputStream属性表示Socket的输出流,用于发送消息给对方.
``` bash
 /// <summary>
        /// 蓝牙发送消息
        /// </summary>
        /// <param name="message"></param>
        async void SendMessage(string message) {
            //连接选中的对等项,selectedPeer为查找到的PeerInformation对象
            StreamSocket _socket = await PeerFinder.ConnectAsync(selectedPeer);
            //创建DataWriter
            DataWriter _dataWriter= new DataWriter(_socket.OutputStream);
            //先写入发送消息的长度
            _dataWriter.WriteInt32(message.Length);
            await _dataWriter.StoreAsync();
            //最后写入发送消息的内容
            _dataWriter.WriteString(message);
            await _dataWriter.StoreAsync();
        }
```
16.16.蓝牙接收消息
蓝牙编程的接收消息机制同样也是使用TCP的StreamSocket的方式,原理与Socket一致,在蓝牙连接成功后，可以获取到一个StreamSocket类的对象,然后我们使用该对象的InputStream属性来初始化一个DataReader对象,通过DataReader对象来进行接收消息,InputStream属性表示的是Socket的输入流,用于接收对方的消息.
``` bash
 /// <summary>
        /// 蓝牙接收消息
        /// </summary>
        /// <returns></returns>
        async Task<string> GetMessage() { 
            //连接选中的对等项,selectedPeer为查找到的PeerInformation对象
            StreamSocket _socket=await PeerFinder.ConnectAsync(selectedPeer);
            //创建DataReader
            DataReader _dataReader= new DataReader(_socket.InputStream);
            //先读取消息的长度
            await _dataReader.LoadAsync(4);
            uint messageLen=(uint)_dataReader.ReadInt32();
            //最后读取消息的内容
            await _dataReader.LoadAsync(messageLen);
            return _dataReader.ReadString(messageLen);
        }
```
16.17.实例:实现蓝牙程序对程序的传输
下面给出蓝牙程序对程序传输的示例,通过蓝牙功能查找周边设备,互相建立起连接和发送测试消息
``` bash
<Page
    x:Class="App1.BtToApPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel>
            <Button x:Name="btFindBluetooth" Content="通过蓝牙查找该应用设备" Click="btFindBluetooth_Click"/>
            <ListBox x:Name="lbBluetoothApp" ItemsSource="{Binding}">
                <ListBox.ItemTemplate>
                    <DataTemplate>
                        <StackPanel>
                            <TextBlock Text="{Binding DisplayName}"/>
                            <TextBlock Text="{Binding ServiceName}"/>
                            <Button Content="连接" HorizontalAlignment="Left" Width="308" Height="91"
                                    Click="Button_Click"/>
                        </StackPanel>
                    </DataTemplate>
                </ListBox.ItemTemplate>
            </ListBox>
        </StackPanel>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Networking.Proximity;
using Windows.Networking.Sockets;
using Windows.Storage.Streams;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class BtToApPage : Page
    {
        public BtToApPage()
        {
            this.InitializeComponent();
            Loaded += BtToApPage_Loaded;
        }
        /// <summary>
        /// 页面加载事件处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void BtToApPage_Loaded(object sender, RoutedEventArgs e)
        {
            //订阅连接请求事件
            PeerFinder.ConnectionRequested += PeerFinder_ConnectionRequested;
        }

        private async void PeerFinder_ConnectionRequested(object sender, ConnectionRequestedEventArgs args)
        {
            //使用UI线程弹出连接请求的接收和拒绝弹窗
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, async () =>
            {
                MessageDialog md = new MessageDialog("是否接收"+args.PeerInformation.DisplayName+"连接请求","蓝牙连接");
                UICommand yes = new UICommand("接收");
                UICommand no = new UICommand("拒绝");
                md.Commands.Add(yes);
                md.Commands.Add(no);
                var result=await md.ShowAsync();
                if (result==yes) {
                    //连接并且发送消息
                    ConnectToPeer(args.PeerInformation);

                }
            });
        }
        /// <summary>
        /// 连接并发送消息给对方
        /// </summary>
        /// <param name="peerInformation"></param>
        /// <exception cref="NotImplementedException"></exception>
        private async void ConnectToPeer(PeerInformation peer)
        {
            _socket = await PeerFinder.ConnectAsync(peer);
            _dataReader = new DataReader(_socket.InputStream);
            _dataWriter= new DataWriter(_socket.OutputStream);
            string message = "测试消息";
            uint strLength=_dataWriter.MeasureString(message);
            //写入消息的长度
            _dataWriter.WriteUInt32(strLength);
            //写入消息的内容
            _dataWriter.WriteUInt32(strLength);
            uint numBytesWritten=await _dataWriter.StoreAsync();

        }

        //Socket数据流对象
        private StreamSocket _socket=null;
        //数据写入对象
        private DataWriter _dataWriter;
        //数据读取对象
        private DataReader _dataReader;
        /// <summary>
        /// 查找蓝牙对等项按钮事件处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btFindBluetooth_Click(object sender, RoutedEventArgs e)
        {
            string message = "";
            try
            {
                //开始查找对等项
                PeerFinder.Start();
                //等待找到的对等项
                var peers=await PeerFinder.FindAllPeersAsync();
                if (peers.Count == 0) {
                    message = "没有发现对等的蓝牙应用";
                }
                else
                {
                    //把对等项目绑定到列表中
                    lbBluetoothApp.ItemsSource= peers;
                }
               
            }
            catch (Exception ex)
            {
                if ((uint)ex.HResult==0x8007048F)
                {
                    message = "Bluetooth已关闭请打开蓝牙开关";
                }
                else
                {
                    message= ex.Message;
                }
            }
            if (message != "")
                await new MessageDialog(message).ShowAsync();
           
        }
        /// <summary>
        /// 连接蓝牙对等项的按钮事件处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void Button_Click(object sender, RoutedEventArgs e)
        {
            Button deleteButton=sender as Button;
            PeerInformation selectedPeer=deleteButton.DataContext as PeerInformation;
            //连接到选择的对等项
            _socket=await PeerFinder.ConnectAsync(selectedPeer);
            //使用输出输入流建立数据读写对象
            _dataReader = new DataReader(_socket.InputStream);
            _dataWriter= new DataWriter(_socket.OutputStream);
            //开始读取消息
            PeerFinder_StartReader();
        }
        /// <summary>
        /// 读取消息
        /// </summary>
        /// <exception cref="NotImplementedException"></exception>
        private async void PeerFinder_StartReader()
        {
            string message = "";
            try
            {
                uint bytesRead = await _dataReader.LoadAsync(sizeof(uint));
                if (bytesRead>0)
                {
                  //获取消息内容的大小
                  uint strLength=(uint)_dataReader.ReadUInt32();
                    bytesRead = await _dataReader.LoadAsync(strLength);
                    if (bytesRead>0)
                    {
                        string content = _dataReader.ReadString(strLength);
                        await new MessageDialog("获取到消息:"+content).ShowAsync();
                        //开始下一条消息读取
                        PeerFinder_StartReader();
                    }
                    else
                    {
                        message = "对方已关闭连接";
                    }
                }
                else
                {
                    message = "对方已关闭连接";
                }
            }
            catch (Exception ex)
            {
                message="读取失败:"+ex.Message;
            }
            if (message!="")
           await new MessageDialog(message).ShowAsync();
        }

    }
}

```