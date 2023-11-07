---
title: WPF-Command命令
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
abbrlink: d0627144
date: 2022-11-24 11:18:21
pic:
---

### Command
``` bash
<Window.CommandBindings>
	<CommandBinding Command="Save" Executed="SaveExecuted" CanExecute="SaveCanExecute"/>
</Window.CommandBindings>
```
CommandBinding有两个重要事件,执行关联的Command时,会触发该Executed事件的处理函数,触发CanExecute事件时,相应的事件处理函数需要将传递来的参数e的CanExecute属性设为true或false.告知WPF命令系统该命令是否可用.
### Command模型
WPFCommand模型包含如下4部分:
1.Command:应用程序需要执行的任务.
2.CommandBinding:连接Comamnd和特定应用程序逻辑,如前面CommandBinding连接Save命令及其处理函数.
3.Command Source:触发Command的对象,如前述的菜单栏和工具栏,单击可以触发绑定的Command.
4.Command target:Command应用再上面的对象.如前述的TextBox.
### Command
1.ICommand接口
一个Command代表应用程序的任务.在WPF中所有Command都要实现ICommand接口,该接口有两个方法(Executed CanExecute)和一个事件(CanExecuteChanged)
1.Execute方法:当Command被触发时调用该方法,执行与命令相对应的操作.
2.CanExecute方法:用来判断该命令是否可应用到当前Command target上,如果该方法返回true可以
3.CanExecutedChanged事件:Command有执行或者不执行两种状态,状态改变时触发该事件.一般监听该事件的是CommandSource,它监听到该事件后会调用绑定的Command的CanExecute方法检查当前Command的状态,然后决定然后决定CommandSource是启用还是禁用.
Recommand的InputGuestures属性用来指定鼠标和键盘动作,从而可以关联命令和相应的鼠标键盘动作.
WPF内置的Command库
ApplicationCommands NavigationCommands EditingCommands ComponentCommands MeidaCommands
### Command Sources
CommandSource是能触发Command的对象按钮和菜单,包括键盘和鼠标操作都可以是Command Source.
CommandSource必须实现ICommandSource接口,该接口定义了3个属性
1.Command:Command Source触发的Command.
2.CommandParameter:Command执行时需要的参数.大多数情况下命令的执行不需要参数.
3.CommandTarget:Command应用对象.
WPF中实现ICommandSource接口的有ButtonBase,MenuItem,Hyperlink和InputBinding.
### Commanding
CommandBinding关联Command和实现Command的事件处理函数
CommandBinding类包括Command属性,以及PreviewExecuted,Executed,PreviewCanExecute和CanExecute事件.如果一个元素派生自UIElement,ContentElement和UIElement3D之一,那么CommandBinding对象可以添加到该元素中.
``` bash
<Window.CommandBindings>
<CommandBinding Command="Close" Executed="CloseCommand"/>
<ComandBinding Command="Save" Executed="SaveCommand"/>
</Window.CommandBindings>

CommandBinding SaveCommandBinding=new CommandBinding(ApplicationCommands.Save,SaveExecuted,SaveCanExecute);
this.CommandBindings.Add(SaveCommandBinding);

```
### Command Target
Command Target是Command会应用在上面的对象.ComandSource 需要实现ICommandSource接口,其中就有一个属性是CommandTarget.当CommandSource不明确指定Command Target属性时,会把当前获得的焦点的元素认为是Comand Target.
## 非规则窗口
``` bash
<Window x:Class="WpfApp1.BiaoZhuWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="clr-namespace:WpfApp1.Pages"
        Title="BiaoZhuWindow"
        Background="Transparent" AllowsTransparency="True" WindowStyle="None"
        SizeToContent="WidthAndHeight" MouseLeftButtonDown="Window_MouseLeftButtonDown">
    <Window.CommandBindings>
        <CommandBinding Command="Close" Executed="Close"/>
    </Window.CommandBindings>
    <!--窗口的形状都绘制在Canvas面板中面-->
        <Canvas Width="200" Height="200">
            <!--窗口的轮廓-->
        <Path Stroke="DarkGray" StrokeThickness="2">
            <Path.Fill>
                <LinearGradientBrush StartPoint="0.2,0" EndPoint="0.8,1">
                    <GradientStop Color="White" Offset="0"/>
                    <GradientStop Color="White" Offset="0.45"/>
                    <GradientStop Color="LightBlue" Offset="0.9"/>
                    <GradientStop Color="Gray" Offset="1"/>
                </LinearGradientBrush>
            </Path.Fill>
            <Path.Data>
                <PathGeometry>
                    <PathFigure StartPoint=" 40,20" IsClosed="True">
                        <LineSegment Point="160,20"/>
                        <ArcSegment Point="180,40" Size="20,20" SweepDirection="Clockwise"/>
                        <LineSegment Point="180,80"/>
                        <ArcSegment Point="160,100" Size="20,20" SweepDirection="Clockwise"/>
                        <LineSegment Point="90,100"/>
                        <LineSegment Point="60,150"/>
                        <LineSegment Point="60,100"/>
                        <LineSegment Point="40,100"/>
                        <ArcSegment Point="20,80" Size="20,20" SweepDirection="Clockwise"/>
                        <LineSegment Point="20,40"/>
                        <ArcSegment Point="40,20" Size="20,20" SweepDirection="Clockwise"/>
                        
                    </PathFigure>
                </PathGeometry>
            </Path.Data>
        </Path>
            <!--拖拽我的标签-->
        <Label  FontSize="15" HorizontalContentAlignment="Center"
               VerticalContentAlignment="Center" Width="200" Height="120" x:Name="mes" Content="早上好!"/>
        <!--关闭按钮-->
        <Button Command="Close" Canvas.Left="155" Canvas.Top="30">
            <Button.Template>
                <ControlTemplate>
                    <Canvas>
                        <Rectangle Width="15" Height="15" Stroke="Black"
                                   RadiusX="3" RadiusY="3">
                            <Rectangle.Fill>
                                <SolidColorBrush x:Name="myAnimateBrush" Color="Red"/>
                            </Rectangle.Fill>
                            
                            
                        </Rectangle>
                        <Line X1="3" Y1="3" X2="12" Y2="12" Stroke="White" StrokeThickness="2"/>
                        <Line X1="12" Y1="3" X2="3" Y2="12" Stroke="White" StrokeThickness="2"/>
                    </Canvas>
                </ControlTemplate>
            </Button.Template>
        </Button>
    </Canvas>
</Window>

```
