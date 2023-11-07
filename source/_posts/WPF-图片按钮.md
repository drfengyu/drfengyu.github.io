---
title: WPF-图片按钮
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
abbrlink: f76c692
date: 2022-11-25 13:10:00
pic:
---
``` bash
    public class ImageButton:Button
    {
        static ImageButton() { 
            DefaultStyleKeyProperty.OverrideMetadata(typeof(ImageButton), new FrameworkPropertyMetadata(typeof(ImageButton)));
        }
        ///ImageSize NormalImage HowerImage PressedImage BorderVisiblity
        ///


        public double ImageSize
        {
            get { return (double)GetValue(ImageSizeProperty); }
            set { SetValue(ImageSizeProperty, value); }
        }

        // Using a DependencyProperty as the backing store for ImageSize.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty ImageSizeProperty =
            DependencyProperty.Register("ImageSize", typeof(double), 
                typeof(ImageButton), new FrameworkPropertyMetadata(30.0,FrameworkPropertyMetadataOptions.AffectsRender));



        public string NormalImage
        {
            get { return (string)GetValue(NormalImageProperty); }
            set { SetValue(NormalImageProperty, value); }
        }

        // Using a DependencyProperty as the backing store for NormalImage.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty NormalImageProperty =
            DependencyProperty.Register("NormalImage", typeof(string), typeof(ImageButton), 
                new FrameworkPropertyMetadata("",FrameworkPropertyMetadataOptions.AffectsRender,ImageSourceChanged));


        public string HoverImage
        {
            get { return (string)GetValue(HoverImageProperty); }
            set { SetValue(HoverImageProperty, value); }
        }

        public static readonly DependencyProperty HoverImageProperty =
            DependencyProperty.Register("HoverImage", typeof(string), typeof(ImageButton),
            new FrameworkPropertyMetadata("", FrameworkPropertyMetadataOptions.AffectsRender, ImageSourceChanged));



        public string PressedImage
        {
            get { return (string)GetValue(PressedImageProperty); }
            set { SetValue(PressedImageProperty, value); }
        }

        // Using a DependencyProperty as the backing store for PressedImage.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PressedImageProperty =
            DependencyProperty.Register("PressedImage", typeof(string), typeof(ImageButton), new FrameworkPropertyMetadata("", FrameworkPropertyMetadataOptions.AffectsRender, ImageSourceChanged));



        public string DisabledImage
        {
            get { return (string)GetValue(DisabledImageProperty); }
            set { SetValue(DisabledImageProperty, value); }
        }

        // Using a DependencyProperty as the backing store for MyProperty.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty DisabledImageProperty =
            DependencyProperty.Register("DisabledImage", typeof(string), typeof(ImageButton), new FrameworkPropertyMetadata("",FrameworkPropertyMetadataOptions.AffectsRender,ImageSourceChanged));



        public Visibility BorderVisibility
        {
            get { return (Visibility)GetValue(BorderVisibilityProperty); }
            set { SetValue(BorderVisibilityProperty, value); }
        }

        // Using a DependencyProperty as the backing store for BorderVisibility.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty BorderVisibilityProperty =
            DependencyProperty.Register("BorderVisibility", typeof(Visibility), typeof(ImageButton), new FrameworkPropertyMetadata(Visibility.Hidden,FrameworkPropertyMetadataOptions.AffectsRender));


        private static void ImageSourceChanged(DependencyObject Object, DependencyPropertyChangedEventArgs e)
        {
            Application.GetResourceStream(new Uri("pack://application:,,,"+(string)e.NewValue));
        }
    }

```
### 资源样式
``` bash
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:Controls="clr-namespace:ImageButton.Controls">

    
    <ControlTemplate x:Key="ImageButtonTemplate1" TargetType="{x:Type Controls:ImageButton}">
        <Grid x:Name="grid">
            <Border x:Name="bg"
                    Background="{TemplateBinding Background}"
                    BorderBrush="{TemplateBinding BorderBrush}"
                    BorderThickness="{TemplateBinding BorderThickness}"
                    CornerRadius="3"
                    Visibility="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=BorderVisibility}">
                <StackPanel Orientation="Horizontal" VerticalAlignment="{TemplateBinding VerticalContentAlignment}"  HorizontalAlignment="{TemplateBinding HorizontalContentAlignment}">
                    <Image x:Name="ButtonImage" Source="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=NormalImage}"
                           Height="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=ImageSize}"
                           Width="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=ImageSize}" ToolTip="{TemplateBinding ToolTip}"/>
                    <ContentPresenter HorizontalAlignment="{TemplateBinding HorizontalContentAlignment}" VerticalAlignment="{TemplateBinding VerticalContentAlignment}" RecognizesAccessKey="True"/>
                </StackPanel>
            </Border>
        </Grid>
        <ControlTemplate.Triggers>
            <Trigger Property="IsMouseOver" Value="true">
                <Setter TargetName="ButtonImage" Property="Source" Value="{Binding HoverImage,RelativeSource={RelativeSource Mode=TemplatedParent}}"/>
            </Trigger>
            <Trigger Property="IsPressed" Value="true">
                <Setter TargetName="ButtonImage" Property="Source" Value="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=PressedImage}"/>
            </Trigger>
            <Trigger Property="IsEnabled" Value="false">
                <Setter TargetName="ButtonImage" Property="Source" Value="{Binding RelativeSource={RelativeSource Mode=TemplatedParent},Path=DisabledImage}"/>
            </Trigger>
        </ControlTemplate.Triggers>
    </ControlTemplate>
    <Style TargetType="Controls:ImageButton" BasedOn="{x:Null}">
        <Setter Property="Padding" Value="3"/>
        <Setter Property="Margin" Value="5"/>
        <Setter Property="ImageSize" Value="32"/>
        <Setter Property="BorderThickness" Value="3"/>
        <Setter Property="Foreground" Value="{DynamicResource TextBrush}"/>
        <Setter Property="Background" Value="{DynamicResource ButtonBorderBrush}"/>
        <Setter Property="BorderBrush" Value="{DynamicResource ButtonBorderBrush}"/>
        <Setter Property="VerticalContentAlignment" Value="Center"/>
        <Setter Property="HorizontalContentAlignment" Value="Center"/>
        <Setter Property="Template" Value="{StaticResource ImageButtonTemplate1}"/>
    </Style>
    <Style x:Key="ButtonStyleOne" TargetType="Controls:ImageButton" BasedOn="{StaticResource {x:Type Controls:ImageButton}}">
        <Setter Property="NormalImage" Value="/ImageButton;component/Resources/Normal.png"/>
        <Setter Property="HoverImage" Value="/ImageButton;component/Resources/Happy.png"/>
        <Setter Property="PressedImage" Value="/ImageButton;component/Resources/Tounge.png"/>
        <Setter Property="DisabledImage" Value="/ImageButton;component/Resources/Sad.png"/>
        <Setter Property="ToolTip" Value="Button Two"/>
    </Style>
    <Style x:Key="ButtonStyleTwo" TargetType="Controls:ImageButton" BasedOn="{StaticResource ButtonStyleOne}">
        <Setter Property="NormalImage" Value="/ImageButton;component/Resources/Happy.png"/>
        <Setter Property="HoverImage" Value="/ImageButton;component/Resources/Tounge.png"/>
        <Setter Property="PressedImage" Value="/ImageButton;component/Resources/Surprize.png"/>
        <Setter Property="ToolTip" Value="Button Two"/>
    </Style>
    <Style x:Key="ButtonStyleThree" TargetType="Controls:ImageButton"
           BasedOn="{StaticResource ButtonStyleTwo}">
        <Setter Property="NormalImage" Value="/ImageButton;component/Resources/Normal.png"/>
        <Setter Property="ToolTip" Value="Button Three"/>
        <Setter Property="BorderVisibility" Value="Visible"/>
    </Style>
</ResourceDictionary>

<ResourceDictionary x:Class="Consilium.Mobile.Tablet.Theme.ThemeDictionary"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <Color x:Key="WindowsBackgroundColor1">#FF000000</Color>
    <Color x:Key="WindowsBackgroundColor2">#FF1A1A1A</Color>

    <LinearGradientBrush x:Key="WindowBackground" EndPoint="0,1" StartPoint="0,0">
      <GradientStop Color="{StaticResource WindowsBackgroundColor1}" Offset=".5"/>
      <GradientStop Color="{StaticResource WindowsBackgroundColor2}" Offset="1"/>
    </LinearGradientBrush>

    <SolidColorBrush x:Key="TextBrush" Color="#FFFFFFFF" />
    <SolidColorBrush x:Key="ButtonBackgroundBrush" Color="#FF999999" />
    <SolidColorBrush x:Key="ButtonBorderBrush" Color="#FF333333" />            
</ResourceDictionary>
<Application x:Class="ImageButton.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:ImageButton"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="/ImageButton;component/Themes/ImageButton.xaml"/>
                <ResourceDictionary Source="/ImageButton;component/Themes/Resources.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>

```
### Xaml
``` bash
<Window x:Class="ImageButton.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:ImageButton" xmlns:controls="clr-namespace:ImageButton.Controls"
        mc:Ignorable="d"
        Title="MainWindow" Background="{DynamicResource WindowBackground}" ResizeMode="NoResize">
    <ScrollViewer Width="500" Height="500">
        <StackPanel>
            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}" BorderVisibility="Visible"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}"
                                  BorderVisibility="Visible" IsEnabled="False"/>

            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}" Content="Test Button"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}" BorderVisibility="Visible" Content="Test Button"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleOne}"
                                  BorderVisibility="Visible" IsEnabled="False" Content="Test Button"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleTwo}"/>
            <controls:ImageButton Style="{DynamicResource ButtonStyleThree}"/>
        </StackPanel>
    </ScrollViewer>
</Window>

```
### 自定义控件
``` bash
using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Media.Animation;
using System.Windows.Shapes;

namespace Tempeture
{
    public class AddBfh : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            float f = (float)value;
            return string.Format("{0}℃",f);
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
    public class Temp:Button
    {
        static Temp() {
            DefaultStyleKeyProperty.OverrideMetadata(typeof(Temp),new FrameworkPropertyMetadata(typeof(Temp)));
        }

        #region 基本控件
        TextBlock txtMaxValue = null;
        TextBlock txtMinValue = null;
        TextBlock txtCurrentValue = null;
        TextBlock txtPostion = null;
        Rectangle rect = null;
        #endregion
        #region 设置绑定
        public override void OnApplyTemplate()
        {
            base.OnApplyTemplate();
            AddBfh addBfh = new AddBfh();
            txtMaxValue = GetTemplateChild("MaxValue") as TextBlock;
            txtMinValue = GetTemplateChild("MinValue") as TextBlock;
            txtCurrentValue=GetTemplateChild("CurrentValue") as TextBlock;
            txtPostion=GetTemplateChild("Positions") as TextBlock;
            rect = GetTemplateChild("RectValue") as Rectangle;

            txtMaxValue.SetBinding(TextBlock.TextProperty,new Binding("MaxValue") { Source=this,Converter=addBfh});
            txtMinValue.SetBinding(TextBlock.TextProperty, new Binding("MinValue") { Source=this,Converter=addBfh});

            txtCurrentValue.SetBinding(TextBlock.TextProperty,new Binding("CurrentValue") { Source=this,Converter=addBfh});
            txtPostion.SetBinding(TextBlock.TextProperty, new Binding("MeterPosition") { Source=this});
            rect.SetBinding(Rectangle.HeightProperty, new Binding("CurrentValue") { Source = this }) ;
        }

        #endregion
        #region 最小值


        public float MinValue
        {
            get { return (float)GetValue(MinValueProperty); }
            set { SetValue(MinValueProperty, value); }
        }

        // Using a DependencyProperty as the backing store for MinValue.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty MinValueProperty =
            DependencyProperty.Register("MinValue", typeof(float), typeof(Temp), new PropertyMetadata(0.0f));


        #endregion
        #region 最大值


        public float MaxValue
        {
            get { return (float)GetValue(MaxValueProperty); }
            set { SetValue(MaxValueProperty, value); }
        }

        // Using a DependencyProperty as the backing store for MaxValue.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty MaxValueProperty =
            DependencyProperty.Register("MaxValue", typeof(float), typeof(Temp), new PropertyMetadata(100.0f));


        #endregion
        #region 当前值


        public float CurrentValue
        {
            get { return (float)GetValue(CurrentValueProperty); }
            set { SetValue(CurrentValueProperty, value); }
        }

        // Using a DependencyProperty as the backing store for CurrentValue.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty CurrentValueProperty =
            DependencyProperty.Register("CurrentValue", typeof(float), typeof(Temp), new PropertyMetadata(50f,CurrentValueChange));

        private static void CurrentValueChange(DependencyObject d, DependencyPropertyChangedEventArgs e)
        {
            (d as Temp).StoryboardPlay(e);
        }

        private void StoryboardPlay(DependencyPropertyChangedEventArgs e)
        {
            Storyboard sb = new Storyboard();
            DoubleAnimation da = new DoubleAnimation();
            da.To = double.Parse(e.NewValue.ToString());
            da.Duration = new Duration(TimeSpan.Parse("0:0:1"));
            rect.BeginAnimation(Rectangle.HeightProperty,da);
        }


        #endregion
        #region 位置信息


        public string MeterPosition
        {
            get { return (string)GetValue(MeterPositionProperty); }
            set { SetValue(MeterPositionProperty, value); }
        }

        // Using a DependencyProperty as the backing store for MeterPosition.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty MeterPositionProperty =
            DependencyProperty.Register("MeterPosition", typeof(string), typeof(Temp), new PropertyMetadata("Bash"));


        #endregion
    }
}
```
### 资源样式
``` bash
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="clr-namespace:Tempeture">
    <Style TargetType="{x:Type local:Temp}">
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type local:Temp}">
                    <Canvas xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Name="Tempreature" Width="250" Height="280" Clip="F1 M 0,0L 250,0L 250,280L 0,280L 0,0">
                        <Canvas x:Name="background" Width="250" Height="280" Canvas.Left="0" Canvas.Top="0">
                            <Path x:Name="Path" Width="230" Height="230" Canvas.Left="10" Canvas.Top="4.99991" Stretch="Fill" Data="F1 M 20,4.99991L 230,4.99991C 235.523,4.99991 240,9.47702 240,14.9999L 240,225C 240,230.523 235.523,235 230,235L 20,235C 14.4772,235 10,230.523 10,225L 10,14.9999C 10,9.47702 14.4772,4.99991 20,4.99991 Z ">
                                <Path.Fill>
                                    <LinearGradientBrush StartPoint="0.5,1.25168" EndPoint="0.5,-0.25168">
                                        <LinearGradientBrush.GradientStops>
                                            <GradientStop Color="#FF000000" Offset="0"/>
                                            <GradientStop Color="#FF4C4E4C" Offset="0.748837"/>
                                            <GradientStop Color="#FF989D98" Offset="1"/>
                                        </LinearGradientBrush.GradientStops>
                                    </LinearGradientBrush>
                                </Path.Fill>
                                <Path.Effect>
                                    <DropShadowEffect BlurRadius="8" ShadowDepth="3.77953" Opacity="0.599998" Color="#FF000000" Direction="315"/>
                                </Path.Effect>
                            </Path>
                            <Viewbox x:Name="Group" Width="58" Height="191" Canvas.Left="15" Canvas.Top="29">
                                <Canvas Width="58" Height="191">
                                    <Path x:Name="Path_0" Width="20" Height="147" Canvas.Left="18.875" Canvas.Top="1.726" Stretch="Fill" StrokeThickness="6" StrokeStartLineCap="Round" StrokeEndLineCap="Round" StrokeLineJoin="Round" Stroke="#FF96C240" Fill="#0096C240" Data="F1 M 25.875,4.72554L 25.875,4.72554C 29.741,4.72554 32.875,7.85956 32.875,11.7255L 32.875,138.725C 32.875,142.591 29.741,145.725 25.875,145.725L 25.875,145.725C 22.009,145.725 18.875,142.591 18.875,138.725L 18.875,11.7255C 18.875,7.85956 22.009,4.72554 25.875,4.72554 Z ">
                                        <Path.Effect>
                                            <DropShadowEffect BlurRadius="8" ShadowDepth="3.77953" Opacity="0.599998" Color="#FF000000" Direction="315"/>
                                        </Path.Effect>
                                    </Path>
                                    <Ellipse x:Name="Ellipse" Width="49.647" Height="46.889" Canvas.Left="4.052" Canvas.Top="136.683" Stretch="Fill" Fill="#FF96C240">
                                        <Ellipse.Effect>
                                            <DropShadowEffect BlurRadius="8" ShadowDepth="3.77953" Opacity="0.599998" Color="#FF000000" Direction="315"/>
                                        </Ellipse.Effect>
                                    </Ellipse>
                                    <Rectangle x:Name="RectValue" Height="131" Canvas.Left="19.875" Canvas.Top="7" Width="18" Fill="#FF96C240" RenderTransformOrigin="0.5,0.5">
                                        <Rectangle.RenderTransform>
                                            <TransformGroup>
                                                <ScaleTransform/>
                                                <SkewTransform/>
                                                <RotateTransform/>
                                                <TranslateTransform/>
                                            </TransformGroup>
                                        </Rectangle.RenderTransform>
                                    </Rectangle>
                                </Canvas>
                            </Viewbox>
                            <Grid Width="230" Height="37" Canvas.Left="10" Canvas.Top="235">
                                <TextBlock x:Name="Positions" FontSize="25" HorizontalAlignment="Center" VerticalAlignment="Center">
                                </TextBlock>
                            </Grid>
                            <Grid Width="130" Height="49" Canvas.Left="98" Canvas.Top="72">
                                <TextBlock x:Name="CurrentValue" Text="123℃" Foreground="#FF96C240" FontSize="50" HorizontalAlignment="Center" VerticalAlignment="Center">
                                    <TextBlock.Effect>
                                        <DropShadowEffect/>
                                    </TextBlock.Effect>
                                </TextBlock>
                            </Grid>
                            <Grid Width="60" Height="31" Canvas.Left="60" Canvas.Top="14">
                                <TextBlock x:Name="MaxValue" FontSize="20" Foreground="#FF96C240" HorizontalAlignment="Center" VerticalAlignment="Center">
                                    <TextBlock.Effect>
                                        <DropShadowEffect/>
                                    </TextBlock.Effect>
                                </TextBlock>
                            </Grid>
                            <Grid Width="60" Height="31" Canvas.Left="60" Canvas.Top="144">
                                <TextBlock x:Name="MinValue" Text="100℃" FontSize="20" Foreground="#FF96C240" HorizontalAlignment="Center" VerticalAlignment="Center">
                                    <TextBlock.Effect>
                                        <DropShadowEffect/>
                                    </TextBlock.Effect>
                                </TextBlock>
                            </Grid>
                        </Canvas>
                    </Canvas>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>

<Window x:Class="Sample.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Sample" xmlns:tempeture="clr-namespace:Tempeture;assembly=Tempeture"
        mc:Ignorable="d"
        Title="MainWindow" Height="350" Width="525">
    <StackPanel>
        <tempeture:Temp x:Name="myMeter" MaxValue="100"/>
        <TextBox Text="{Binding ElementName=myMeter,Path=CurrentValue,UpdateSourceTrigger=PropertyChanged}"/>
    </StackPanel>
</Window>

```