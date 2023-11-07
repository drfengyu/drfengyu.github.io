---
title: WPF-页面和导航
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
abbrlink: 14e8ff67
date: 2022-11-24 14:04:38
pic:
---

### 导航应用程序演绎
1.将导航的内容寄宿于窗口中
2.Xaml浏览器应用程序 扩展名.xbap 可以直接在浏览器中运行.
### 4个核心
1.页面:WPF将导航内容封装为多个页面.
2.超链接
3.NavigationServices
4.Journal 每次连接通过Jourmal记录作为历史记录.
### Page的宿主窗口
Page的宿主窗口包括浏览器,导航窗口和Frame.
NavigationWindow是一个顶层窗口,不允许嵌入到其他控件中,而Frame则为轻量级,可以嵌入到其他控件,如NavigationWindow或者Page,甚至其他Frame中.Frame默认没有导航工具栏,可以设置其NavigationUIVisiblity属性为Visible使其工具栏是否可见.
### 导航链接
#### 超链接
``` bash
<HyperLink NavigateUri="Page2.xaml">开始阅读路由事件
</HyperLink>
```
段落导航 NavigateUri的设置方法是"页面名#元素名"
HyperLink Click NavigationService Navigate方法导航.
``` bash
private Hyperlink_Click(object sender,RoutedEventArgs e){
	NavigationService.Navigate(newUri("pack://application:,,,/Page2.xaml"));
}
```
### 其他方式导航
NavigationCommands.BrowseBack NavigationCommands.BrowseForward
``` bash
<Page x:Class="WpfApp1.Pages.LoginPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
      xmlns:local="clr-namespace:WpfApp1.Pages"

      Title="登陆页面" ShowsNavigationUI="True" WindowTitle="登录页面" Loaded="Page_Loaded"  PreviewLostKeyboardFocus="Page_PreviewLostKeyboardFocus">

    <Border BorderBrush="Black" BorderThickness="2" Margin="10">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition Height="auto"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="4*"/>
                <ColumnDefinition Width="6*"/>
            </Grid.ColumnDefinitions>
            <TextBlock Text="用户名" Margin="5"/>
            <TextBox Style="{x:Null}" x:Name="name" Grid.Column="1" Margin="5"/>
            <TextBlock Grid.Row="1" Text="密码" Margin="5"/>
            <PasswordBox x:Name="password" Grid.Row="1" Grid.Column="1" Margin="5"/>
            <Button x:Name="btn" Grid.Row="2" Height="25" Width="80" HorizontalAlignment="Left" Margin="5 0 0 0"
                    Click="btn_Click" Content="登录"/>
            <TextBlock Grid.Row="2" Grid.Column="1" VerticalAlignment="Center" HorizontalAlignment="Center">
            <Hyperlink NavigateUri="RegisterPage.xaml">
                忘记密码了... ...    
            </Hyperlink>
            </TextBlock>
            <TextBlock Margin="0" Grid.Row="3" Grid.ColumnSpan="2" x:Name="hyperlinktext"
                       HorizontalAlignment="Center" VerticalAlignment="Center">
                如果没有注册,请单击
                <Hyperlink Click="Hyperlink_Click">
                    注册
                    </Hyperlink>
                页面
                <LineBreak/>
            </TextBlock>
        </Grid>
    </Border>
</Page>

    public partial class LoginPage : Page
    {
        public LoginPage()
        {
            InitializeComponent();
        }


        public string FocusElement
        {
            get { return (string)GetValue(FocusElementProperty); }
            set { SetValue(FocusElementProperty, value); }
        }

        // Using a DependencyProperty as the backing store for FocusElement.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty FocusElementProperty =
            DependencyProperty.Register("FocusElement", typeof(string), typeof(LoginPage), new FrameworkPropertyMetadata(null,FrameworkPropertyMetadataOptions.Journal));


        private void btn_Click(object sender, RoutedEventArgs e)
        {
            List<User> users =((App) App.Current).users;
            int usercount = users.Count;
            User user = new User(name.Text,password.ToString());
            for (int i = 0; i < usercount; i++)
            {
                if (name.Text == users[i].Name && password.Password == users[i].Password) {
                    WelcomePage page = new WelcomePage(user,false);
                    NavigationService.Navigate(page);
                    return;
                }
            }
            NavigationService.Navigate(new Uri("pack://application:,,,/Pages/ErrorPage.xaml"));
        }

        private void Page_PreviewLostKeyboardFocus(object sender, KeyboardFocusChangedEventArgs e)
        {
            if (e.NewFocus==this.name || e.NewFocus==this.password)
            {
                this.FocusElement=(string)(((DependencyObject)e.NewFocus).GetValue(FrameworkElement.NameProperty));

            }
        }

        private void Page_Loaded(object sender, RoutedEventArgs e)
        {
            if (this.FocusElement != null) {
                IInputElement element = (IInputElement)LogicalTreeHelper.FindLogicalNode(this,this.FocusElement);
                Keyboard.Focus(element);
            }
            
        }

        private void Hyperlink_Click(object sender, RoutedEventArgs e)
        {
            RegisterPage registerPage = new RegisterPage();
            registerPage.Return += RegisterPage_Return;
            this.NavigationService.Navigate(registerPage);
        }

        private void RegisterPage_Return(object sender, ReturnEventArgs<User> e)
        {
            if (e == null) {
                return;
            }
            User user = e.Result;
                if (user != null) {
                this.name.Text = user.Name;
                this.password.Password = password.Password;
            }
            List<User> list = ((App)(App.Current)).users;
            list.Add(user);
        }
    }

<PageFunction x:Class="WpfApp1.Pages.RegisterPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
      xmlns:local="clr-namespace:WpfApp1.Pages" xmlns:bookmodels="clr-namespace:BookModels;assembly=BookModels"
              mc:Ignorable="d" 
      d:DesignHeight="450" d:DesignWidth="800"
      Title="RegisterPage"
              x:TypeArguments="bookmodels:User">

    <Border BorderBrush="Black" BorderThickness="2" >
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>

                <RowDefinition/>
                <RowDefinition Height="auto"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="4*"/>
                <ColumnDefinition Width="7*"/>
            </Grid.ColumnDefinitions>
            <TextBlock Text="用户名" Margin="5"/>
            <TextBox x:Name="name" Grid.Column="1" Margin="5"/>
            <TextBlock Grid.Row="1" Text="密码" Margin="5"/>
            <PasswordBox x:Name="password" Grid.Row="1" Grid.Column="1" Margin="5"/>
            <TextBlock Grid.Row="2" Text="再次输入密码" Margin="5"/>
            <PasswordBox x:Name="Secondpassword" Grid.Row="2" Grid.Column="1" Margin="5"/>
            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Right" VerticalAlignment="Center">
                <Button Content="注册" Margin="30 0 0 0" Click="Button_Click"/>
                <Button Content="取消" Margin="30 0 30 0" Command="Close"/>
            </StackPanel>

        </Grid>
    </Border>
</PageFunction>

    public partial class RegisterPage : PageFunction<User>
    {
        public RegisterPage()
        {
            InitializeComponent();
            user = new User();
        }

        private User user;

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            User user = CreateUser();
            if (user == null) return;
            else
                OnReturn(new ReturnEventArgs<User>(user));
        }

        private User CreateUser()
        {
            if (string.IsNullOrEmpty(name.Text) || string.IsNullOrEmpty(password.Password)||
                string.IsNullOrEmpty(Secondpassword.Password) || password.Password!=Secondpassword.Password)
            {
                BiaoZhuWindow biaoZhu=new BiaoZhuWindow();
                biaoZhu.mes.Content = "用户名或密码有误!";
                biaoZhu.Show();
                return null;
            }
            user = new User() { Name=name.Text,Password=Secondpassword.Password};
            return user;
        }
    }

```