---
title: WPF-属性与依赖
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
abbrlink: b140a941
date: 2022-11-23 13:03:12
pic:
---

### 属性与依赖
依赖属性是一种类型为DependencyProperty的属性,其依赖属性标识则是依赖属性的实例.
1.DependencyObject:继承该类后才可以注册和拥有依赖属性
2.WPF属性系统:WPF通过提供一系列服务扩展普通的.Net属性.这些服务总称为WPF属性系统.
3..Net属性包装器:指属性的get和set的实现.在这个实现中均调用DependencyObject的GetValue和SetValue方法.
1.依赖属性对资源引用的支持
2.依赖属性对样式的支持
3.依赖属性对动画的支持
4.依赖属性对数据绑定的支持
5.依赖属性对属性值继承的支持
6.依赖属性对元数据重载的支持
依赖属性和普通.Net属性区别之一是有一个元数据对象.通过设置元数据对象,可以改变依赖属性的状态和行为.一般用到的元数据类是PropertyMetaData和FrameworkPropertyMetaData,前者是后者的基类.
PropertyMetaData--->UIPropertyMetaData--->FrameworkPropertyMetaData.
一般依赖属性的元数据类型为PropertyMetaData,而大部分控件的依赖属性,如按钮的WIdth及Background这样的依赖属性就会用到FrameworkPropertyMetaData元数据对象.
一般元数据对象包括如下类型的信息:
1.默认值
2.引用回调函数 其中PropertyChangedCallback,在属性值发生改变时调用;CoerceValueCallback用于限制属性值.
3.如果是框架级别的一些属性,则会有一些标识告知WPF该属性的某些状态信息.

7.依赖属性对WPF设计器的集成支持
###依赖属性的组成部分
``` bash
public static DependencyProperty Register(string name,Type propertyType,Type ownerType,PropertyMetadata typeMetadata,ValidateValueCallback validateValue validateValueCallback)
```
第一个参数是指的属性名,这个名字和依赖属性相关的.Net属性名相同,第二个参数是指该.Net属性的类型.第三个属性是指该依赖属性属于什么类型.第四个参数是依赖属性的元数据.第五个参数是值验证的回调函数.该回调函数是负责检验值的最后一道关卡.
### 自定义依赖属性
实现依赖属性必须满足以下条件:
1.该类必须继承自DependencyObject类.只有DependencyObject类才可以注册和拥有依赖属性.
2.该类中必须定义一个public static readonly成员变量,类型为DependencyProperty.
3.该依赖属性必须以属性名+Property命名
4.必须调用DependencyProperty的注册方法,在WPF属性系统中注册该依赖属性或者使用依赖属性的AddOwner方法.两种方法均返回一个DependencyProperty类型的标识并将其保存在定义的DependencyProperty成员变量中.
5.为依赖属性实现一个.Net属性包装器.
``` bash
//依赖属性
public static readonly DependencyProperty SpaceProperty;
//.Net属性包装器
public int Space{set{setValue(SpaceProperty,value);} get{return (int)GetValue(SpaceProperty);}}
//静态的构造函数
//定义元数据
static SpaceButton{
FrameworkPropertyMetadata metadata=new FrameworkPropertyMetadata();
metadata.DefaultValue=0;
metadata.PropertyChangedCallback+=OnSpacePropertyChanged;
//注册依赖属性
SpaceProperty=DependencyProperty.Register("Space",typeof(int),typeof(SpaceButton),metadata,ValidataSpaceValue);
}
//值验证的回调函数
static bool ValidateSpaceValue(object obj){
	int i=(int)obj;
	return i>=0;
}
//属性值改变的回调函数
static void OnSpacePropertyChanged(DependencyObject obj,DependencyPropertyChangedEventArgs args){
	SpaceButton btn=obj as SpaceButton;
string txt=btn.Content as string;
if(txt==null)return;
btn.Content=btn.SpaceOutText(txt);
}
//该方法为字符间距添加空格
string SpaceOutText(string str){
	if(str==null)return null;
	StringBuilder build=new StringBuilder();
	//在其中添加Space个空格
foreach(char ch in str){
	build.Append(ch+new string('  ',Space));
	return build.Tostring();
	}
}
}

```
#### 为依赖属性增加属性值继承的特性.
``` bash
static SpaceWindow(){
	FrameworkPropertyMetadata metadata=new FrameworkPropertyMetadata();
	metadata.Inherits=true;
	SpaceProperty=SpaceButton.SpaceProperty.AddOwner(typeof(SpaceWindow));
	SpaceProperty.OverriderMetadata(typeof(Window),metadata);
}

public static readonly DependencyProperty SpaceProperty;
public int Space{
	set{
	SetValue(SpaceProperty,value);
	}
	get{
	return (int)GetValue(SpaceProperty);
}
}
```
注册依赖属性Space不是通过注册而来,而是从SpaceButton的Space属性的AddOwner方法得来的.
即依赖属性可以选择把自身添加给其他属性,这是普通属性不可实现的.需要特别注意元数据不能再使用,必须新建一个,为了实现属性值继承,将Inherit标识为true.
### 所有规则大排队
1.直接设置的值>样式中触发器设置的值>样式中Setter设置的值.
### 依赖属性设置优先级列表.
优先级列表.
1.限制 Coerce 2.动画 3. 本地值 包括再代码和Xaml中直接设置的值,以及动态资源引用和数据绑定.
4.模板的父类 TemplateParent Triggers Setters 在模板的父类中Triggers设置依赖属性值的优先级高于在Setter中设置依赖属性值.5.样式触发器 主要指在Application或者Page中的样式,不包括主题样式.
6.模板触发器 7.样式设置 8.主题样式 9.属性值继承 10.元数据的默认值
### 附加属性和"等餐号"
#### 附加属性的本质
附加属性实质上是一个依赖属性,与普通的依赖属性相比有以下不同
1.注册不再是通过Register方法注册,而是通过RegisterAttached方法注册.
2.没有普通的.Net属性包装器,而是通过Get和Set属性名来实现属性包装.
3.没有普通的.Net属性.
一个附加属性IsBubbleSource
```bash
public static readonly DependencyProperty IsBubbleSourceProperty=DependencyProperty.RegisterAttached("IsBubbleSource",typeof(Boolean),typeof(AquariumObject),new FrameworkPropertyMetadata(false,FrameworkPropertyMetadataOptions.AffectsRender));
public static void SetIsBubbleSource(UIElement element,Boolean value){
	element.SetValue(IsBubbleSourceProperty,value);
}
public static Boolean GetIsBubbleSource(UIElement element){
	return (Boolean)element.GetValue(IsBubbleSourceProperty);
}
```
