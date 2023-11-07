---
title: 深入浅出Windows通用应用程序开发(一)
categories:
  - Windows应用程序开发
tags:
  - UWP
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 7b676f35
date: 2022-11-29 13:15:57
pic:
---
### 创建Windows 10通用应用
## 创建Hello World项目
1.打开vs,New Project,C#,Windows,Universal/UWP,选择一个空白项目模板(Universal Windows),单击OK按钮完成项目的创建.
2.MainPage.xaml
``` bash
<Page
    x:Class="App1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Button HorizontalAlignment="Center" VerticalAlignment="Center" Content="Hello,App" Click="Button_Click"/>
    </Grid>
</Page>

```
3.编辑部署程序
选择Deploy Solution在模拟器上运行应用程序.
### 解析应用
1.MainPage.xaml
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
表示当前背景使用的是系统的主题资源背景.
2.App.xaml.cs
``` bash
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.ApplicationModel;
using Windows.ApplicationModel.Activation;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Media.Animation;
using Windows.UI.Xaml.Navigation;

namespace App1
{
    /// <summary>
    /// 提供特定于应用程序的行为，以补充默认的应用程序类。
    /// </summary>
    sealed partial class App : Application
    {

        private TransitionCollection transitions;
        /// <summary>
        /// 初始化单一实例应用程序对象。这是执行的创作代码的第一行，
        /// 已执行，逻辑上等同于 main() 或 WinMain()。
        /// </summary>
        public App()
        {
            this.InitializeComponent();
            this.Suspending += OnSuspending;
        }

        /// <summary>
        /// 在应用程序由最终用户正常启动时进行调用。
        /// 将在启动应用程序以打开特定文件等情况下使用。
        /// </summary>
        /// <param name="e">有关启动请求和过程的详细信息。</param>
        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // 不要在窗口已包含内容时重复应用程序初始化，
            // 只需确保窗口处于活动状态
            if (rootFrame == null)
            {
                // 创建要充当导航上下文的框架，并导航到第一页
                rootFrame = new Frame();
                //设置当前的页面框架可以缓存的页面数量

                rootFrame.CacheSize=1;

                rootFrame.NavigationFailed += OnNavigationFailed;
                //判断程序的上一个状态.
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //如果是从休眠程序启动,这里要加载上一个状态的相关数据
                    //TODO: 从之前挂起的应用程序加载状态
                }

                // 将框架放在当前窗口中
                Window.Current.Content = rootFrame;
            }

            if (e.PrelaunchActivated == false)
            {
                //判断框架内容是否为空
                if (rootFrame.Content == null)
                {
                    //如果相关的动画值不为空则添加动画
                    if (rootFrame.ContentTransitions!=null)
                    {
                        this.transitions= new TransitionCollection();
                        foreach (var item in rootFrame.ContentTransitions)
                        {
                            this.transitions.Add(item);
                        }
                    }
                    rootFrame.ContentTransitions= null;
                    //添加导航跳转完成的事件
                    rootFrame.Navigated += RootFrame_Navigated;
                    ///导航跳转到引用程序的MainPage页面
                    if (!rootFrame.Navigate(typeof(MainPage), e.Arguments)) {
                        throw new Exception("Failed to create initial page.");
                    }
                    // 当导航堆栈尚未还原时，导航到第一页，
                    // 并通过将所需信息作为导航参数传入来配置
                    // 参数
                    rootFrame.Navigate(typeof(MainPage), e.Arguments);
                }
                // 确保当前窗口处于活动状态
                Window.Current.Activate();
            }
        }
        //导航事件完成的处理程序,从新创建启动动画的集合.
        private void RootFrame_Navigated(object sender, NavigationEventArgs e)
        {
            var rootFrame = sender as Frame;
            rootFrame.ContentTransitions=this.transitions= new TransitionCollection() { new NavigationThemeTransition()};
            rootFrame.Navigated-= RootFrame_Navigated;
        }

        /// <summary>
        /// 导航到特定页失败时调用
        /// </summary>
        ///<param name="sender">导航失败的框架</param>
        ///<param name="e">有关导航失败的详细信息</param>
        void OnNavigationFailed(object sender, NavigationFailedEventArgs e)
        {
            throw new Exception("Failed to load Page " + e.SourcePageType.FullName);
        }

        /// <summary>
        /// 在将要挂起应用程序执行时调用。  在不知道应用程序
        /// 无需知道应用程序会被终止还是会恢复，
        /// 并让内存内容保持不变。
        /// </summary>
        /// <param name="sender">挂起的请求的源。</param>
        /// <param name="e">有关挂起请求的详细信息。</param>
        private void OnSuspending(object sender, SuspendingEventArgs e)
        {
            var deferral = e.SuspendingOperation.GetDeferral();
            //TODO: 保存应用程序状态并停止任何后台活动
            deferral.Complete();
        }
    }
}

```
应用程序的三种主要状态,Running,NotRunning,Suspended.
1.应用启动 从其他状态到Running
2.应用激活 从NotRunning到Running
activated事件参数包括一个PreviousExecutionState属性,该属性告诉你应用在激活之前处于哪种状态.此属性是ApplicationExecutionState属性枚举值之一,CloseByUser,Terminated,NotRunning.PreviousExecutionState.Running,Suspended.应用以前不是被终止,因此不用担心还原数据.
3.应用挂起.从Running状态到Suspended状态.
4.应用恢复 从Suspended状态到Running状态.

3.Package.appxmanifest 
声明应用的标识,应用的功能以及用来进行部署和更新的信息.
Package元素是整个清单的根节点.Identity元素表示应用程序版本发布者名称的信息.mp:PhoneIdentity元素表示应用程序相关的唯一标识符信息.Properties元素包含应用程序的名称,发布者名称等信息的设置.Prerequisites元素则是用于设置应用程序所支持的系统版本号,Resources元素表示应用程序所使用资源信息.Applicaitons元素包含了与应用程序相关的logo设置,闪屏图片设置等可视化的设置信息.Capablities元素表示当前应用程序所使用的一些手机特定功能,例如internetClient表示使用网络的功能
### 不同平台设备的适配
## 特定平台的API调用
Window10里面有Windows Mobile Extension SDK和Windows Desktop Extension SDK分别表示手机版本和桌面版本的扩展SDK.都是内置在WIndow10SDK里面的,默认情况下不会给项目工程加上,需要使用时可以在项目工程里引用.Project,Add Reference,Window Universal,Extensions.在使用Api时必须判断当前环境支不支持.
判断方法为Windows.Foundation.Metadata.ApiInformation.IsTypePresent(string typeName),typename表示带完整命名空间的API名称,i返回true表示支持.ji返回false表示不支持.下面通过一个手机硬件后退键的适配来演示如何使用和适配特定平台的API.
首先创建WIndow10通用应用程序项目,BackButtonDemo,在项目里面引用Windows Mobile Extension SDK.
1.BlackPage.xaml
``` bash
<Page
    x:Class="App1.BlankPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel VerticalAlignment="Top" Orientation="Horizontal">
            <Button x:Name="backButton" Margin="24,24,24,24" Click="backButton_Click" Style="{StaticResource NavigationBackButtonNormalStyle}"/>
            <TextBlock Text="你好" FontSize="30" HorizontalAlignment="Left" VerticalAlignment="Center"/>
            
        </StackPanel>
    </Grid>
</Page>
//App.xaml.cs
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
    public sealed partial class BlankPage : Page
    {
        public BlankPage()
        {
            this.InitializeComponent();
            if (App.IsHardwareButtonsAPIPresent)
            {
                backButton.Visibility = Visibility.Collapsed;
            }
            else
            {
                backButton.Visibility = Visibility.Visible;
            }
        }
        /// <summary>
        /// 后退按钮处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void backButton_Click(object sender, RoutedEventArgs e)
        {
            if(this.Frame.CanGoBack)
            { this.Frame.GoBack(); }
        }
    }
}
///MainPage.xaml
<Page
    x:Class="App1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <StackPanel Orientation="Vertical">
            <Button HorizontalAlignment="Center" VerticalAlignment="Center" Content="Hello,App" Click="Button_Click"/>
            <Button Content="跳转到新的页面" Click="Button2_Click" VerticalAlignment="Center" HorizontalAlignment="Center"/>
            
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

// https://go.microsoft.com/fwlink/?LinkId=402352&clcid=0x804 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
        }

        private async void Button_Click(object sender, RoutedEventArgs e)
        {
            MediaElement media=new MediaElement();
            var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
            Windows.Media.SpeechSynthesis.SpeechSynthesisStream stream = await synth.SynthesizeTextToStreamAsync("Hello,App!");
            media.SetSource(stream,stream.ContentType);
            media.Play();   
            
        }

        private void Button2_Click(object sender, RoutedEventArgs e)
        {
            this.Frame.Navigate(typeof(BlankPage));
        }
    }
}

```
### 界面适配
1.使用相对的布局控件来进行排列,如Grid,RelativePanel等.
2.使用相对的属性来设置控件在容器的位置.如orizontaolAligment，VertaicalAlignment属性
3.设置长度宽度的最大值最小值限制;
4.根据设备实际分辨率来设置长度宽度或者位置;
#### xaml界面原理和语法
### 对象元素
Xaml的对象元素是指Xaml中一个完整的节点.
### 设置属性
1.使用属性语法
2.使用属性元素语法
3.使用内容元素语法
4.使用集合语法
### 附加属性
### 标记扩展
1.Binding 实现在Xaml载入时,将数据绑定到xaml对象.
2.StaticResource标记扩展,实现引用数据字典中定义的静态资源.
3.ThemeResource标记扩展,表示系统内置的静态资源.
4.TemplateBinding标记扩展,实现在Xaml页面中对象模板绑定调用.
5.RelativeSource标记扩展,实现对特定数据源的绑定.
### 事件
<元素对象 事件名称 ="事件处理">
### Xaml原理
## 动态加载xaml
动态加载Xaml是指在程序运行时通过解析xaml格式的字符串或文件来动态生成UI的效果.
使用XamlReader.Load方法动态加载XAML对XAML的字符串是有一定要求的,这些格式良好的XAML片段必须符合以下要求:
1.Xaml内容串必须定义单个根元素,使用XamlReader.Load创建的内容只能赋予一个Win10对象,它们是1对1关系.
2.内容字符串Xaml必须是格式良好的xml,并且必须是可分析的Xaml.
3.所需的根元素还必须指定某一默认的Xml命名空间值,这通常是命名空间xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
动态加载Xaml的示例:演示了使用XamlReader.Load方法加载Xaml字符串生成一个按钮和加载XAML文件生成一个矩形
``` bash
   <StackPanel x:Name="sp_Show">
                <Button x:Name="bt_addXaml" Content="加载Xaml按钮" Click="bt_addXaml_Click"/>
            </StackPanel>

        /// <summary>
        /// 加载Xaml按钮
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bt_addXaml_Click(object sender, RoutedEventArgs e)
        {
            //注意Xaml字符串里面的命名空间"http://schemas.microsoft.com/winfx/2006/presentation"不能少
            string buttonXaml = $"<Button xmlns='http://schemas.microsoft.com/winfx/2006/xaml/presentation' Content=\"加载XAML文件\" Foreground=\"Red\"/>";
            Button btnRed=(Button)XamlReader.Load(buttonXaml);
            btnRed.Click += BtnRed_Click;
            sp_Show.Children.Add(btnRed);
        }
        /// <summary>
        /// 已加载的XAML按钮关联的事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void BtnRed_Click(object sender, RoutedEventArgs e)
        {
            string xaml = string.Empty;
            //加载程序的Retangle.xaml文件
            StorageFile fileRead=await Windows.ApplicationModel.Package.Current. InstalledLocation.GetFileAsync("Rectangle.xaml");
            //读取文件的内容
            xaml=await FileIO.ReadTextAsync(fileRead);
            //加载Rectangle
            Rectangle rectangle=(Rectangle)XamlReader.Load(xaml);
            sp_Show.Children.Add(rectangle);
        }

/// Rectangle.xaml
<Rectangle
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Height="100" Width="200"
    >

    <Rectangle.Fill>
        <LinearGradientBrush>
            <GradientStop Color="Black" Offset="0"/>
            <GradientStop Color="Red" Offset="0.5"/>
            <GradientStop Color="Black" Offset="1"/>
        </LinearGradientBrush>
    </Rectangle.Fill>
</Rectangle>

```
添加Rectangle.xaml文件,需要手动设置Build Action属性为Content.表示作为内容资源来使用。
#### XAML树结构
### 可视化树
VisualTreeHelper类
VisualTreeHelper是一个静态帮助器类,它提供了一个在可视化对象级别编程的低级功能
提供了用来枚举可视化树成员的功能.可以在运行时对可视化树执行操作,并且可以遍历到模板部件,这是一种可用来检查模板组成情况的有用手段.此外,可以检查可能通过数据绑定填充的子集合，或者是应用程序代码可能无法全部了解运行时对象树的完整本质的子集合.若要检索父级,请调用GetParent方法.若要检索父级,请调用GetParent方法,若要检索可视化对象的子级或直接子代,请调用GetChild方法,此方法返回父级在指定索引处的子对象.
VisualTreeHelper4个常用静态方法
1.FindElementsInHostCoordinates 检索一组对象,这些对象位于某一对象的坐标空间的指定点或矩形内
2.GetChild 使用提供的索引,通过检查可视化树获取所提供对象的特定子对象
3.GetChildrenCount 返回在可视化树中在某一对象的子集合中存在的子级的数目.
4.GetParent 返回可视化树中某一对象的父对象.
### 遍历可视化树
``` bash
  <StackPanel x:Name="stackPanel" Margin="12,0,12,0">
                <TextBox>
                </TextBox>
                <Button Content="遍历" Click="Button_Click1">
                </Button>
            </StackPanel>

 string visualTreeStr = "";
        /// <summary>
        /// 单击事件,弹出XAML页面里面StackPanel控件的可视化树的所有对象.
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void Button_Click1(object sender, RoutedEventArgs e)
        {
            visualTreeStr = "";
            GetChildType(stackPanel);
            MessageDialog messageDialog = new MessageDialog(visualTreeStr);
            await messageDialog.ShowAsync();

        }
        /// <summary>
        /// 获取某个XAML元素的可视化对象的递归方法
        /// </summary>
        /// <param name="stackPanel"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void GetChildType(DependencyObject reference)
        {
            //获取子对象的个数
            int count=VisualTreeHelper.GetChildrenCount(reference);
            //如果子对象的个数不为0 继续递归
            if (count>0)
            {
                for (int i = 0; i <= VisualTreeHelper.GetChildrenCount(reference)-1; i++)
                {
                    //获取当前节点的子对象.
                    var child=VisualTreeHelper.GetChild(reference, i);
                    //获取子对象的类型
                    visualTreeStr += child.GetType().ToString() + count + "";
                    //递归调用
                    GetChildType(child);
                }
            }
        }
```
### 框架和页面
Frame frmae=Window.current.Content as Frame;
this.Frame.Navigate(typeof(BlackPage1));
### 页面导航
应用的导航历史记录是一种后进先出的堆栈结构.该结构还称为后退堆栈,因为它所包含的一组页面在后退导航的堆栈结构中,最新的页面会被添加到顶部,此操作称为推送操作.删除堆栈顶部项的操作称为弹出操作.
还可以使用Frame类BackStack属性获取后退堆栈的项目,后退堆栈的项目为PageStackEntry类的对象.PackStackEntry类表示后退或前进导航历史记录中的一个条目.通过PageStackEntry类的Type属性和Parameter属性可以知道导航过来的Page对象的类型和参数.
#### 控件编程
扁平化设计风格
系统控件基类有FrameworkElement类.Panel类,Control类.ContentControl类.和ItemControl类.所有的系统控件都派生自这5个基类.这5个基类自身之间也是派生的关系.
FrameworkElement类
Panel类 Control类
Control类
ContentControl类 ItemControl类
1.FrameworkElement类
2.Panel类
布局
3.Control类
表示用户界面元素的基类.这些元素使用ControlTemplate来定义其外观.ControlTemplate是控件的模板.由多个元素组成,并且可以直接由用户去定义和修改.
4.ContentControl类
Content属性
5.ItemsControl类.
ItemsControl类是派生自Control类,表示一个可用于呈现项的集合的控件.ItemsControl类本身也是一个列表控件类.或用于显示从指向某个对象的数据绑定中获取的列表.当然,ItemsControl类只是封装了列表的一些基本特性,ItemSource属性,指定为对某个对象集合的引用.

5各类别
1.面板控件 Pannel派生 Canvas,Grid,StackPanel,RelativePanel.
2.内容控件 Button,RadioButton,HyperlinkButton,CheckBox,ScrollViewer.
<ContentPresenter Content="{TemplateBinding Content}" ContentTemplate="{TemplateBinding ContentTemplate}" ContentTemplateSelector="{TemplateBinding ContentTemplateSelector}"/>
ContentPresenter元素用于ContentControl的Template的内部,不同的内容控件(如Button,CheckBox)会把ContentPresenter放置在控件内部不同的地方.内容控件通过给Content属性赋值就会把相关信息传递到了ContentPresenter里面进行呈现和显示.
3.列表控件
这类控件由ItemsControl类派生,经常用于显示数据的集合,包括ListBox,Pivot,ListView和GridView控件.
最主要的属性 Items表示生成控件内容的集合.ItemsPanel表示定义了控制项的布局的面板.ItemSource表示生成ItemsControl的内容的集合.ItemTemplate表示用于显示每个项的DataTemplate.
4.普通控件
直接派生Control类，包含TextBox,PasswordBox,ProgressBar,ScrollBar,Slider等控件.这类控件的共性特点是可以自定义或者修改其控件的数据模板,也就是说控件内部的呈现元素可以通过模板去修改.
5.其他控件
FrameworkElement TextBlock. Border.Image,MediaElement.Popup.
4.2 Button
1.Windows.UI.Controls.Button.
按钮控件三个状态. 分别是按下状态(Press),悬停状态(Hover),释放状态(Release) 通过设置按钮控件的ClickMode属性来控制按钮在哪种状态下才执行Clcik事件.
2.文字的相关属性
Win10内置的SymbolIcon图形作为Content属性.在Visual Studio里面打开按钮属性窗口.
选择Symbol的图形就可以看到一系列扁平化的图标图形.
TextBox
如需支持Enter换行 需要把AcceptsReturn设为true.
通过InputScope属性来设置在控件输入信息所提供的键盘类型,比如TextBox只是要求用户输入手机号,
设置InputScope="TelephoneNumber"来制定电话号码的输入键盘.可以通过枚举值InputScopeNameValue来看到所有的键盘类型,包括EmailSmtpAddress,Url,Number等.
textBox1.InputScope=new InputScope();
textBox1.InputScope.Names.Add(new InputScopeName(){NameValue=InputScopeNameValue.TelephoneLocalNumber});
3.控件头
Header 请输入用户名
4.5边框 Border
边框是指在另一个对象周围绘制边框,背景或同时绘制二者的控件.
4.6 超链接
<HyperlinkButton .../> 或 <HyperlinkButton>内容</HyperlinkButton>                <HyperlinkButton Content="Google" NavigateUri="http://goole.com"/>
4.7 单选按钮
4.9 ProgressBar
重复模式 非重复模式 IsIndeterminate true 重复 默认
``` bash
<TextBlock Text="选择ProgressBar的类型:"/>
                <RadioButton Content="Determinate类型" Height="71" Name="radioButton1" GroupName="Type"/>
                <RadioButton Content="Indeterminate类型" Height="71" Name="radioButton2" GroupName="Type" IsChecked="True"/>
                <Button Content="启动ProgressBar" Height="72" x:Name="begin" Click="begin_Click"/>
                <Button Content="取消ProgressBar" Height="72" x:Name="cancel" Click="cancel_Click"/>
                <ProgressBar x:Name="progressBar1" IsIndeterminate="True"/> 

  private void cancel_Click(object sender, RoutedEventArgs e)
        {
            //隐藏进度条
            progressBar1.Visibility= Visibility.Collapsed;
        }

        private void begin_Click(object sender, RoutedEventArgs e)
        {
            //设置进度条为可见
            progressBar1.Visibility = Visibility.Visible;
            if (radioButton1.IsChecked==true)
            {
                //设置进度条为不可重复模式
                progressBar1.IsIndeterminate = false;
                //使用一个定时器,每一秒钟触发以下进度的改变
                DispatcherTimer timer= new DispatcherTimer();
                timer.Interval = TimeSpan.FromSeconds(1);
                timer.Tick += Timer_Tick;
                timer.Start();
            }
            else
            {
                //设置进度条的值为0
                progressBar1.Value = 0;
                //设置进度条为重复模式
                progressBar1.IsIndeterminate=true;

            }
        }
        //定时器的定时触发的事件处理
        private async void Timer_Tick(object sender, object e)
        {
            //如果进度没有到达100则在原来的进度基础上再增加10
            if (progressBar1.Value<100)
            {
                progressBar1.Value += 10;
            }
            else
            {
                
                //进度完成
                (sender as DispatcherTimer).Tick-= Timer_Tick;
                //停止定时器的运行
                (sender as DispatcherTimer).Stop();
                await new MessageDialog("进度完成").ShowAsync();

            }
        }
```
4.10 滚动视图(ScrollViewer)
``` bash
 <ScrollViewer Height="200" Width="200" VerticalScrollBarVisibility="Auto" HorizontalScrollBarVisibility="Auto">
                    <ScrollViewer.Content>
                        <StackPanel>
                            <Image Source="/Assets/StoreLogo.png"/>
                        </StackPanel>
                    </ScrollViewer.Content>
                </ScrollViewer>

                <StackPanel>
                    <ScrollViewer Name="scrollViewer1" VerticalScrollBarVisibility="Hidden" Height="300">
                        <StackPanel Name="stkpnImage"/>
                    </ScrollViewer>
                    <Button Content="往上" FontSize="30" Click="btnUp_Click"/>
                    <Button Content="往下" FontSize="30" Click="btnDown_Click"/>
                    <Button Content="停止" FontSize="30"
                            Click="stop_Click"/>
                       //添加图片到ScrollViewer里面的StackPanel中.
            for (int i = 0; i <=30; i++)
            {
                Image imgItem = new Image();
                imgItem.Width = 200;
                imgItem.Height = 200;
                //4张图片循环添加到StackPanel的子节点上
                if (i % 4 == 0)
                {
                    imgItem.Source = (new BitmapImage(new Uri("a.jpg", UriKind.RelativeOrAbsolute)));

                }
                else if (i % 4 == 1)
                {
                    imgItem.Source = (new BitmapImage(new Uri("b.jpg", UriKind.RelativeOrAbsolute)));
                }
                else if (i % 4 == 2)
                {
                    imgItem.Source = (new BitmapImage(new Uri("c.jpg", UriKind.RelativeOrAbsolute)));
                }
                else if (i % 4 == 3) {
                    imgItem.Source = (new BitmapImage(new Uri("d.jpg", UriKind.RelativeOrAbsolute)));
                };
                this.stkpnImage.Children.Add(imgItem);
            }
            //初始化tmrDown定时触发器
            tmrDowm = new DispatcherTimer();
            //每500毫秒跑一次
            tmrDowm.Interval=new TimeSpan(500);
            //加入每次tick的事件
            tmrDowm.Tick += TmrDowm_Tick;
            //初始化tmrUp定时触发器
            tmrUp=new DispatcherTimer();
            tmrUp.Interval=new TimeSpan(500);
            tmrUp.Tick += TmrUp_Tick;

 private DispatcherTimer tmrDowm;
        //往上滚动的定时触发器
        private DispatcherTimer tmrUp;
        //往上按钮事件
        private void btnUp_Click(object sender, RoutedEventArgs e)
        {
            tmrDowm.Stop();
            tmrUp.Start();
        }

        private void TmrUp_Tick(object sender, object e)
        {
            //将VerticalOffset-10将出现图片将往上滚动的效果
            scrollViewer1.ChangeView(0,scrollViewer1.VerticalOffset-10,0);
        }
        private void TmrDowm_Tick(object sender, object e)
        {
            //先停止往上的定时触发器
            tmrUp.Stop();
            //将VerticalOffset+10将出现图片往下滚动的效果
            scrollViewer1.ChangeView(0, scrollViewer1.VerticalOffset + 10, 0);
        }

        private void btnDown_Click(object sender, RoutedEventArgs e)
        {
            tmrUp.Stop();
            tmrDowm.Start();
        }

        private void stop_Click(object sender, RoutedEventArgs e)
        {
            tmrDowm.Stop();
            tmrUp.Stop();
        }
```
4.11.Slider
``` bash
 <StackPanel Grid.Row="1">
            <Grid Name="controlGrid">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*"/>
                    <RowDefinition Height="*"/>
                    <RowDefinition Height="*"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*"/>
                    <ColumnDefinition Width="*"/>
                    <ColumnDefinition Width="*"/>
                </Grid.ColumnDefinitions>
                <!--设置红色-->
                <TextBlock Text="红色" Foreground="Red" FontSize="20"/>
                <Slider x:Name="redSlider" Grid.Row="1" Foreground="Red" Minimum="0" Maximum="255"
                        ValueChanged="redSlider_ValueChanged"/>
                <TextBlock x:Name="redText" Grid.Row="2" Text="0" Foreground="Red" FontSize="20"/>
                <!--设置绿色-->
                <TextBlock Grid.Column="1" Text="绿色" Foreground="Green" FontSize="20"/>
                <Slider x:Name="greenSlider" Foreground="Green" FontSize="20" Minimum="0" Maximum="255" ValueChanged="redSlider_ValueChanged" Grid.Column="1" Grid.Row="1"></Slider>
                <TextBlock x:Name="greenText" Grid.Column="1" Grid.Row="2" Foreground="Green" FontSize="20"/>
                <!--设置蓝色-->
                <TextBlock Grid.Column="2" Grid.Row="0" Text="蓝色" Foreground="Blue" FontSize="20"/>
                <Slider x:Name="blueSlider" Grid.Column="2" Grid.Row="1" Foreground="Blue" Minimum="0" Maximum="255" ValueChanged="redSlider_ValueChanged"/>
                <TextBlock x:Name="blueText" Grid.Column="2" Grid.Row="2" Text="0" Foreground="Blue" FontSize="20"/>
                
                <!--设置-->
            </Grid>
            <Ellipse Height="100" x:Name="ellipse1" Stroke="Black" StrokeThickness="1" Width="224"/>
            <TextBlock x:Name="textBlockColor" Text="颜色" FontSize="26"/>
            
        </StackPanel>
//三个Slider控件的初始值
            redSlider.Value = 128;
            greenSlider.Value= 128;
            blueSlider.Value= 128;
            if (App.IsHardwareButtonsAPIPresent)
            {
                backButton.Visibility = Visibility.Collapsed;
            }
            else
            {
                backButton.Visibility = Visibility.Visible;
            }
 private void redSlider_ValueChanged(object sender, RangeBaseValueChangedEventArgs e)
        {
            //
            Color clr = Color.FromArgb(255,(byte)redSlider.Value,(byte)greenSlider.Value,(byte)blueSlider.Value);
            //将三种颜色的混合色设置为ellipse1的填充颜色
            ellipse1.Fill = new SolidColorBrush(clr);
            textBlockColor.Text = clr.ToString();
            redText.Text = clr.R.ToString("X2");
            greenText.Text = clr.G.ToString("X2");
            blueText.Text = clr.B.ToString("X2");
        }

```
4.12 时间选择器和日期选择器
``` bash
 <StackPanel Grid.Row="2">
            <TimePicker x:Name="time" Header="请选择时间:" TimeChanged="time_TimeChanged"/>
            <DatePicker x:Name="date" Header="请选择日期:"
                        DateChanged="date_DateChanged"/>
            <TextBlock x:Name="info" FontSize="20" TextWrapping="Wrap"/>
        </StackPanel>
  ///DatePicker TimePicker
            //控件默认的时间和日期是设备当前的时间和日期
            info.Text = $"时间:{time.Time.ToString()} 日期:{date.Date.ToString()}";
private void time_TimeChanged(object sender, TimePickerValueChangedEventArgs e)
        {
            info.Text = $"时间改变为:{time.Time} 日期:{date.Date}";
        }
        /// <summary>
        /// 
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void date_DateChanged(object sender, DatePickerValueChangedEventArgs e)
        {
            info.Text = $"时间:{time.Time} 日期改变为:{date.Date}";
        }
```
4.13 枢轴控件(Pivot)
枢轴控件Pivot提供了一种快捷的方式来管理应用中的视图或页面,通过一种类似页签的方式来将视图分类,这样就可以在一个界面上通过切换页签来浏览多个数据集,或者切换应用视图.枢轴控件水平放置独立的视图,同时处理左侧和右侧的导航,在触摸屏上可以通过划动或者平移手势来切换枢轴控件中的视图,同时处理左侧和右侧的导航,在触摸屏上通过划动或平移手势来切换枢轴控件中的视图.语法如下
``` bash
<Pivot>
	<PivotItem>
	<!--UI内容-->
	</PivotItem>
	<PivotItem>
	<!--UI内容-->
	</PivotItem>
</Pivot>
```
Pivot分两部分 标题 内容
1.标题 通过Title设置 PivotItem Header 富文本信息
2.内容 SelectionChanged SelectedIndex SelectedItem 可以获取到当前选中的页签索引和对象
4.14 全景视图控件(Hub)
全景视图控件Hub是Win10中独特的视图控件,给用户提供一种纵向拉伸延长的效果,提供了独特显示控件,数据，服务的方式.
``` bash
<Hub>
<HubSection>
	<HubSection.ContentTemplate>
		<DateTemplate>
			<!--UI内容-->
		</DateTemplate>
	</HubSection.ContentTemplate>
</hubSection>
<HubSection Header="second item">
	<HubSection.ContentTemplate>
		<DataTemplate>
			<!--UI内容-->
		</DataTemplate>
	</HubSection.ContentTemplate>
</Hub>
```
Hub控件和Pivot控件是同一类型的控件,都是使用页签的方式来组织页面的内容,不过这两个控件的交互效果却不太一样
在使用Hub控件时通常会关注Hub控件的三个部分,背景,全景背景,全景区域.
背景是Hub控件包装下的整个应用程序的背景,位于全景应用的最底层,这个背景会铺满整个设备屏幕.
背景通常是一张全景图,他可能是应用程序最直观的部分,也可以不设置.全景标题与全景区域跟Pivot控件的作用类似.
``` bash
  <!--在大标题上添加程序的图标-->            
                    <Hub>
                        <Hub.Header>
                            <StackPanel Orientation="Horizontal">
                                <Image Source="Assets/StoreLogo.scale-100.png" Height="100"/>
                                <TextBlock Text="My App"/>
                            </StackPanel>
                        </Hub.Header>
                        <HubSection Header="first item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第一个Item" FontSize="50"/>
                                        <TextBlock Text="这是第一个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>

                        <HubSection Header="second item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第二个Item" FontSize="50"/>
                                        <TextBlock Text="这是第二个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>

                        <HubSection Header="Third item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第三个Item" FontSize="50"/>
                                        <TextBlock Text="这是第三个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>
                    </Hub>
```
4.15 浮出控件(Flyout)
浮出控件(Flyout)是一个轻型的辅助型的弹出控件,通常作为提示或者要求用户进行相关的交互来使用.
Flyout控件与Win10里面的弹出框MessageDialog有很大区别.Flyout控件是一个辅助控件,需要与其他控件组合起来才能使用.其次,取消的规则不一样,Flyout控件收集用户输入,显示与某个项目相关的更多信息或者要求用户确认某个操作,只有当为了相应用户点击时才应显示Flyout控件,也就是说Flyout控件并不是直接显示出来,而是需要用户操作才能呈现出来,当用户在弹出窗口外部点击时,Flyout控件就会消失,这也是Flyout控件默认的关闭规则.
``` bash
<Button>
	<Button.Flyout>
		<Flyout>
			<!--浮出的UI内容-->
		</Flyout>
	</Button.Flyout>
</Button>
```
非Button控件也一样可以使用Flyout控件,也可以使用FlyoutBase.AttachedFlyout附加属性,将Flyout控件附加到任何FrameworkElement对象.因为Flyout控件必须要响应相应某个用户的操作,Button控件默认关联到了Clcik事件,如果用FlyoutBase.AttachedFlyout附加属性来添加Flyout控件就必须相应FrameworkElement控件上的交互,并在代码中打开Flyout控件
``` bash
<TextBlock text="Tapped事件触发Flyout" Tapped="Textblock_Tapped">
<FlyoutBase.AttachedFlyout>
<Flyout>
<!--浮出的UI内容-->
</Flyout>
</FlyoutBase.AttachedFlyout>
</TextBlock>
private void TextBlock_Tapped(object sender,TappedRoutedEventArgs e){
	FrameworkElement element=sender as FrameworkElement;
	if(element!=null){
	FlyoutBase.ShowAttachedFlyout(element);
}
}
```
Flyout有两种创建方式,一种方式 Button Flyout 添加 2.通过FlyoutBase.AttachedFlyout附加属性给任何FrameworkElement对象来添加,在Win10上Flyout控件一共有6种不同的类型,Flyout,DatePickerFlyout,TimePickerFlyout,PickerFlyout,ListPickerFlyout和MenuFlyout.
1.Flyout
Flyout类型表示处理自定义浮出窗口,Flyout控件经常使用的事件是Closed,Opened和Opening,分别表示在关闭,已打开,正在打开,在实际种在Closed事件获取用户操作结果.这三种事件也是其他共性
2.DatePickerFlyout TimerPickerFlyout
3,PickerFlyout ListPickerFlyout
PickerFlyout表示选择的浮出窗口,可以在页面底下添加确认的菜单栏用于用户确认;
ListPickerFlyout表示列表形式展示的浮出窗口,需要通过集合数据绑定来呈现列表的选择.
与其他区别是提供了选中确认的时间,分别是PickerFlyout对应的Confirmed事件和ListPickerFlyout对应的ItemsPicked事件.ListPickerFlyout需要通过数据绑定来实现选择的列表
4.MenuFlyout
MenuFlyout表示上下文菜单的选择浮出窗口,一个MenuFlyout会包含若干个MenuFlyoutItem.
每个MenuFlyoutItem表示一个选项,用户可以单击,同时通过MenuFlyoutItem的Click事件来处理单击请求.
``` bash
 <PivotItem Header="Flyout">
                <StackPanel>
                    <Button Content="Show Flyout">
                        <Button.Flyout>
                            <Flyout>
                                <StackPanel>
                                    <TextBox PlaceholderText="Please Enter Name."/>
                                    <Button HorizontalAlignment="Right" Content="确定"/>
                                </StackPanel>
                            </Flyout>
                        </Button.Flyout>
                    </Button>
                    <!--浮出上下文菜单,点击菜单后改变当前按钮上的文本内容-->
                    <Button x:Name="menuFlyoutButton" Content="Show MenuFlyout">
                        <Button.Flyout>
                            <MenuFlyout>
                                <MenuFlyoutItem Text="Option 1" Click="MenuFlyoutItem_Click"/>
                                <MenuFlyoutItem Text="Option 2"
                                                Click="MenuFlyoutItem_Click"/>
                                <MenuFlyoutItem Text="Option 3" Click="MenuFlyoutItem_Click"/>
                            </MenuFlyout>
                        </Button.Flyout>
                    </Button>
                    
                    <!--浮出选择日期弹窗,点击确认后触发DatePicked事件,然后可以获取选中的日期-->
                    <Button Content="Show DatePicker">
                        <Button.Flyout>
                            <DatePickerFlyout DatePicked="DatePickerFlyout_DatePicked"/>
                        </Button.Flyout>
                    </Button>
                    <!--浮出选择时间弹窗,点击确认后触发DatePicked事件,然后可以获取选中的时间-->
                    <Button Content="Show TimePicker">
                        <Button.Flyout>
                            <TimePickerFlyout TimePicked="TimePickerFlyout_TimePicked"/>
                        </Button.Flyout>
                    </Button>
                    <!--浮出选择弹窗,显示底下的确认取消菜单栏并且处理其确认事件Confirmed-->
                    <Button Content="Show Picker">
                        <Button.Flyout>
                            <PickerFlyout Confirmed="PickerFlyout_Confirmed"
                                          ConfirmationButtonsVisible="True">
                                <TextBlock Text="你确定吗？？？？？" FontSize="30" Margin="0 100 0 0"/>
                            </PickerFlyout>
                        </Button.Flyout>
                    </Button>
<!--浮出选择列表弹窗,绑定集合的数据,处理选中的事件ItemsPicked-->
                    <Button Content="Show DataPicker">
                        <Button.Flyout>
                            <ListPickerFlyout x:Name="listPickerFlyout" Title="Select Mobile Tag：" ItemsPicked="listPickerFlyout_ItemsPicked">
                                <ListPickerFlyout.ItemTemplate>
                                    <DataTemplate>
                                        <TextBlock Text="{Binding}" FontSize="30"/>
                                        
                                    </DataTemplate>
                                </ListPickerFlyout.ItemTemplate>
                            </ListPickerFlyout>
                        </Button.Flyout>
                    </Button>
                    <!--使用附加属性FlyoutBase.AttachedFlyout来实现Flyout控件-->
                    <TextBlock Text="Please Click Me!" Tapped="TextBlock_Tapped" FontSize="20">
                        <FlyoutBase.AttachedFlyout>
                            <Flyout>
                                <TextBox Text="你好!">
                                    
                                    </TextBox>
                                </Flyout>
                            </FlyoutBase.AttachedFlyout>
                    </TextBlock>
                </StackPanel>
            </PivotItem>

 //Flyout
            //绑定ListPickerFlyout数据源
            listPickerFlyout.ItemsSource = new List<string> { "Apple", "Honor", "RedMi", "TSD","HTC" };
   private void MenuFlyoutItem_Click(object sender, RoutedEventArgs e)
        {
            menuFlyoutButton.Content = (sender as MenuFlyoutItem).Text;
        }
        //ListPickerFlyout的选中事件，点击列表的某一项便会触发,在事件处理程序中通常会获取选中的项目来进行相关逻辑的处理.
        private async void listPickerFlyout_ItemsPicked(ListPickerFlyout sender, ItemsPickedEventArgs args)
        {
            if (sender.SelectedItem!=null)
            {
                await new MessageDialog($"你选择的是:{sender.SelectedItem.ToString()}").ShowAsync();
            }
        }

        private async void DatePickerFlyout_DatePicked(DatePickerFlyout sender, DatePickedEventArgs args)
        {
            await new MessageDialog(args.NewDate.ToString()).ShowAsync();
        }
        // TimerPickerFlyout 时间选中事件,在事件处理程序里可以获取选中的时间
        private async void TimePickerFlyout_TimePicked(TimePickerFlyout sender, TimePickedEventArgs args)
        {
            await new MessageDialog(args.NewTime.ToString()).ShowAsync();
        }

        private async void PickerFlyout_Confirmed(PickerFlyout sender, PickerConfirmedEventArgs args)
        {
            await new MessageDialog("你点击了确定").ShowAsync();
        }
        // 通过Flyoutase.ShowAttachedFlyout方法来展示Flyout控件
        private void TextBlock_Tapped(object sender, TappedRoutedEventArgs e)
        {
            FrameworkElement element= sender as FrameworkElement;
            if (element!=null)
            {
                FlyoutBase.ShowAttachedFlyout(element);
            }
        }
```

4.16 ComboBox
对ComboBox控件下拉数据进行绑定,可以通过ItemTemplate设置选项的模板和绑定的属性,然后再绑定数据源.
常用事件:DropDownOpened(下拉框打开时触发的事件) DropDownClosed(下拉框关闭时触发的事件)
再选中选项后关闭菜单,获取选中选项.
``` bash
  <StackPanel>
                    <!--纯文本的下拉框-->
                    <ComboBox Header="Colors" PlaceholderText="Pick a Color">
                        <x:String>
                            Blue
                        </x:String>
                        <x:String>
                            Green
                        </x:String>
                        <x:String>
                            Red
                        </x:String>
                        <x:String>
                            Yellow
                        </x:String>
                    </ComboBox>
                    <!--数据绑定的下拉框-->
                    <ComboBox x:Name="comboBox2" DropDownClosed="comboBox2_DropDownClosed">
                        <ComboBox.ItemTemplate>
                            <DataTemplate>
                                <StackPanel Orientation="Horizontal">
                                    <TextBlock Text="{Binding Name}" FontSize="30"/>
                                    <TextBlock Text="{Binding Age}" Margin="50 10 0 0"/>
                                </StackPanel>
                            </DataTemplate>
                        </ComboBox.ItemTemplate>
                    </ComboBox>
                    
                   <!--数据绑定的下拉框关闭后,这里显示选中的选项的信息-->
                    <TextBlock x:Name="Info" FontSize="20"/>
                </StackPanel>

  private void comboBox2_DropDownClosed(object sender, object e)
        {
            if (comboBox2.SelectedItem!=null)
            {
                Man man = comboBox2.SelectedItem as Man;
                Info.Text = $"name:{man.Name} age:{man.Age}";
            }
        }
```
4.17 命令栏/菜单栏(CommandBar)
命令栏(CommandBar)也是程序中的菜单栏控件,win10中可以作为底部的菜单栏来使用.
Page页面的BottomAppBar属性表示页面的底部菜单栏,这个菜单栏必须是CommandBar控件类型,所以CommandBar控件通常都用在页面的底部菜单栏上,菜单栏有两种类型的按钮:一种是图形的图形按钮,另一种是文字的菜单按钮,这两种按钮都是使用AppBarButton控件来表示,只不过文字的菜单按钮需要放在CommandBar控件的SecondaryCommands属性上.
``` bash
<Page.BottomAppBar>
<CommandBar>
<AppBarButton/>

<CommandBar.SecondaryCommands>
<AppBarButton/>
</CommandBar.SecondaryCommands>
</CommandBar>
</Page.BottomAppBar>
```
对于CommandBar里面的应用栏按钮控件AppBarButton,通常需要设置其Label和Icon属性用于定义应用栏按钮的内容,除此之外,AppBarButton控件可以作为一种图形的图形按钮单独使用,Label属性属于设置按钮的文本标签,Icon属性用于设置按钮的图形,图形的图标可以使用以下4种类型:
1.FontIcon:图标基于来自指定字体系列的字型
2.BitmapIcon:图标基于带指定URI的位图图像文件
3.PathIcon:图标基于Path数据.
4.SymbolIcon:图标基于来自Segoe UI Symbol字体的字型预定义列表.
``` bash
<Page.BottomAppBar>
        <CommandBar Opened="CommandBar_Opened" Closed="CommandBar_Closed">
            <AppBarButton Label="buy" Icon="Shop"/>
            <!--BitmapIcon图标按钮-->
            <AppBarButton Label="BitmapIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <BitmapIcon UriSource="ms-appx:///Assets/questionmark.png"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--FontIcon图标按钮-->
            <AppBarButton Label="FontIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <FontIcon FontFamily="Candara" Glyph="&#x03a3;"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--PathIcon图标按钮-->
            <AppBarButton Label="PathIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <PathIcon Data="F1 M 20,20L 24,10L 24,24L 5,24"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--文本菜单按钮-->
            <CommandBar.SecondaryCommands>
                <AppBarButton Label="about" Click="AppBarButton_Click"/>
            </CommandBar.SecondaryCommands>
        </CommandBar>
    </Page.BottomAppBar>

 private void CommandBar_Opened(object sender, object e)
        {
            info.Text = "The CommmandBar is Opened";
        }

        private void CommandBar_Closed(object sender, object e)
        {
            info.Text = "The CommandBar is Closed";
        }

        private void AppBarButton_Click(object sender, RoutedEventArgs e)
        {
            info.Text = $"单击了菜单栏:{(sender as AppBarButton).Label}";
        }
```
4.18 分屏控件(SPlitView)
SplitView是Win10通用应用程序推荐的交互控件,通常和一个汉堡按钮搭配作为一种抽屉式菜单来进行呈现.
``` bash
<SplitView>
<SplitView.Pane>
...菜单面板的内容
</SplitView.Pane>
...主体内容
</SplitView>
```
菜单面板是通过Pane属性来进行赋值,并且通过IsPaneOpen属性来控制打开和关闭状态.true表示打开,false表示关闭,当菜单关闭时,页面将全部显示SplitView的主体内容,主题内容为Content属性
``` bash
 <PivotItem Header="SplitView">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <ToggleButton Click="ToggleButton_Click" VerticalAlignment="Top" Foreground="Green">
                        <ToggleButton.Content>
                            <Border Background="Transparent" Width="40" Height="40">
                                <FontIcon x:Name="hamburger" FontFamily="Segoe MDL2 Assets" Glyph="&#xE700;"/>
                                
                            </Border>
                        </ToggleButton.Content>
                    </ToggleButton>
                    <SplitView x:Name="Splitter" IsPaneOpen="True">
                        <SplitView.Pane>
                            <StackPanel VerticalAlignment="Center">
                                <Button Content="菜单1" Click="Button_Click_1"/>
                                <Button Content="菜单2" Click="Button_Click_1"/>
                                <Button Content="菜单3"
                                        Click="Button_Click_1"/>
                                <Button Content="菜单4" Click="Button_Click_1"/>
                            </StackPanel>
                        </SplitView.Pane>
                        <Grid>
                            <TextBlock x:Name="tb" Text="" VerticalAlignment="Center" HorizontalAlignment="Center"/>
                        </Grid>
                    </SplitView>
                </Grid>
            </PivotItem>
 private void ToggleButton_Click(object sender, RoutedEventArgs e)
        {
            Splitter.IsPaneOpen = (Splitter.IsPaneOpen == true) ? true : false;
        }

        private void Button_Click_1(object sender, RoutedEventArgs e)
        {
            Splitter.IsPaneOpen = false;
            tb.Text = $"你好,{(sender as Button).Content}";
        }

```
#### 布局控件
5.14 Canvas
``` bash
<PivotItem Header="渐变矩形">
                <Canvas Background="White">
                    <Canvas Height="400" Width="400" Canvas.Left="0" Canvas.Top="50" Background="Gray" Opacity="0.1"/>
                    <Canvas Height="360" Width="360" Canvas.Left="20"
                            Canvas.Top="70" Background="Gray" Opacity="0.2"/>
                    <Canvas Height="320" Width="320" Canvas.Left="40" Canvas.Top="90" Background="Gray" Opacity="0.3"/>
                    <Canvas Height="280" Width="280" Canvas.Left="60"
                            Canvas.Top="110" Background="Gray" Opacity="0.4"/>
                    <Canvas Height="240" Width="240" Canvas.Left="80"
                            Canvas.Top="130" Background="Gray" Opacity="0.5"/>

                        <Canvas Height="200" Width="200" Canvas.Left="100" Canvas.Top="150" Background="Gray" Opacity="0.6"/>
                        <Canvas Height="160" Width="160" Canvas.Left="120"
                            Canvas.Top="170" Background="Black" Opacity="0.3"/>
                        <Canvas Height="120" Width="120" Canvas.Left="140" Canvas.Top="190" Background="Black" Opacity="0.4"/>
                        <Canvas Height="80" Width="80" Canvas.Left="160"
                            Canvas.Top="210" Background="Black" Opacity="0.5"/>
                        <Canvas Height="40" Width="40" Canvas.Left="180"
                            Canvas.Top="230" Background="Black" Opacity="0.6"/>
                    </Canvas>
            </PivotItem>
```
5.15. 相对布局(RelativePanel)
控件的位置是按照相对位置来计算的,后一个控件在什么位置依赖于前一个控件的基本位置,是最灵活的一种布局.
RelativePanel也是通过附加属性来控制控件的布局,类似于Canvas的left.Top等.
Relative主要附加属性如下所示
Above:在某元素的上方
Belo:在某元素的下方
LeftOf:在某元素的左方
RightOf:在某元素的右方
AlignBottmWith:本元素的下边缘和某元素的下边缘对齐.
AlignLeftWith:本元素的左边缘和某元素的左边缘对齐
AlignRightWith:本元素的右边缘和某元素的右边缘对齐.
AlignTopWith:本元素的上边缘和某元素的上边缘对齐.
AlignVerticalCenterWith:本元素和某元素的垂直居中对齐.
5.16 多分辨率的适配布局
``` bash
 <PivotItem Header="RelativePanel">
                <RelativePanel VerticalAlignment="Center" HorizontalAlignment="Center">
                    <Button x:Name="bt1" Content="按钮1"/>
                    <Button x:Name="bt2" Content="按钮2" RelativePanel.RightOf="bt1" Height="100"/>
                    <Button x:Name="bt3" Content="按钮3... ..."
                            RelativePanel.AlignLeftWith="bt2"
                            RelativePanel.AlignBottomWithPanel="True"/>

                </RelativePanel>
            </PivotItem>
```
5.16.多分辨率的适配布局
1.对于要自适应布局部分不要硬编码 设置HorizontalAlignment VerticalAlignment 设置为strenth.通过MaxWidth,MinWidth来控制长度范围.
2.利用Grid面板或者RelativePanel面板动态布局.
规则的 Grid 反之
3.直接根据分辨率进行适配
真正的长和宽 WIndows.UI.Xaml.Window.Current.Bounds类的Height和Width属性来获取.
4.使用AdaptiveTrigger进行适配
通过设置MinWindowHeight,MinWindowWidth来设置触发的时机,当最小的窗口高度或者宽度超过限制将会触发布局属性的修改.AdaptiveManager需要在VisualStateManger里面使用.并且设置触发某个状态的改变.
5.23.布局系统的重要方法和属性
1.UIElement类
关于布局,重要属性 DesiredSize RenderSize 重要方法 Measure Arrange.
DesiredSize:只读 Size类 表示在布局过程的测量处理过程中计算的大小
RenderSize:这是一个只读属性,类型是Size类,表示UI元素最终呈现大小,RenderSize和DesiredSize并不一定相等.RenderSize就是其ArrangeOverride方法的返回值.
public void Measure(Size availableSize)方法:更新UIElement的DesiredSize属性,测量出UI元素的大小.[如果在该元素上实现了FrameworkElement.MeasureOverride(System.Windows.Size)方法,将会用此方法以形成递归布局更新.参数availableSize表示父对象可以为子对象分配可用空间.子对象可以请求大于可用空间的空间,如果特定面板中允许滚动或其他调整大小行为,则提供的大小可以适应此空间.
public void Arrange(Rect finalRect):Arrange方法所做的事情是定位子对象并确定UIElement的大小,也就是DesiredSize属性的值,如果在该元素上实现了FrameworkElement,ArrangeOverride(System.Windows.Size)方法,将会用此方法以形成递归布局更新,参数finalRect表示布局中父对象为子对象计算的最终大小,作为System.WIndows.Rect值提供.
2.FrameworkElement类
FrameworkElement MeasureOverride ArrangeOverride 
Protected virtual Size MeasureOverride(Size availableSize)
Protected virtual Size ArrangeOverride(Size finalSize)
5.24 测量和排列过程
1.测量的过程是为了确认DesiredSize的值,最终是要提供给排列的过程去使用.
2.DesiredSize是根据Margin,Width,Height等属性来决定.
3.排列的过程确定RenderSize,以及最终子对象被安置的空间.RenderSize就是ArrangeOverride的返回值,还没有被裁剪过的值.
4.Margin,Width,Height只是控件表面上的属性,而实际掌控这些效果的是布局的测量排列过程.
5.Margin,Width,Height等属性的改变会重新触发布局的过程.
5.3.自定义布局规则
``` bash
 public class TestPanel :Panel
    {
        public TestPanel()
        {
        }
        protected override Size MeasureOverride(Size availableSize)
        {
            Debug.WriteLine($"进入父对象:{this.Name}的MeasureOverride方法测量大小.");
            foreach (var item in this.Children)
            {
                item.Measure(new Size(120,120));
                Debug.WriteLine($"子对象的DesiredSize值Width:{item.DesiredSize.Width} Height:{item.DesiredSize.Height}");
                Debug.WriteLine($"子对象的RenderSize值Width:{item.RenderSize.Width} Height:{item.RenderSize.Height}");

            }
            Debug.WriteLine($"父对象的DesiredSize值Width:{this.DesiredSize.Width} Height:{this.DesiredSize.Height}");
            Debug.WriteLine($"父对象的RenderSize值Width:{this.RenderSize.Width} Height:{this.RenderSize.Height}");
            return availableSize;
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            Debug.WriteLine($"进入父对象:{this.Name}的ArrangeOverride方法进行排列");
            double x = 0;
            foreach (var item in this.Children)
            {
                //排列子元素
                item.Arrange(new Rect(x,0,item.DesiredSize.Width,item.DesiredSize.Height));
                x += item.DesiredSize.Width;
                Debug.WriteLine($"子对象的DesiredSize值Width:{item.DesiredSize.Width} Height:{item.DesiredSize.Height}");
                Debug.WriteLine($"子对象的RenderSize值Width:{item.RenderSize.Width} Height:{item.RenderSize.Height}");

            }
            Debug.WriteLine($"父对象的Desired值Width:{this.DesiredSize.Width} Height:{this.DesiredSize.Height}");
            Debug.WriteLine($"父对象的Render值Width:{this.RenderSize.Width} Height:{this.RenderSize.Height}");
            return base.ArrangeOverride(finalSize);
        }
    }

 public class TestUIElement : Panel
    {
        public TestUIElement()
        {

        }
        protected override Size MeasureOverride(Size availableSize)
        {
            Debug.WriteLine($"进入子对象:{this.Name}的MeasureOverride方法测量大小.");
            return availableSize;
        }
        protected override Size ArrangeOverride(Size finalSize)
        {
            Debug.WriteLine($"进入子对象:{this.Name}的ArrangeOverride方法进行排列");
            return finalSize;
        }

    }

 <PivotItem Header="MeasureArrange">
                <StackPanel>
                    <Button Content="改变高度" Click="Button_Click_2"/>
                    <controls:TestPanel x:Name="element1" Width="60" Height="60" Background="Red" Margin="10"/>
                    <controls:TestUIElement x:Name="element2" Width="60" Height="60" Background="Red"/>
                    
                </StackPanel>
            </PivotItem>
```
5.3.1.创建布局类
需要注意的是,自定义的半径属性发生改变的时候,需要调用InvalidateArrange方法重新触发布局的排列过程,否则修改半径后将不会起到任何作用.
``` bash
public class CirclePanel:Panel
    {
        //自定义的半径变量
        private double _radius = 0;

        public double Radius
        {
            get { return (double)GetValue(RadiusProperty); }
            set { SetValue(RadiusProperty, value); }
        }

        // Using a DependencyProperty as the backing store for Radius.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty RadiusProperty =
            DependencyProperty.Register("Radius", typeof(double), typeof(CirclePanel), new PropertyMetadata(0.0,OnRadiusPropertyChanged));

        public CirclePanel()
        {
        }

        private static void OnRadiusPropertyChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
        {
            //获取触发属性改变的CirclePanel对象.
            CirclePanel target = (CirclePanel)d;
            //获取传递进来的最新的值,并赋值给半径变量
            target._radius = (double)e.NewValue;
            //使排列状态失效,进行重新排列
            target.InvalidateArrange();
        }

        protected override Size MeasureOverride(Size availableSize)
        {
            //处理测量子对象的逻辑
            //最大的宽度的变量
            double maxElementWidth = 0;
            //遍历所有的子对象,并调用子对象的Measure方法进行测量,取出最大的宽度的子对象.
            foreach (var item in Children)
            {
                //测量子对象
                item.Measure(availableSize);
                maxElementWidth = Math.Max(item.DesiredSize.Width,maxElementWidth);
            }
            //取两个半径的大小和最大的宽度的两倍作为面板的宽度.
            double panelWidth = 2 * this.Radius + 2 * maxElementWidth;
            //取面板所分配的高度,宽度和计算出来的宽度的最小值作为面板的实际大小.
            double width = Math.Min(panelWidth,availableSize.Width);
            double height = Math.Min(panelWidth,availableSize.Height);
            return new Size(width,height); 
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            //当前的角度,从0开始排列
            double degree = 0;
            //计算每个子对象所占用的角度大小.
            double degreeStep = (double)360 / this.Children.Count;
            //计算
            double mX = this.DesiredSize.Width / 2;
            double mY = this.DesiredSize.Height / 2;
            //遍历所有子对象进行排列
            foreach (var item in Children)
            {
                //把角度转换为弧度单位
                double angle = Math.PI * degree / 180.0;
                //根据弧度计算出圆弧上的x,y的坐标值
                double x = Math.Cos(angle) * this._radius;
                double y = Math.Sin(angle) * this._radius;
                //使用变换效果让控件旋转角度degree
                RotateTransform rotateTransform = new RotateTransform();
                rotateTransform.Angle = degree;
                rotateTransform.CenterX = 0;
                rotateTransform.CenterY = 0;
                item.RenderTransform = rotateTransform;
                //排列子对象
                item.Arrange(new Rect(mX+x,mY+y,item.DesiredSize.Width,item.DesiredSize.Height));
                //角度递增
                degree += degreeStep;
            }
            return finalSize;
        }
        
        
    }

<PivotItem Header="CustomPanel">
                <Grid x:Name="ContentPanel1" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <Slider Grid.Row="0" Value="5" ValueChanged="Slider_ValueChanged"/>
                    <controls:CirclePanel x:Name="circlePanel" Radius="50" Grid.Row="1" HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock>Start here</TextBlock>
                        <TextBlock>TextBlock 2</TextBlock>
                        <TextBlock>TextBlock 3</TextBlock>
                        <TextBlock>TextBlock 4</TextBlock>
                        <TextBlock>TextBlock 5</TextBlock>
                        <TextBlock>TextBlock 6</TextBlock>
                        <TextBlock>TextBlock 7</TextBlock>
                    </controls:CirclePanel>
                </Grid>
            </PivotItem>

 private void Slider_ValueChanged(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (circlePanel!=null)
            {
                circlePanel.Radius = e.NewValue * 10;
            }
        }
```
