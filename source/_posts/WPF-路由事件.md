---
title: WPF-路由事件
categories:
  - WPF
tags:
  - WPF
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 6f6c3460
date: 2022-11-23 15:34:40
pic:
---
### 回顾.NET事件模型
``` bash
namespace BookModels
{
    public delegate void WhiteBee(string param);//声明委托
    public class XiaoLongNv
    {
        public event WhiteBee WhiteBeeEvent;//玉峰事件
        private string msg = "I am in the bottom of JueQingGu";
        public void OnFlyBee() {
            Console.WriteLine("XiaoLongNv fly the bee in the bottom of JueQingGu,Hope Yangguo one day see.");
            WhiteBeeEvent(msg);
        }
    }
    public class LaoWantong{
        public void ProcessBeeLetter(string msg) {
            Console.WriteLine("LaoWantong: Samll Bee,Small Bee,dont fly.");
        }
    }
    public class HuangRong {
        public void ProcessBeeLetter(string msg) {
            Console.WriteLine("Huangrong:\"{0}\",that is ...",msg);
        }
    }
    public class YangGuo {
        public void ProcessBeeLetter(string msg)
        {
            Console.WriteLine("Yangguo:\"{0}\",I Will get together with her!",msg);
        }

        public void Sign() {
            Console.WriteLine("YangGuo sadly say:Longer,where are you... ...");
        }
    }
}
  XiaoLongNv longNv = new XiaoLongNv();
            LaoWantong laoWantong = new LaoWantong();
            HuangRong huangRong = new HuangRong();
            YangGuo yangGuo = new YangGuo();

            longNv.WhiteBeeEvent += laoWantong.ProcessBeeLetter;
            longNv.WhiteBeeEvent+=huangRong.ProcessBeeLetter;
            // longNv.WhiteBeeEvent+=yangGuo.ProcessBeeLetter;//没有订阅事件
            longNv.OnFlyBee();
            yangGuo.Sign();
```
不过这种事件看起来不像.Net事件.改写后
``` bash
namespace BookModels
{
    public class WhiteBeeEventArgs : EventArgs
    {
        public readonly string _msg;
        public WhiteBeeEventArgs(string msg)
        {
            _msg = msg;
        }

    }

    public class XiaoLongNv2 {
        public event WhiteBeeEventHandler WhiteBeeEvent;
        private string msg = "I am in the bottom of JueQingGu";
        public void OnFlyBee()
        {
            Console.WriteLine("XiaoLongNv fly the bee in the bottom of JueQingGu,Hope Yangguo one day see.");
            WhiteBeeEventArgs args = new WhiteBeeEventArgs(msg);
            WhiteBeeEvent(this, args);
        }
    }
    public class LaoWantong2
    {
        public void ProcessBeeLetter(object sender,WhiteBeeEventArgs e)
        {
            Console.WriteLine("LaoWantong: Samll Bee,Small Bee,dont fly.");
        }
    }
    public class HuangRong2
    {
        public void ProcessBeeLetter(object sender,WhiteBeeEventArgs e)
        {
            Console.WriteLine("Huangrong:\"{0}\",that is ...", e._msg);
        }
    }
    public class Yangguo2 {
        public void ProcessBeeLetter(object sender,WhiteBeeEventArgs e)
        {
            XiaoLongNv2 xiaoLongNv2 = sender as XiaoLongNv2;
            if (xiaoLongNv2 != null) {
                Console.WriteLine("Yangguo:\"{0}\",I Will get together with her!",e._msg);
            }

        }

        public void Sign()
        {
            Console.WriteLine("YangGuo sadly say:Longer,where are you... ...");
        }
    }
	//声明委托
    public delegate void WhiteBeeEventHandler(object sender, WhiteBeeEventArgs e);
}
            XiaoLongNv2 xiaoLongNv2 = new XiaoLongNv2();
            LaoWantong2 laoWantong2 = new LaoWantong2();
            HuangRong2 huangRong2=new HuangRong2();
            Yangguo2 yangguo2 = new Yangguo2();

            XiaoLongNv2 xiaoLongNv21 = new XiaoLongNv2();
            xiaoLongNv21.WhiteBeeEvent+=laoWantong2.ProcessBeeLetter;
            xiaoLongNv21.WhiteBeeEvent += huangRong2.ProcessBeeLetter;
            xiaoLongNv21.OnFlyBee();
            yangguo2.Sign();
```
1.委托类型名称以EventHandler结束,原型有一个void返回值并接受两个输入参数,即一个Object类型和一个EventArgs类型(或继承自EventArgs)
``` bash
public delegate void WhiteBeeEventHandler(object sender,WhiteBeeEventArgs e);
```
2.事件的命名为委托去掉EventHandler之后剩余的部分.
``` bash
public event WhiteBeeEventHandler WhiteBee;
```
3.继承自EventArgs的类型应该以EventArgs结尾.
``` bash
public class WhiteBeeEventArgs:EventArgs{
	public readonly string _msg;
	public WhiteBeeEventArgs(string msg){
	this._msg=msg;
	}
}
```
### 路由事件的定义
以Button的Click事件为例.该事件是个路由事件.
```bash
public abstract class ButtonBase:ContentControl,ICommandSource{
	//路由事件的定义
	public static readonly RoutedEvent ClickEvent;
	//传统的事件包装器
	public event RoutedEventHandler Click{
	add{
	base.AddHandler(ClickEvent,value);
	}
	remove{
	base.Removehandler(ClickEvent,value);
	}
}
//事件的注册
	static ButtonBase(){
	ClickEvent=EventManager.RegisterRoutedEvent("Click",RoutingStrategy.Bubble,typeof(RoutedEventHandler),typeof(ButtonBase));
}	
}
```
同依赖属性一样,路由事件也需要注册,不同的是使用EventManager.RegisterRoutedEvent方法.
同依赖属性,用户不会直接使用路由事件,而是使用传统的Clr事件.有两种方式关联事件及其处理函数.
在代码中,仍然按照原方法关联和解除关联事件处理函数(+=/-=).
``` bash
Button b2=new Button();
//关联事件及其处理函数
b2.Click+=new RoutedEventHandler(Onb2Click);
//事件处理函数
void Onb2Click(object sender,RoutedEventArgs e){
	
}
*********************************************************
Button b2=new Button();
//关联事件及其处理函数
b2.AddHandler(Button.ClickEvent,new RoutedEventHandler(Onb2Clcik));
//事件处理函数

```
传统的事件触发往往直接调用其委托(因为事件的本质是委托),而路由事件则通过一个RaiseEvent方法触发,调用该方法后所有关联该事件的对象都会得到通知.
``` bash
RoutedEventArgs e=new RoutedEventArgs(ClickEvent,this);
base.RaiseEvent(e);
```
路由事件通过EventManager.RegisterRoutedEvent方法注册,通过AddHandler和RemoveHandler来关联和解除关联的事件处理函数,通过RaiseEvent方法来触发事件,通过传统的CLR事件封装后供用户调用,使得用户同使用传统的CLR事件一样使用路由事件.
### 路由事件的旅行
1.路由事件的旅行策略
旅行当中,一般只出现两种角色:1.事件源.由其触发事件,是路由事件的起点.2.是事件监听者,通常针对监听的事件有一个相应的事件处理函数,当路由事件经过事件监听者,就好比经过一个客栈,要做短暂停留,由事件处理函数来处理该事件.
路由事件的策略有如下三种:
1.Bubbing:事件从事件源出发一路上溯直到根节点,很多路由事件使用该策略.
2.Direct 事件从事件源出发,围绕事件源转一圈结束.
3.Tunneling 事件源触发事件后,事件从根节点出发下沉直到事件源.
#### 改变旅行策略因素之一 事件处理函数
最基本的路由事件处理函数原型
public delegate void RoutedEventHandler(Object sender,RoutedEventArgs e)
事件处理函数之间有微小差异.鼠标事件的处理函数原型
public delegate void MouseEventhandler(Object sender,MouseEventArgs e)
这种事件处理函数有如下两个特点
1.返回原型为void.
2.有两个参数,第一个是一个Object类型的对象.表示拥有该事件处理函数的对象.第二个是RoutedEventArgs或者是RoutedEventArgs的派生类.带有其路由事件的信息.
RoutedEventArgs结构包括4个成员变量.
Source 表明触发事件的源 如当键盘事件发生时,触发事件的源是当前获得焦点的对象.当鼠标事件发生时,触发事件的源是鼠标所在的最上层对象
OriginalSource 表明触发事件的源.一般来说OriginalSource和Source相同,区别在于Source表示逻辑树上的元素;OriginalSource是可视树中的元素.如单击窗口的边框,Source为Window,OriginalSource为Border.
RoutedEvent 路由事件对象
Handled 布尔值 为true 表示该事件已处理,这样可以停止路由事件.
Handled属性是改变路由事件旅行的元凶,一旦在某个事件处理函数中将Handled的值设置为true.路由事件就停止传递.
一个事件被标记为处理,事件处理函数则不可处理该事件,WPF提供了一种机制,即使事件被标记为处理,事件处理函数仍然可以处理.但是关联事件及其处理函数需要稍作处理.AddHandler重载了两个方法,其中之一如下,需要将第三个参数设为true.
public void Addhandler(RoutedEvent routedEvent,Delegate handler,bool handledEventsToo)
####  改变旅行策略因素之二 类和实例事件处理函数
事件处理函数有两种类型,一是前面所说的普通事件处理函数.二是通过EventManager.RegisterClassHandler方法将一个事件处理函数和一个类关联起来,这种事件处理函数,
称为类事件处理函数.其优先权高于前者.也就是说事件在旅行时,会先光临类事件处理函数,然后再光临类事件处理函数.
### 路由事件示例
``` Bash
public class MySimpleButton:Button{
	static MySimpleButton(){}
	//创建和注册该事件,该事件路由策略为Bubble.
public static readonly RoutedEvent CustomClickEvent=EventManager.RegisterRoutedEvent(
"CustomClick",RoutingStrategy.Bubble,typeof(RoutedEventHandler),typeof(MySimpleButton)
);
//Clr事件的包装器、
public event RoutedEventHandler CustomClick{
	add{AddHandler(CustomClickEvent,value);}
	remove{RemoveHandler(CustomClickEvent,value);}
}
//触发CustomClickEvent
void RaiseCustomClickEvent(){
	RoutedEventArgs newEventArgs=new RoutedEventArgs(MySimpleButton.CustomClickEvent);
	RaiseEvent(newEventArgs);

}
//OnClick触发CustomClickEvent
protected override void OnClick(){
	RaiseCustomClickEvent();
}
}

<Window x:Class="WpfApp1.Window2"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        xmlns:custom="clr-namespace:BookModels;assembly=BookModels"
        mc:Ignorable="d"
        Title="Window2" Height="450" Width="800" Loaded="Window_Loaded">
    <Grid Margin="3" custom:MySimpleButton.CustomClick="InsertList"  Name="grid1">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <custom:MySimpleButton x:Name="simpleBtn" CustomClick="InsertList">
            MySimpleButton
        </custom:MySimpleButton>
        <ListBox Margin="5" Name="lstMessage" Grid.Row="1"/>
        <CheckBox Margin="5" Grid.Row="2" Name="chkHandle">Handle first event
        </CheckBox>
        <Button Grid.Row="3" HorizontalAlignment="Right" Margin="5" Padding="3"
                Click="Button_Click">Clear List
        </Button>
    </Grid>
</Window>

    public partial class Window2 : Window
    {
        public Window2()
        {
            InitializeComponent();
            //MySimpleButton的类事件处理函数处理过Window能够得到通知
            this.simpleBtn.ClassHandlerProcessed += SimpleBtn_ClassHandlerProcessed;
        }

        private void SimpleBtn_ClassHandlerProcessed(object sender, EventArgs e)
        {
            eventCounter++;
            string message = $"#{eventCounter}:\r\n Windows Class Handler \r\n Sender:{sender}";
            lstMessage.Items.Add(message);
        }
        private void ProcessHandlersToo(object sender, RoutedEventArgs e)
        {
            eventCounter++;
            string message = $"#{eventCounter}:\r\n ProcessHandlerToo \r\n Sender:{sender} \r\n Source:{e.Source} \r\n Original Source:{e.OriginalSource}";
            lstMessage.Items.Add(message);
        }
        public int eventCounter = 0;
        private void InsertList(object sender, RoutedEventArgs e)
        {
            eventCounter++;
            string message=$"#{eventCounter}:\r\n InsertList \r\n Sender:{sender} \r\n Source:{e.Source} \r\n Original Source:{e.OriginalSource}";
            lstMessage.Items.Add(message);
            e.Handled = (bool)chkHandle.IsChecked;
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            eventCounter = 0;
            lstMessage.Items.Clear();
        }

        private void Window_Loaded(object sender, RoutedEventArgs e)
        {
            grid1.AddHandler(MySimpleButton.CustomClickEvent,new RoutedEventHandler(ProcessHandlersToo),true);
        }
    }

```
