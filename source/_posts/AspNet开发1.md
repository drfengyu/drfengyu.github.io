---
title: AspNet开发1
categories:
  - Asp.Net
tags:
  - C#
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: c33edf33
date: 2023-01-04 10:27:03
pic:
---
#### ASP.NET网页语法
### ASP.NET网页扩展名
文件                扩展名
Web用户控件  	.ascx
HTML页		.htm
XML页		.xml
母版页		.master
Web服务		.asmx
全局应用程序类	.asax
Web配置文件	.config
网站地图		.sitemap
外观文件		.skin
样式表		.css
### 页面指令
ASP.NET页面中前几行一般是<%@...%>这样的代码,这叫做页面指令,用来定义ASP.NET页分析器和编译器使用的特定于该页的一些定义.在.aspx文件中使用的页面指令一般有以下几种:
1.<%@Page%>
2.<%@Import Namespace="Value"%>
3.<%@OutputCache%>
4.<%@Implements Interface="接口名称"%>
5.<%@Register%>
### ASPX文件内容注释
服务端注释<%-注释内容-%>,允许开发人员在ASP.Net应用程序文件的任何部分(除了<script>代码块内部)嵌入代码注释.
如果<script>代码块中的代码需要注释,则使用HTML代码中的注释(<!--注释内容//-->)
### 服务器端文件包含
服务器端文件包含用于将指定文件的内容插入ASP.NET文件中,这些文件包含网页(.aspx文件),用户控件文件(.ascx文件)和Global.ascx文件.包含文件是在编译之前被包含的文件按原始格式插入到原始位置,相当于两个文件组合为一个文件,两个文件的内容必须符合.aspx文件的要求.
语法:
<!-- #include file|virtual="filename"-->
例如,使用服务器端包含指令语法调用将在Asp.net页上创建页眉的文件,这里使用的是相对路径
<html>
<body>
<!-- #Include virtual="/include/header.ascx" -->
</body>
</html>
### HTML服务器控件语法
默认情况下,Asp.net文件中的HTML元素作为文本进行处理,页面开发人员无法在服务器端访问文件中的HTML元素,要使这些元素可以被服务器端访问,必须将HTML元素作为服务器控件进行分析和处理,这可以通过为HTML元素添加runat="server"属性来完成,服务器端通过HTML元素的id属性引用该控件
语法:
<控件名 id="名称" ...runbat="server">
例如:
<body>
<form id="form1" runbat="server">
<input id="btnRed" type="button" value="Red" onclick="return btnRed_onClick()"/>
</form>
</body>
### ASP.NET服务器控件语法
ASP.NET服务器控件比HTML服务器控件具有更多内置功能,web服务器控件不仅包含窗体控件(如按钮和文本框),而且还包括特殊用途的控件(如日历,菜单和树视图控件).Web服务器控件不仅包括窗体控件(如按钮和文本框),而且还包括特殊用途的控件(如日历,菜单和树视图控件),Web服务器控件与Html服务器控件相比更为抽象,因为其对象模型不一定反映HTML语法.
语法如下:
<asp:控件名 ID="名称" ...组件的其他属性... runbat="server"/>
例如:
<form id="form1" runbat="server">
<div>
<asp:Button ID="btnTest" runbat="server" Text="服务器按钮控件"/>
</div>
</form>
### 代码块语法
代码块语法是定义网页呈现时所执行的内嵌代码.定义内嵌代码的语法标记元素为:
<%内嵌代码%>
例如:
<form id="form1" runbat="server">
<%if(DateTime.Now.Hour<12)%>
上午好
<%else%>
下午好
</form>
#### 制作一个ASP.NET网站
### 设计Web页面
1.加入Asp.net网页
2.布局Asp.net网页
3.添加服务器控件
### 添加Asp.net文件夹
文件夹		说明
Bin		包含程序所需的所有已编译程序集(.dll文件).应用程序中自动引用Bin文件夹中的代码所表示的任何类
APP_Code	包含页使用的类(如.cs .vb .jsl文件)的源代码
App_GlobalResources包含编译到具有全局范围的程序集中的资源(.resx和.resources文件)
App_LocalResources 包含与应用程序中的特定页,用户控件或母版页关联的资源(.resx和.resources文件)
App_WebReferences 包含用于定义在应用程序中使用的Web引用的引用协定文件(.wsdl),架构(.xsd)和发现文档文件(.disco和.discomap文件)
App_Browers	包含Asp.net用于标识个别浏览器并确定其功能的浏览器定义(.browser)文件
主题		包含用于定义Asp.NET网页额和控件外观的文件集合(.skin和.css,图像文件及一般资源)

##### ASP.NET开发实例大全 提高卷
#### ASP.NET网络开发常备技术
1.1.正则表达式
001.使用正则表达式验证电话号码
关键技术
本实例主要用到Regex类的IsMatch方法用于指示正则表达式使用pattern参数中指定的正则表达式是否在输入字符串中找到匹配项
1.input:字符串对象,表示要搜索匹配项的字符串
2.pattern:字符串对象,表示要匹配的正则表达式模式
3.bool:方法返回布尔值,如果正则表达式找到匹配项,则返回true,否则false
设计过程
1.打开vs,新建一个网站,命名为Validatephone，默认主页为Default.aspx.
2.在Default.aspx页面中添加一个TextBox控件,用于输入电话号码,添加一个Button,用于验证电话号码
``` bash
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
        <div>
        </div>
        <asp:Label ID="Label1" Font-Size="16px" runat="server" Text="电话号码:"></asp:Label>

        <asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
        <br />
        <br />
        <asp:Label ID="Label2" Font-Size="16px" runat="server" Text="号码格式:"></asp:Label>

        XXXX-XXXXXXXX<br />
        <asp:Button ID="Button1"  runat="server" Text="电话验证" OnClick="Button1_Click" />

       

    </form>
</body>
</html>
using System;
using System.Text.RegularExpressions;


public partial class _Default : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {

    }

    protected void Button1_Click(object sender, EventArgs e)
    {
       if(isTelePhone(TextBox1.Text.Trim())){
           
        }
        else
        {
           
        }
    }
    public bool isTelePhone(string str_telephone) {
        return Regex.IsMatch(str_telephone,@"^(\d{3,4}-)?\d{6,8}$");
    }
}
```
秘笈心法
正则表达式中“^”用于匹配行首,如果正则表达式匹配以First开头的行,则正则表达式如下:
^First
002.使用正则表达式验证输入密码条件
关键技术
[A-Za-z]表示匹配一个大写或小写的字母;+表示最少匹配一个大写或小写字母,[0-9]表示匹配一个数字
``` bash
  <div class="col-md-4">
            <h2>使用正则表达式验证密码输入条件</h2>
            <p>
               密码必须由数字和26个英文字母组成的密码字符
            </p>
            <p>
                密码: 
                <asp:TextBox ID="TextBox1" runat="server" Height="16px" Width="248px"></asp:TextBox>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="验证密码" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label1" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button1_Click(object sender, EventArgs e)
    {
        if (IsPassword(TextBox1.Text))
        {
            Label1.Text = "密码格式正确!";
            Label1.ForeColor = Color.Green;
        }
        else
        {
            Label1.Text = "密码格式错误!";
            Label1.ForeColor = Color.Red;
        }
    }
 public bool IsPassword(string str_password) {
        return Regex.IsMatch(str_password,@"[A-Za-z]+[0-9]");
    }
```
秘笈心法
正则表达式中匹配位置的元字符“$”
正则表达式中"$"用于匹配行尾,如果正则表达式匹配以End结尾的行,则正则表达式如下:
End$
003.使用正则表达式验证邮政编码
关键技术
“^\d{6}$”其中^表示匹配行开始,\d表示匹配数字,{6}是限定符,表示匹配6个数字,$表示匹配行结束.
``` bash
<div class="col-md-4">
            <h2>3.使用正则表达式验证邮政编码</h2>
            <p>
               邮政编码:
                <asp:TextBox ID="TextBox3" runat="server" Height="16px" Width="248px"></asp:TextBox>
            </p>
            <p>
                <asp:Button ID="Button3" runat="server" OnClick="Button3_Click" Text="验证邮政编码" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label3" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button3_Click(object sender, EventArgs e)
    {
        if (IsPostalCode(TextBox3.Text))
        {
            Label3.Text = "邮政编码格式正确!";
            Label3.ForeColor = Color.Green;
        }
        else
        {
            Label3.Text = "邮政编码格式错误!";
            Label3.ForeColor = Color.Red;
        }
    }
 public bool IsPostalCode(string str_postalCode) {
        return Regex.IsMatch(str_postalCode,@"^\d{6}$");
    }
```
秘笈心法
正则表达式中匹配字符的元字符“\w”和“\W”
正则表达式中“\w”用于匹配单词字符,单词字符包括汉字,字母,数字和下划线;"\W"用于匹配任意的非单词字符.
004.使用正则表达式验证手机号
“^[1][3-5]\d{9}$”正则表达式,其中[1]表示匹配一个数字1;[3-5]表示匹配数字3,4,5中的任何一个数字,\d表示匹配数字,{9}是限定符,表示匹配9个数字
``` bash
 <div class="col-md-4">
            <h2>4.使用正则表达式验证手机号</h2>
            <p>
               手机号:
                <asp:TextBox ID="TextBox4" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button4" runat="server" OnClick="Button4_Click" Text="手机号验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label4" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button4_Click(object sender, EventArgs e)
    {
        if (isUsePhone(TextBox4.Text))
        {
            Label4.Text = "手机号格式正确!";
            Label4.ForeColor = Color.Green;
        }
        else
        {
            Label4.Text = "手机号格式错误!";
            Label4.ForeColor = Color.Red;
        }
    }
 public bool isUsePhone(string str_usePhone) {

        //^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$
        //^[1]((3[\d])|(4[5,6,9])|(5[0-3,5-9])|(6[5-7])|(7[0-8])|(8[1-3,5-8])|(9[1,8,9]))\d{8}$
        return Regex.IsMatch(str_usePhone, @"^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$");
    }
```
秘笈心法
正则表达式中匹配字符的元字符"\s"和"\S".
正则表达式中"\s"用于匹配任意的空白字符,包括换行符,空格,制表符等,"\S"用于匹配任意的非空白字符.
005.使用正则表达式验证身份证号
关键技术
“(^\d{18}$)|(^\d{15}$)”
``` bash
  <div class="col-md-4">
            <h2>5.使用正则表达式验证身份证号</h2>
            <p>
               身份证号:
                <asp:TextBox ID="TextBox5" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button5" runat="server" OnClick="Button5_Click" Text="身份证号验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label5" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button5_Click(object sender, EventArgs e)
    {
        if (isIDCard(TextBox5.Text))
        {
            Label5.Text = "身份证号格式正确!";
            Label5.ForeColor = Color.Green;
        }
        else
        {
            Label5.Text = "身份证号格式错误!";
            Label5.ForeColor = Color.Red;
        }
    }
 public bool isIDCard(string str_IDCard) {
        return Regex.IsMatch(str_IDCard,@"(^\d{18}$)|(^\d{15}$)");
    }
```
秘笈心法
正则表达式中匹配字符的元字符“\d”和“\D”
"\d"用于匹配任意的数字,数字包括了0,1,2,3,4,5,6,7,8,9;"\D"用于匹配任意的非数字字符
006.使用正则表达式验证两位小数
关键技术
"^[0-9]+\.[0-9]{2}$"正则表达式,其中[0-9]+表示匹配一个或多个数字,\.表示匹配小数点,[0-9]{2}表示匹配两个数字.
``` bash
  <div class="col-md-4">
            <h2>6.使用正则表达式验证两位小数</h2>
            <p>
               小数:
                <asp:TextBox ID="TextBox6" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button6" runat="server" OnClick="Button6_Click" Text="小数验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label6" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button6_Click(object sender, EventArgs e)
    {
        if (isXiaoShu(TextBox6.Text))
        {
            Label6.Text = "小数格式正确!";
            Label6.ForeColor = Color.Green;
        }
        else
        {
            Label6.Text = "小数格式错误!";
            Label6.ForeColor = Color.Red;
        }
    }
public bool isXiaoShu(string str_Xiaoshu) {
        return Regex.IsMatch(str_Xiaoshu,@"^[0-9]+\.[0-9]{2}$");
    }
```
秘笈心法
正则表达式中匹配元字符".".
正则表达式中“.”用于匹配除换行符以外的任意字符,例如,正则表达式"^...$"匹配了字符串“abc",同时也会匹配123和”_la“.
007.使用正则表达式验证一年的12个月份
关键技术
"^(0?[1-9]|1[0-2])$"正则表达式,其中0？表示匹配零个或1个”0“:[1-9]表示匹配数字1-9,1[0-2]表示匹配数字10，11，12
``` bash
 <div class="col-md-4">
            <h2>7.使用正则表达式验证一年的12个月份</h2>
            <p>
               输入月份信息:
                <asp:TextBox ID="TextBox7" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button7" runat="server" OnClick="Button7_Click" Text="月份验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label7" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button7_Click(object sender, EventArgs e)
    {
        if (isMonth(TextBox7.Text))
        {
            Label7.Text = "月份格式正确!";
            Label7.ForeColor = Color.Green;
        }
        else
        {
            Label7.Text = "月份格式错误!";
            Label7.ForeColor = Color.Red;
        }
    }
public bool isMonth(string str_month) {
        return Regex.IsMatch(str_month,@"^(0?[1-9]|1[0-2])$");
    }
```
秘笈心法
正则表达式中的限定符"!"
正则表达式中的每一个元字符都只能匹配一个字符,如果要匹配多个字符时,则需要使用限定符,限定符用于指定字符自身重复出现的次数
008.使用正则表达式验证一个月的31天
"^((0?[1-9])|[1-2][0-9]|3[0-1])$"其中0？表示匹配0个或1个0,[1-9]表示匹配数字1-9,（1|2）表示匹配1或2,30|31表示匹配数字30或31.
``` bash
  <div class="col-md-4">
            <h2>8.使用正则表达式验证一个月的31天</h2>
            <p>
               输入天数信息:
                <asp:TextBox ID="TextBox8" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button8" runat="server" OnClick="Button8_Click" Text="天数验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label8" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button8_Click(object sender, EventArgs e)
    {
        if (isDay(TextBox8.Text))
        {
            Label8.Text = "天数格式正确!";
            Label8.ForeColor = Color.Green;
        }
        else
        {
            Label8.Text = "天数格式错误!";
            Label8.ForeColor = Color.Red;
        }
    }
 public bool isDay(string str_day) {
        return Regex.IsMatch(str_day,@"^((0?[1-9])|[1-2][0-9]|3[0-1])$");
    }
```
秘笈心法
正则表达式中的"?"限定符.
正则表达式中的"?"限定符用于限定指定的字符出现0次或1次.
^abc?$
上面的正则表达式可以匹配字符串abc和ab.表达式c?表示字母c可以出现1次或不出现.
009.使用正则表达式验证数字输入
关键技术
”^[0-9]*$“，[0-9]*表示匹配0个至多个数字
``` bash
   <div class="col-md-4">
            <h2>9.使用正则表达式验证数字输入</h2>
            <p>
               输入数字:
                <asp:TextBox ID="TextBox9" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button9" runat="server" OnClick="Button9_Click" Text="数字验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label9" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button9_Click(object sender, EventArgs e)
    {
        if (isNumber(TextBox9.Text))
        {
            Label9.Text = "数字格式正确!";
            Label9.ForeColor = Color.Green;
        }
        else
        {
            Label9.Text = "数字格式错误!";
            Label9.ForeColor = Color.Red;
        }
    }
public bool isNumber(string str_number) {
        return Regex.IsMatch(str_number,@"^[0-9]*$");
    }
```
正则表达式中的“*”限定符
用于限定指定字符至少出现0次
^abc*$
上面的正则表达式可以匹配字符串ab,abc,abcc等.表达式c*表示字母c可以出现0次或无数次.
010.使用正则表达式验证密码长度
关键技术
“^\d[6,18]$”正则表达式,其中\d{6,18}表示匹配6-18个数字.
``` bash
   <div class="col-md-4">
            <h2>10.使用正则表达式验证密码长度</h2>
            <p>
               输入密码:
                <asp:TextBox ID="TextBox10" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button10" runat="server" OnClick="Button10_Click" Text="密码验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label10" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button10_Click(object sender, EventArgs e)
    {
        if (IsPasswLength(TextBox10.Text))
        {
            Label10.Text = "密码格式正确!";
            Label10.ForeColor = Color.Green;
        }
        else
        {
            Label10.Text = "密码格式错误!";
            Label10.ForeColor = Color.Red;
        }
    }
public bool IsPasswLength(string str_Length) {
        return Regex.IsMatch(str_Length,@"^\d{6,18}$");
    }
```
011.使用正则表达式验证非零正整数
“^\+?[1-9][0-9]*$”，其中\+?表示匹配零个或一个正号"+"，[0-9]*表示匹配零个或多个数字.
``` bash
 <div class="col-md-4">
            <h2>11.使用正则表达式验证非零正整数</h2>
            <p>
               输入数值:
                <asp:TextBox ID="TextBox11" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button11" runat="server" OnClick="Button11_Click" Text="正数验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label11" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button11_Click(object sender, EventArgs e)
    {
        if (IsIntNumber(TextBox11.Text))
        {
            Label11.Text = "密码格式正确!";
            Label11.ForeColor = Color.Green;
        }
        else
        {
            Label11.Text = "密码格式错误!";
            Label11.ForeColor = Color.Red;
        }
    }
 public bool IsIntNumber(string int_Number) {
        return Regex.IsMatch(int_Number,@"^\+?[1-9][0-9]*$");
    }
```
秘笈心法
正则表达式中的“{n}”限定符.
正则表达式中的“{n}”限定符用于限定指定的字符出现n次.
^c{5}$
上面的正则表达式表示可以匹配字符串ccccc,字母c可出现5次
012.使用正则表达式验证非零负整数
同上,+变-
秘笈心法
正则表达式中的“{n,}”限定符
正则表达式中的“{n,}”限定符用于限定指定的字符至少出现n次.
^c{5,}$
上面的正则表达式可以匹配字符串ccccc和cccccc等.表达式c{5,}表示字母c可以出现5次或5次以上
013.使用正则表达式验证大写字母
“^[A-Z]+$”正则表达式,其中[A-Z]表示匹配一个或多个大写字母
秘笈心法
Char结构的IsUpper方法
如果为大写返回true，否则false.
014.使用正则表达式验证小写字母
同上 A-Z变a-z
秘笈心法
Char结构的IsLower依次判断每个字符是否为小写,是true,否false.
015.使用正则表达式检查字符串中重复出现的词
关键技术
Regex类的Matches方法用于在输入字符串中搜索pattern参数中提供的正则表达式的匹配项
public static MatchCollection Matches(string input,string pattern,RegexOptions options)
Matches方法中的参数说明
参数		说明
input		要搜索匹配项的字符串
pattern		要匹配的正则表达式模式
options		RegexOption枚举值的按位"或"组合
MatchCollection	方法返回找到的成功匹配的集合
``` bash
  <div class="col-md-4">
            <h2>15.使用正则表达式验证字符串中重复出现的词</h2>
            <p>
                 <asp:Label ID="Label13" runat="server" Text=" The the quick brown for jumped over the lary dog dog. "></asp:Label>
             
            </p>
           
             <p>
                <asp:Button ID="Button12" runat="server" OnClick="Button12_Click" Text="列出字符串中重复出现的词" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label12" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button12_Click(object sender, EventArgs e)
    {
        if (!GetSameWords(Label13.Text))
        {
            Label12.Text = "没有重复单词!";
            Label12.ForeColor = Color.Green;
        }
        else
        {
            Label12.Text = "有单词重复!";
            Label12.ForeColor = Color.Red;
        }
    }
public bool GetSameWords(string str_words) {
        MatchCollection matches = Regex.Matches(str_words,@"\b(?<word>\w+)\s+(\k<word>)\b",RegexOptions.Compiled|RegexOptions.IgnoreCase);
        if (matches.Count!=0)
        {
            foreach (Match match in matches)
            {
                string word = match.Groups["word"].Value;
                Response.Write("<script>alert('重复单词:"+word.ToString()+"');</script>");
            }
            return true;
        }
        else
        {
            Response.Write("<script>alert('没有重复的单词');</script>") ;
            return false;
        }
    }
```
秘笈心法
正则表达式中的"{n,m}"限定符
正则表达式中的"{n,m}"限定符用于限定指定的字符至少出现n次,最多出现m次
^c{2,3}$
上面的正则表达式可以匹配字符串cc或ccc,表达式c{2,3}表示字母c至少可以出现2次,最多出现3次
016.使用正则表达式替换字符串
关键技术
Regex类的的Replace方法用于在指定的输入字符串中,使用指定的替换字符串替换与指定正则表达式匹配的所有字符串
public static string Replace(string input,string pattern,string replacement)
input		要搜索匹配的字符串
replacement	替换字符串
pattern		要匹配的正则表达式模式
string		方法返回被替换后的字符串对象
``` bash
 <div class="col-md-4">
            <h2>16.使用正则表达式替换字符串</h2>
            <p>
                 <asp:Label ID="Label14" runat="server" Text=" 被替换的字符串: "></asp:Label>
             
                 <asp:TextBox ID="TextBox12" Text="1a2b3c4d5e6f7g8h9" runat="server" Width="215px"></asp:TextBox>
             
            </p>
            <p>
                 <asp:Label ID="Label16" runat="server" Text=" 字母替换为: "></asp:Label>
               
                  <asp:TextBox ID="TextBox13" Text="9" runat="server" Width="242px"></asp:TextBox>
             
            </p>
             <p>
                <asp:Button ID="Button13" runat="server" OnClick="Button13_Click" Text="替换" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label15" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button13_Click(object sender, EventArgs e)
    {
        Label15.Text = ReplaceWords(TextBox12.Text,TextBox13.Text);
    }
public string ReplaceWords(string str_words,string replace_words) {
        string str = Regex.Replace(str_words,@"[A-Za-z]\*?",replace_words);
        string showstr = $"替换前字符:{str_words},替换的字符:{replace_words},替换后的字符:{str}";
        Response.Write("<script>alert('"+showstr+"');</script>");
        return showstr;
    }
```
秘笈心法
字符串对象的Replace方法可以方便的替换字符串对象中指定的字符或字符串
017.使用正则表达式拆分字符串
关键技术
Regex类的split方法用于正则表达式模式定义位置拆分输入字符串
public static string[] Split(string input,string pattern)
input	字符串对象,表示要拆分的字符串
pattern	字符串对象,表示要匹配的正则表达式模式
string[]	方法返回字符串数组,表示拆分后的字符串数组
``` bash
 <div class="col-md-4">
            <h2>17.使用正则表达式拆分字符串</h2>
            <p>
                 <asp:Label ID="Label17" runat="server" Text=" 被拆分的字符串: "></asp:Label>
             
                 <asp:TextBox ID="TextBox14" Text="1a2b3c4d5e6f7g8h9" runat="server" Width="215px"></asp:TextBox>
             
            </p>
            <p>
                 <asp:Label ID="Label18" runat="server" Text=" 拆分后字符: "></asp:Label>
               
                  <asp:TextBox ID="TextBox15" Text="" runat="server" Width="242px"></asp:TextBox>
             
            </p>
             <p>
                <asp:Button ID="Button14" runat="server" OnClick="Button14_Click" Text="拆分" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label19" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button14_Click(object sender, EventArgs e)
    {
        Label19.Text = SplitWords(TextBox12.Text, TextBox13.Text);
    }
public string SplitWords(string str_words, string replace_words)
    {
        string text="";
        string[] str = Regex.Split(str_words, @"[1-9]\*?");
        foreach (var item in str)
        {
            text += item;
        }
        string showstr = $"拆分前字符:{str_words},拆分后的字符:{text}";
        Response.Write("<script>alert('" + showstr + "');</script>");
        return showstr;
    }
```
秘笈心法
字符串对象的split方法
018.使用正则表达式验证输入字母
“^[A-Za-z]+$”表示匹配一个或多个大写或小写字母
秘笈心法
Char结构的IsLetter方法依次判断用户输入字符串中的每一个字符是否为字母,如果为字母返回true，否则为false
019.使用正则表达式验证中文汉字输入
关键技术
“^[\u4e00-\u9fa5]{1,}$”表示匹配一个或多个汉字
``` bash
<div class="col-md-4">
            <h2>19.使用正则表达式验证汉字输入</h2>
            <p>
               输入汉字:
                <asp:TextBox ID="TextBox16" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button15" runat="server" OnClick="Button15_Click" Text="汉字验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label20" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 public bool isChinese(string str_chinese) {
        return Regex.IsMatch(str_chinese,@"^[\u4e00-\u9fa5]{1,}$");
    }
 protected void Button15_Click(object sender, EventArgs e)
    {
        if (isChinese(TextBox16.Text))
        {
            Label20.Text = "输入的是汉字!";
            Label20.ForeColor = Color.Green;
            
        }
        else
        {
            Label20.Text = "输入格式不正确!";
            Label20.ForeColor = Color.Red;
        }
        Response.Write("<script>alert('" + Label20.Text + "')</script>");
    }
```
秘笈心法
Char结构的IsDigit方法用来验证是否为10进制数字,可以用for循环遍历字符串中的字符,依次判断每一个字符是否为数字,如果是数字则方法返回true,否则返回false.
020.使用正则表达式验证输入字符串
关键技术
.匹配除换行符外的任意字符..{8,}表示匹配除换行符以外的8个或8个以上字符.
``` bash
 <div class="col-md-4">
            <h2>20.使用正则表达式验证字符串输入</h2>
            <p>
               输入字符串:
                <asp:TextBox ID="TextBox17" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button16" runat="server" OnClick="Button16_Click" Text="字符串验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label21" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button16_Click(object sender, EventArgs e)
    {
        if (isStr(TextBox17.Text))
        {
            Label21.Text = "输入的是字符串!";
            Label21.ForeColor = Color.Green;

        }
        else
        {
            Label21.Text = "输入格式不正确!";
            Label21.ForeColor = Color.Red;
        }
        Response.Write("<script>alert('" + Label21.Text + "')</script>");
    }
public bool isStr(string str) {
        return Regex.IsMatch(str, @"^.{8,}$");
    }
```
秘笈心法
字符串对象的Length方法
021.使用正则表达式验证E-mail格式
“^((\w\.)+)@(([0-9]{1,3}\.[0-9]{1,3}))|((\w+\.)+)@([a-zA-Z]{2,4}|[0-9]{1,3})(\.[a-zA-Z]{2,4})$”
([a-zA-Z]{2,4}|[0-9]{1,3})(\.[a-zA-Z]{2,4})表示匹配服务器域名
``` bash
<div class="col-md-4">
            <h2>21.使用正则表达式验证E-mail格式</h2>
            <p>
               输入E-mail地址:
                <asp:TextBox ID="TextBox18" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button17" runat="server" OnClick="Button17_Click" Text="E-mail验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label22" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button17_Click(object sender, EventArgs e)
    {
        if (isEmail(TextBox18.Text))
        {
            Label22.Text = "输入的是邮箱!";
            Label22.ForeColor = Color.Green;

        }
        else
        {
            Label22.Text = "输入格式不正确!";
            Label22.ForeColor = Color.Red;
        }
        Response.Write("<script>alert('" + Label22.Text + "')</script>");
    }
public bool isEmail(string str_email) {
        return Regex.IsMatch(str_email, @"^(([\w\.]+)@(([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}))|((\w+\.?)+)@([a-zA-Z]{2,4}|[0-9]{1,3})(\.[a-zA-Z]{2,4}))$");
    }
```
秘笈心法
在使用正则表达式中,经常会有验证带有小数点的字符串,.表示在正则表达式除了换行符外的任意字符.那么要怎样才可以匹配字符“.”,可以使用转义字符“\”实现,"\."
022.使用正则表达式验证IP地址
关键技术
“（25[0-5]|2[0-4]\d|[1]\d{2}|[1-9]?\d）”表示匹配数字0-255
``` bash
 <div class="col-md-4">
            <h2>22.使用正则表达式验证IP地址</h2>
            <p>
               输入IP地址:
                <asp:TextBox ID="TextBox19" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button18" runat="server" OnClick="Button18_Click" Text="IP验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label23" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
 protected void Button18_Click(object sender, EventArgs e)
    {
        if (isIP(TextBox19.Text))
        {
            Label23.Text = "输入的是IP!";
            Label23.ForeColor = Color.Green;

        }
        else
        {
            Label23.Text = "输入格式不正确!";
            Label23.ForeColor = Color.Red;
        }
        Response.Write("<script>alert('" + Label23.Text + "')</script>");
    }
public bool isIP(string str_IP)
    {
        string num = @"(25[0-5]|2[0-4]\d|[1]\d{2}|[1-9]?\d)";
        return Regex.IsMatch(str_IP,$"^{num}\\.{num}\\.{num}\\.{num}$");
    }
```
秘笈心法
使用split代替正则表达式验证IP地址
首先调用Split方法根据字符.拆分IP地址字符串为字符串数组,字符串转换为数值类型判断数值范围即可
023.使用正则表达式验证URL
关键技术
“http(s)?://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?",[\w-]+\.表示匹配一个或多个以单词字符和连接符号“-”组成的字符串开头的,i以字符.结尾的字符串,(/[\w-./?%&=]*)?表示匹配出现零次或一次由单词字符“-”“/”“.”"?" "%" "&" "="组成的长度至少为1的字符串
``` bash
<div class="col-md-4">
            <h2>23.使用正则表达式验证网址</h2>
            <p>
               输入网络地址:
                <asp:TextBox ID="TextBox20" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
           
             <p>
                <asp:Button ID="Button19" runat="server" OnClick="Button19_Click" Text="网址验证" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label24" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
protected void Button19_Click(object sender, EventArgs e)
    {
        if (isInternet(TextBox20.Text))
        {
            Label24.Text = "输入的是网址!";
            Label24.ForeColor = Color.Green;

        }
        else
        {
            Label24.Text = "输入格式不正确!";
            Label24.ForeColor = Color.Red;
        }
        Response.Write("<script>alert('" + Label24.Text + "')</script>");
    }
public bool isInternet(string str_IE) {
        return Regex.IsMatch(str_IE, @"http(s)?:([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?");
    }
```
1.2.网站信息的站内搜索
024.一般搜索
关键技术
Like运算符用于确定给定的字符串是否与指定模式匹配,模式可以包含常规字符和通配符字符.模式匹配过程中,常规字符必须与字符串中指定的字符完全匹配,但是,通配符可使用字符串的任意片段进行匹配.
例如,要查询tb_info表中信息名称包含有C的记录.
select * from tb_info where InfoName like '%C%'
% 包含零个或更多任意字符串,例如,like "%a%"表示查找在字符串任何位置包含a的值
-   任何单个字符,例如,like 'a_b'表示查找以a开头,以b结尾的值,并且在这两个字符之间有任意一个字符.
[]  属于指定范围或集合中的任何单个字符.例如,[af]表示属于指定范围([a-f])或集合([abcdef])中的任何单个字符
[^] 不属于指定范围或集合的任何单个字符.例如,[^af]表示不属于指定范围(a-f)或集合([abcdef])的任何单个字符
Default.aspx页面用到的控件及用途
HTML/Table		布局页面
标准/Label	labInfoID	信息ID
		labInfoName信息名称
		labInfoType信息类型
		labInfoContent信息内容
		labPage	当前页码
		labBackPage总页码
标准/Button	btnSearch搜索
标准/TextBox	txtSKWord搜索关键字
标准/DropDownList	ddlSCondition 搜索条件
		ddlInfoType 信息类型
标准/LinkButton	InkBtnOne 第一页
		InkbtnUp	  上一页
		InkbtnNext 下一页
		InkbtnBack 最后一页
数据/DataList	dlInfo	显示搜索结果
本实例主要用到了DataList空间分页技术.首先自定义一个dllDataBind方法,该方法有sqlstr,table和dkf三个参数,分别表示要执行的SQL语句,数据表名和DataList控件要绑定的数据表主键字段名.dlDataBind方法用来从数据库中查找出相关记录并计算出计算总数,然后对DataList控件进行数据绑定,
``` bash
 <div class="col-md-4">
            <h2>24.一般搜索</h2>
            <p>
              搜索条件:&nbsp;&nbsp;&nbsp;&nbsp;
              
                <asp:DropDownList ID="ddlInfoType" runat="server" Height="16px" Width="158px">
                    <asp:ListItem>ErrorCode</asp:ListItem>
                    <asp:ListItem>RunStatus</asp:ListItem>
                    <asp:ListItem>ErrorInfo</asp:ListItem>
                </asp:DropDownList>
               &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
               搜索关键字:

                <asp:TextBox ID="txtSKWord" runat="server" Width="164px" Text="软件关闭"></asp:TextBox>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <asp:Button ID="btnSearch" runat="server" Text="搜索" Width="95px" OnClick="btnSearch_Click" />

            </p>
           
             <p>
                 搜索结果:
                 </p>
             <p>
                 
               
                 
                 <asp:DataList ID="dllInfo" runat="server" CellPadding="4" ForeColor="#333333" Width="760px">
                     <AlternatingItemStyle BackColor="White" />
                     <FooterStyle BackColor="#507CD1" Font-Bold="True" ForeColor="White" />
                     <HeaderStyle BackColor="#507CD1" Font-Bold="True" ForeColor="White" />
                     <ItemStyle BackColor="#EFF3FB" />
                     <ItemTemplate>
                         <asp:Label ID="Label24" runat="server" Text="信息ID:"></asp:Label>
                          <asp:Label ID="labInfoID" runat="server" Text='<%# DataBinder.Eval(Container.DataItem,"ID") %> '></asp:Label>
                           &nbsp;&nbsp;&nbsp;
                          <asp:Label ID="Label25" runat="server" Text="信息名称:"></asp:Label>
                          <asp:Label ID="labInfoName" runat="server" Text='<%# DataBinder.Eval(Container.DataItem,"ModuleCode") %> '></asp:Label>
                          &nbsp;&nbsp;&nbsp;
                          <asp:Label ID="Label26" runat="server" Text="信息类别:"></asp:Label>
                          <asp:Label ID="labInfoType" runat="server" Text='<%# DataBinder.Eval(Container.DataItem,"ErrorCode") %> '></asp:Label>
                         <p>
                             信息内容:
                             </p>
                          <p>
                             <asp:Label ID="labInfoContent" runat="server" Text='<%# DataBinder.Eval(Container.DataItem,"ErrorInfo") %> '></asp:Label>
                             </p>
                     </ItemTemplate>

                     <SelectedItemStyle BackColor="#D1DDF1" Font-Bold="True" ForeColor="#333333" />
                 </asp:DataList>
                 
               
                 
                
                 
               
                 
               
                 
               
                 
                
                 
               
                 
                 </p>
            <p>
                 
               当前页码为:
                 
                
                 
                 <asp:Label ID="labPage" runat="server" Text="1"></asp:Label>
                 
                
                 &nbsp;&nbsp;&nbsp;
                 
                
                 总页码为:

                 <asp:Label ID="labBackPage" runat="server" Text="5"></asp:Label>

                 &nbsp;&nbsp;&nbsp;

                 <asp:LinkButton ID="InkbtnOne" Text="第一页" runat="server"></asp:LinkButton> 
                &nbsp;&nbsp;&nbsp; 
                <asp:LinkButton ID="InkbtnUp" Text="上一页" runat="server"></asp:LinkButton>
                 &nbsp;&nbsp;&nbsp;
                <asp:LinkButton ID="InkbtnNext" Text="下一页" runat="server"></asp:LinkButton>

                 &nbsp;&nbsp;&nbsp;
                 <asp:LinkButton ID="InkbtnBack" Text="最后一页" runat="server"></asp:LinkButton>
                 </p>
        </div>
 protected void btnSearch_Click(object sender, EventArgs e)
    {
        this.dllInfoBind();
    }
    SqlConnection sqlconn;
    public void dllDataBind(string sqlstr, string table, string dkf,int psize)
    {
        int curpage = Convert.ToInt32(this.labPage.Text);
        PagedDataSource ps = new PagedDataSource();
        if (sqlconn.State==ConnectionState.Closed)
        {
            sqlconn.Open();
        }
       
        SqlDataAdapter MyAdapter= new SqlDataAdapter(sqlstr,sqlconn);
        DataSet ds=new DataSet();
        MyAdapter.Fill(ds,table);
        ps.DataSource = ds.Tables[table].DefaultView;
        ps.AllowPaging= false;//是否可以分页
        ps.PageSize = ps.PageCount;//显示的数量
        ps.CurrentPageIndex= curpage-1;//取得当前页的页码
        this.InkbtnUp.Enabled = true;
        this.InkbtnOne.Enabled = true;
        this.InkbtnNext.Enabled = true;
        this.InkbtnBack.Enabled = true;
        if (curpage==1)
        {
            this.InkbtnOne.Enabled = false;//不显示第一页按钮
            this.InkbtnUp.Enabled= false;//不显示上一页按钮
        }
        if (curpage==ps.PageCount)
        {
            this.InkbtnNext.Enabled = false;//不显示下一页按钮
            this.InkbtnBack.Enabled= false;//不显示最后一页按钮
        }
        this.labBackPage.Text=Convert.ToString(ps.PageCount);
        this.dllInfo.DataSource= ps;
        this.dllInfo.DataKeyField = dkf;
        this.dllInfo.DataBind();

    }
    public void dllInfoBind() {

            string sqlstr = $"SELECT * FROM [ZHH].[dbo].[OEE_DT] where {ddlInfoType.SelectedValue.Trim()} like '%{txtSKWord.Text.Trim()}%'";
        sqlconn = new SqlConnection("Data Source=localhost;Initial Catalog=ZHH;Integrated Security=True");
        if (sqlconn.State==ConnectionState.Closed)
            {
                sqlconn.Open();
            }
            SqlCommand comm=new SqlCommand(sqlstr, sqlconn);
            DataSet ds=new DataSet();
            SqlDataReader dr=comm.ExecuteReader();
            if (!dr.Read())
            {
            dr.Close();
            Response.Write("<script>alert('没有你要查询的结果,请重新输入')</script>");
                dllInfo.Visible = false;
            }
            else
            {
            dr.Close();
                this.dllDataBind(sqlstr, "OEE_DT","ID",5);
            }
            
        }
```
秘笈心法
运算符 
<>不等于
!> 不大于
页面定时刷新后跳转到其他页
可以在<head></head>中利用meta标签来定义.例如,页面停留5秒后自动刷新到URL网址
<Meta http-equiv="Refresh" Content="5;Url=http://www.xia8.net">
如何锁定Application对象
由于Application对象属于网站的全局变量,因此经常会出现多人同时访问和修改对象的情况.为了避免两个会话同时修改Application对象,需要在修改前锁定Application对象,修改后再解锁Application对象.Application都西昂可以使用Lock方法实现加锁功能,j使用UnLock方法实现解锁功能.
026.常用搜索
``` bash
<div class="col-md-4">
            <h2>26.常用搜索</h2>
            <p>
              查询条件:&nbsp;&nbsp;&nbsp;&nbsp;
              
                <asp:DropDownList ID="DropDownList1" runat="server" Height="16px" Width="158px">
                    <asp:ListItem>ErrorCode</asp:ListItem>
                    <asp:ListItem>RunStatus</asp:ListItem>
                    <asp:ListItem>ErrorInfo</asp:ListItem>
                </asp:DropDownList>
               &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
               搜索关键字:

                <asp:TextBox ID="TextBox21" runat="server" Width="164px" Text="软件关闭"></asp:TextBox>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <asp:Button ID="Button20" runat="server" Text="搜索" Width="95px" OnClick="ComSearch_Click" />

            </p>
           
             <p>
                 搜索结果:
                 </p>
             <asp:GridView ID="gvGoodsInfo" runat="server" Width="722px">
             </asp:GridView>
          
        </div>
protected void ComSearch_Click(object sender, EventArgs e)
    {
        if (TextBox21.Text=="")
        {
            string sqlstr = "SELECT * FROM [ZHH].[dbo].[OEE_DT]";
            gvDataBind(gvGoodsInfo, sqlstr);
        }
        else
        {
            string sqlstr = $"SELECT * FROM [ZHH].[dbo].[OEE_DT] where {DropDownList1.SelectedValue.Trim()} like '%{TextBox21.Text.Trim()}%'";
            gvDataBind(gvGoodsInfo, sqlstr);
        }
       
    }

    private void gvDataBind(GridView gvGoodsInfo, string sqlstr)
    {
        sqlconn = new SqlConnection("Data Source=localhost;Initial Catalog=ZHH;Integrated Security=True");
        if (sqlconn.State==ConnectionState.Closed)
        {
            sqlconn.Open();
        }
        SqlDataAdapter myda = new SqlDataAdapter(sqlstr,sqlconn);
        DataSet ds = new DataSet();
        myda.Fill(ds);
        gvGoodsInfo.DataSource = ds;
        gvGoodsInfo.DataBind();
        sqlconn.Close();
    }
```
秘笈心法
如何删除Application变量内容
由于Application对象长期占用服务器资源,因此应及时清理无用的Application变量;例如,在本秘笈心法中,将保存在Application对象中名为"小明"的变量删除
``` bash
protected void Page_Load(object sender,EventArgs e){
Application.Add("姓名","小强");
Application.Add("姓名","小明");
Application.RemoveAt(1); //删除姓名为"小明"的Application变量
for(int i=0;i<Applicaiton.Count;i++){
Response.Write("姓名:"+Application[i]+"<br>");
}
Application.Clear();
}

```
027.在自己的网站中加入百度和Goole搜索
``` bash
<body>
<center>
     <form action="https://www.baidu.com/s" target="_blank" method="get">
         <table bgcolor="#FFFFFF" border="0" cellpadding="0" cellspacing="0" style="width:755px;height:59px;text-align:center">
             <tr><td>
                 <a href="https://www.baidu.com/s?wd=%E7%99%BE%E5%BA%A6%E7%83%AD%E6%90%9C&sa=ire_dl_gh_logo_texing&rsv_dl=igh_logo_pcs">
                 <img src="https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png" border="0" alt="Google"
                     align="absmiddle"/>
                 </a>
                 <input type="text" name="wd" size="31" maxlength="255" value=""/>
                 <input type="hidden" name="ie" value="utf-8"/>
                  <input type="hidden" name="f" value="3"/>
               
                 <input type="submit" name="btnG" value="百度一下"/>
                 </td></tr>
             </table>
         </form>
        </center>
</body>
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class BaiduSearch : Page
{
    protected void Page_Load(object sender, EventArgs e)
    {

    }
}
Site.master
<li><a runat="server" href="~/BaiduSearch">搜索</a></li>
```
秘笈心法
使用ViewState对象在同一个页面传值
ViewState对象用于在同一个页面内保存和还原状态信息.首先使用ViewState对象保存用户名mr,
例如:
ViewState["UserName"]="mr";
然后将保存在ViewState的值赋给Label控件
this.Label1.Text=ViewState["UserName"].ToString();
028.搜索引擎中智能匹配检索功能
关键技术
本实例的核心技术是通过Asp.NetAjaxControl Tookit中的AutoCompleteExtender控件实现的,AutoCompleteExtender控件实现自动输入功能,通过调用WebService或本页面对应的方法来获取提示数据,使用户能达到自动选择的功能
AutoCompleteExtender控件的主要属性及说明
TargetControlID	指定将被辅助完成自动输入的控件ID,这里的控件只能是TextBox.
ServicePath	指出提供服务的web服务路径,若不指出则ServiceMethod表示本页面对应的方法名
ServiceMethod	指出提供服务的方法名,例如,public string[] Method(string prefixText,int count),其中参数prefixText是用户输入的关键字,参数count是所需要获取提示数据的数量,两个参数都会自动传给WebService的ServiceMethod方法,返回值是用户所获的提示数据的来源数组
MinimumPrefixLength	指出开始提供提示服务时,TextBox控件应有的最小字符数,默认值为3
CompletionInterval		从服务器读取数据的时间间隔,默认为1000,单位为ms
EnableCaching	是否在客户端缓存数据,默认为true
CompletionSetCount	显示的条数,默认值为10
设计过程
新建一个Ajax网站,在Default.aspx页中主要添加一个ScriptManager控件,用于管理web页面中的Ajax控件,添加一个AutoCompleteExtender控件,用于实现自动完成功能,添加一个TextBox控件,用于接收输入检索关键字.
2.创建一个web服务,将其命名为KeyFind.asmx.该服务主要用于完成智能检索功能.在KeyFind.asmx web服务的keyFind.cs文件下实现如下代码:
``` bash
using System;
using System.Collections;
using System.Collections.Generic;
using System.Data;
using System.Data.OleDb;
using System.Data.SqlClient;
using System.Linq;
using System.Web;
using System.Web.Services;

/// <summary>
/// KeyFind 的摘要说明
/// </summary>
[WebService(Namespace = "http://tempuri.org/")]
[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
// 若要允许使用 ASP.NET AJAX 从脚本中调用此 Web 服务，请取消注释以下行。 
 [System.Web.Script.Services.ScriptService]
public class KeyFind : System.Web.Services.WebService
{

    public KeyFind()
    {

        //如果使用设计的组件，请取消注释以下行 
        //InitializeComponent(); 
    }
    private string[] autoCompleteWordList = null;
    //参数prefixText表示用户输入的前缀,count表示返回的个数
    [WebMethod]
    public string[] GetCompleteDepart(string prefixText, int count)
    {
        if (string.IsNullOrEmpty(prefixText) == true || count <= 0) return null;
        if (autoCompleteWordList==null)
        {
             SqlConnection conn = new SqlConnection(@"Data Source=localhost;Initial Catalog=ZHH;Integrated Security=True");
            if (conn.State == ConnectionState.Closed)
            {
                conn.Open();
            }
            SqlDataAdapter da = new SqlDataAdapter("SELECT *  FROM [ZHH].[dbo].[OEE_DT] where ErrorInfo like '%"+prefixText+"%' order by Datetime",conn);
            DataSet ds=new DataSet();
            da.Fill(ds);
            string[] temp = new string[ds.Tables[0].Rows.Count];
            int i = 0;
            foreach (DataRow dr in ds.Tables[0].Rows)
            {
                temp[i] = dr["ErrorInfo"].ToString();
                i++;
            }
            Array.Sort(temp,new CaseInsensitiveComparer());
            autoCompleteWordList = temp;//将临时数组的内容赋给返回数组
            if (conn.State==ConnectionState.Open)
            {
                conn.Close();
            }
        }
        //定义二叉树搜索的起点
        int index=Array.BinarySearch(autoCompleteWordList, prefixText,new CaseInsensitiveComparer());
        if (index<0)
        {
            index = -index;//修正起点
        }

        int matchCount = 0;//搜索符合条件的数据
        for (matchCount = 0; matchCount < count&& matchCount+index<autoCompleteWordList.Length;matchCount++)
        {//查看开头字符串相同的项
            if (autoCompleteWordList[index+matchCount].StartsWith(prefixText,StringComparison.CurrentCultureIgnoreCase)==false)
            {
                break;
            }

        }
        string[] matchResultList= new string[matchCount];//处理搜索结果
        if (matchCount>0)
        {
            Array.Copy(autoCompleteWordList,index,matchResultList,0,matchCount);//复制搜索结果
        }
        return matchResultList;
    }
    [WebMethod]
    public string HelloWorld()
    {
        return "Hello World";
    }

}
 <div class="col-md-4">
            <h2>28.搜索引擎中智能匹配检索功能
            </h2>
            <p>
            
                 <asp:TextBox ID="TextBox22" runat="server" Width="300px" Height="24px" Text="">
                    
                 </asp:TextBox>
            <ajaxToolkit:AutoCompleteExtender ID="AutoCompleteExtender1" runat="server" TargetControlID="TextBox22" ServicePath="KeyFind.asmx" CompletionSetCount="10" MinimumPrefixLength="1" ServiceMethod="GetCompleteDepart">
                </ajaxToolkit:AutoCompleteExtender>
                
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <asp:Button ID="Button21" runat="server" Text="搜索" Width="95px" OnClick="Search28_Click" />

               
               

            </p>
           
           
          
        </div>

```
秘笈心法
格式化输入数据为货币格式
主要用NumberFormatInfo类的CurrencyGroupSeparator属性格式化输入数据为货币格式.
CurrencyGrounpSeparator属性获取或设置在货币值中对小数点左边数字进行分组的字符串.
``` bash
<div class="col-md-4">
            <h2>28.格式化输入数据为货币格式</h2>
            <p>
                 <asp:Label ID="Label27" runat="server" Text=" 输入的数字: "></asp:Label>
             
                 <asp:TextBox ID="TextBox23" Text="" runat="server" Width="215px"></asp:TextBox>
             
            </p>
            <p>
                 <asp:Label ID="Label28" runat="server" Text=" 格式化货币: "></asp:Label>
               
                  <asp:TextBox ID="TextBox24" Text="" runat="server" Width="242px"></asp:TextBox>
             
            </p>
             <p>
                <asp:Button ID="Button22" runat="server" OnClick="Button22_Click" Text="格式化货币" Width="181px" />
            </p>
             <p>
                 
                 <asp:Label ID="Label29" runat="server" Text=" "></asp:Label>
                 
                 </p>
        </div>
private void Button22_Click(object sender,EventArgs e){
	NumberFormatInfo nfi=new CultureInfo("zh-CN",false).NumberFormat;
	nfi.CurrencyGroupSeparator="";
	textBox24.Text=Convert.ToDouble(textBox23.text).Tostring("c",nfi);
}
```
 1.3.思维扩展的常用算法
029.链表算法——单向链表
链表是一种重要的数据结构,该结构由节点构成,每个节点包含两部分数据,第一部分是节点本身的数据,第二部分是指向下一个节点的指针,对于单向链表,链表中存在两个特殊的节点,分别为头节点和尾节点,头节点本身没有数据,只存储下一个节点的指针,尾节点只存储数据.
head	5471	4512	5674	8778
5471	数据4512	数据5674	数据8778	数据Null
本实例中为了描述节点,设计了一个节点类,
``` bash
public class ListNode
{
    public ListNode(int NewValue)
    {
        //
        // TODO: 在此处添加构造函数逻辑
        //
        Value = NewValue;
    }
    public ListNode Previous;//前一个
    public ListNode Next;//后一个
    public int Value;//值
}
```
其中,私有成员Value用于存储节点本身的数据,Next用于存储下一个节点的指针,Previous用于存储上一个节点的指针,对于链表的操作,无非是进行节点的查找,插入和删除.
1.查找节点
在进行节点查找时,通常根据节点的数据查找节点.要实现节点的查找,首先需要解决的问题就是遍历链表中的所有节点,可以从链表头节点开始,利用循环的方式向下查找,如果当前节点指向的下一个节点不为空,则将下一个节点设置为当前节点.
2.插入节点
插入节点需要考虑两种情况:第一种情况是在链表尾节点插入节点,这种情况比较简单,只要将当前尾节点的Next(指向下一个节点的指针)指向新插入的节点即可.第二种情况是在链表的中间位置插入节点.首先需要确定在哪个节点后插入新的节点.这就需要由用户来确定,本实例中为了演示的需要,笔者将树视图控件中的当前选项设置尾当前节点,然后需要记录当前节点的下一个节点(避免发生"脱节"),可以定义一个临时的节点进行记录,将当前节点的下一个节点指向新添加的节点,再将新添加节点的下一个节点指向临时节点
3.删除节点
删除节点与插入节点类似,也需要考虑两种情况,第一种情况是删除尾节点,这种情况操作相对比较简单,首先找到尾节点之前的一个节点,将该节点指向的下一个节点设置为空,然后释放尾节点资源,第二种情况是删除链表中间部分的节点,首先需要找到当前节点的上一个节点,并定义一个临时节点PriorNode进行记录,然后再定义一个临时节点TempNode记录当前节点的下一个节点,最后将PriorNode的下一个节点指向TempNode,并释放当前节点
设计过程
1.创建一个类库,将其命名为UnilateralismChainTable,默认类名为Class1.
2.将Class1.cs文件重命名为Clist.cs
3.定义节点之后,开始类线性表的操作编程,在List类中,采用了Head,Tail,Current3个指针,使用Append,MoveFirst,MovePrevious,MoveNext,MoveLast,Delete,InsertAscending,Clear实现移动,添加,删除,升序插入,降序插入,清空链表操作,并用GetCurrentValue方法取得当前的值.
在构造函数中初始化,并定义指针变量,
``` bash
namespace UnilateralismChainTable
{
    public class Clist { 
        public Clist() {
            ListCountValue = 0;
            Head = null;
            Tail = null;
        }
        private ListNode Head;//头指针
        private ListNode Tail;//尾指针
        private ListNode Current;//当前指针
        private int ListCountValue;//链表数据的个数
        #region 尾部添加数据

        
        //尾部添加数据
        public void Append(int DataValue) {
            ListNode NewNode = new ListNode(DataValue);
            if (IsNull())
            {
                Head = NewNode; 
                Tail=NewNode;
            }
            else
            {
                Tail.Next = NewNode;
                NewNode.Previois = Tail;
                Tail = NewNode;
            }
            Current = NewNode;
            ListCountValue += 1;//链表数据个数加1
        }
        #endregion
        #region 删除数据


        /// <summary>
        /// 删除当前数据的代码
        /// </summary>
        public void Delete() {
            if (!IsNull())//若为空链表
            {
                if (IsBof())//若删除头
                {
                    Head = Current.Next;
                    Current = Head;
                    ListCountValue-= 1;
                    return;
                }
                if (IsEof())//若删除尾
                {
                    Tail = Current.Previois;
                    Current = Tail;
                    ListCountValue-= 1;
                    return;
                }
                Current.Previois.Next = Current.Next;//若删除中间数据
                Current = Current.Previois;
                ListCountValue-= 1;
                return;

            }
        }
        #endregion
        #region 移动数据


        /// <summary>
        /// 向后移动一个数据
        /// </summary>
        public void MoveNext() {
            if (!IsEof()) Current = Current.Next;
        }
        /// <summary>
        /// 向前移动一个数据
        /// </summary>
        public void MovePrevious() {
            if (!IsBof()) Current = Current.Previois;
        }
        //移动到第一个数据
        public void MoveFirst() {
            Current = Head;
        }
        /// <summary>
        /// 移动到最后一个数据
        /// </summary>
        public void MoveLast() {
            Current = Tail;
        }
        /// <summary>
        /// 判断是否为空链表
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsNull()
        {
            if (ListCountValue == 0)
                return true;
            return false;

        }
        /// <summary>
        /// 判断是否到达尾部
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsEof()
        {
            if (Current==Tail)
            {
                return true;
            }
            return false;
        }

        /// <summary>
        /// 判断是否为到达头部
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsBof()
        {
            if (Current==Head)
            {
                return true;
            };
            return false;
        }
        /// <summary>
        /// 获取节点值
        /// </summary>
        /// <returns></returns>
        public int GetCurrentValue() {
            return Current.Value;
        }
        /// <summary>
        /// 取得链表的数据个数
        /// </summary>
        public int ListCount
        {
            get { return ListCountValue; }
        }
        ///清空链表
        public void Clear() { 
            MoveFirst();
            while (!IsNull())
            {
                Delete();//若不为空链表,从尾部删除
            }
        }
        #endregion
        #region 在当前位置前插入数据
        public void Insert(int DataValue) { 
            ListNode NewNode=new ListNode(DataValue);
            if (IsNull())
            {
                Append(DataValue);//为空表,则添加
                return;
            }
            if (IsBof())
            {
                //为头部插入
                NewNode.Next = Head;
                Head.Previois = NewNode;
                Head = NewNode;
                Current = Head;
                ListCountValue += 1;
                return;
            }
            //中间插入
            NewNode.Next = Current;
            NewNode.Previois = Current.Previois;
            Current.Previois.Next = NewNode;
            Current.Previois = NewNode;
            Current = NewNode;
            ListCountValue += 1;
        }
        #endregion
        #region 升序插入
        public void InsertAscending(int InsertValue)
        {
            //参数:InsertValue 插入的数据
            if (IsNull())   //为空链表
            {
                Append(InsertValue);    //添加
                return;
            }
            MoveFirst();    //移动到头部
            if (InsertValue < GetCurrentValue()) {
                Insert(InsertValue);    //满足条件,则插入,退出
                return;
            }
            while (true)
            {
                if (InsertValue<GetCurrentValue)
                {
                    Insert(InsertValue);
                    break;
                }
                if (IsEof())
                {
                    Append(InsertValue);//尾部添加
                    break;
                }
                MoveNext();//移动到下一个指针
            }
            
        }
        #endregion
        #region 进行降序插入
        public void InsertUnAscending(int InsertValue) {
            //参数:InsertValue 插入的数据
            if (IsNull())       //为空链表
            {
                Append(InsertValue);//添加
                return;
            }
            MoveFirst();    //移动到头部
            if (InsertValue>GetCurrentValue())
            {
                Insert(InsertValue);    //满足条件,则插入,退出
                return;
            }
            while (true)
            {
                if (InsertValue>GetCurrentValue)
                {
                    Insert(InsertValue);    //满足条件,则插入,退出
                    break;
                }
                if (IsEof()) {
                    Append(InsertValue);    //尾部添加
                    break;
                }
                MoveNext(); //移动到下一个指针
            }
        }
        #endregion
    }
}
```
秘笈心法
"凯撒"密码的算法.在该算法中,单词的每一个字母都被字母表中与该字母相隔的4个字母的下一个字母替换,例如Abyz变成了Fgde.
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Web;

/// <summary>
/// CaesarHelper 的摘要说明
/// </summary>
public class CaesarHelper
{
   
    public string Caesar(string str)
    {
        int spaceNum = 5;
        char[] c = str.ToCharArray();
        string strCaesar = "";
        for (int i = 0; i < str.Length; i++)
        {
            string ins = c[i].ToString();
            string outs = "";
            bool isChar="abcdefghijklmnopqrstuvwxyz".Contains(ins.ToLower());
            bool isToUpperChar = isChar && (ins.ToUpper()==ins);
            ins=ins.ToLower();
            if (isChar)
            {
                int offset = (ASCII(ins) + spaceNum - ASCII("a"))%(ASCII("z") - ASCII("a")+1);
                outs = Convert.ToChar(offset+ASCII("a")).ToString();
                if (isToUpperChar)
                {
                    outs = outs.ToUpper();
                }
            }
            else
            {
                outs = ins;
                strCaesar += outs;
            }

        }
        return strCaesar;
    }

    private int ASCII(string ins)
    {
        byte[] array = new byte[1];
        array = Encoding.ASCII.GetBytes(ins);
        int asciicode = (short)(array[0]);
        return asciicode;
    }
}
```
030.链表算法---双向链表
实例说明
在单向链表的存储机构中,只有一个指示直接后继的指针域,因此从某个节点出发只能顺指针往后查询其他节点,若要查询节点的直接前驱,则需要从表头指针出发,为了克服单链表这种单向性的缺点,可以使用双向链表,在双向链表的节点中有两个指针域,一个指向直接前驱,另一个指向直接后继
关键技术
双向链表在删除节点时,不但要修改节点的直接后继指针,还要同时修改节点的直接前驱指针,在插入时更是要修改插入节点的前驱和后继的两个方向上的指针.
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class Clists
    {
        private string clistName = "";

        public Clists()
        {

        }
        /// <summary>
        /// 表名
        /// </summary>
        public string ClistName { get => clistName; set => clistName = value; }
        private DoubleListNode Head;//头指针
        private DoubleListNode Tail;//尾指针
        private DoubleListNode Current;//当前指针
        

        public DoubleListNode current { get => Current; set => Current = value; }
        private int ListCountValue;//链表数据的个数
        #region 尾部添加数据
        public void Append(DoubleLink DataValue) {
            DoubleListNode NewNode = new DoubleListNode(DataValue);
            if (IsNull())   //如果头指针为空
            {
                Head= NewNode;
                Tail = NewNode;
            }
            else
            {
                Tail.Next = NewNode;
                NewNode.Previous = Tail;
                Tail= NewNode;
            }
            Current= NewNode;
            ListCountValue += 1;
        }
        #endregion
        #region 删除当前数据
        public void Delete() {
            if (!IsNull())  //若为空链表
            {
                if (IsBof())  //若删除头部
                {
                    Head = Current.Next;
                    Current = Head;
                    ListCountValue-= 1;
                    return;
                }
                if (IsEof())    //若删除尾部
                {
                    Tail = Current.Previous;
                    Tail.Next = null;
                    Current = Tail;
                    ListCountValue -= 1;
                    return;
                }
                Current.Previous.Next = Current.Next;
                Current=Current.Previous;
                ListCountValue -= 1;
                return;
            }
        }
        /// <summary>
        /// 向后移动一个数据
        /// </summary>
        public void MoveNext() {
            if (!IsEof()) Current = Current.Next;
            
        }
        /// <summary>
        /// 向前移动一个数据
        /// </summary>
        public void MovePrevious() {
            if (!IsBof()) Current = Current.Previous;
        }
        /// <summary>
        /// 移动到第一个数据
        /// </summary>
        public void MoveFirst() {
            Current = Head;
        }
        public void MoveLast()
        {
            Current = Tail;
        }
        /// <summary>
        /// 判断是否到达尾部
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsEof()
        {
            if (Current == Tail)
                return true;
            else
            return false;
        }
        /// <summary>
        /// 判断是否到达头部
        /// </summary>
        /// <returns></returns>
        private bool IsBof()
        {
            if (Current == Head)
                return true;
            else
                return false;
        }
        #endregion
        private bool IsNull()
        {
            if (ListCountValue == 0)
                return true;
            else
                return false;
        }
        public DoubleLink GetCurrentValue() {
            return Current.Goods;
        }
        /// <summary>
        /// 取得链表的数据个数
        /// </summary>
        public int ListCount {
            get 
            {
                return ListCountValue;
            }
        }
        public void Clear() { 
            MoveFirst();
            while (!IsNull())
            {
                Delete();//若不为空,从尾部删除
            }
        }
        /// <summary>
        /// 在当前位置插入数据
        /// </summary>
        /// <param name="DataValue"></param>
        public void Insert(DoubleLink DataValue) {
            DoubleListNode NewNode = new DoubleListNode(DataValue);
            if (IsNull())
            {
                Append(DataValue);  //为空表,则添加
                return;
            }
            if (IsBof()) {
                ///为头部插入
                NewNode.Next = Head;
                Head.Previous = NewNode;
                Head = NewNode;
                Current = Head;
                ListCountValue += 1;
                return;
            }
            NewNode.Next = Current;
            NewNode.Previois = Current.Previous;//中间插入
            Current.Previous.Next = NewNode;
            Current.Previous = NewNode;
            Current = NewNode;
            ListCountValue+= 1;
        }
        /// <summary>
        /// 升序插入
        /// </summary>
        /// <param name="InsertValue"></param>
        public void InsertAscending(DoubleLink InsertValue) {
            if (IsNull())
            {
                Append(InsertValue);
                return;
            }
            MoveFirst();    //移动到头部
            if (InsertValue.Number<GetCurrentValue().Number)
            {
                Insert(InsertValue);
                return;
            }
            while (true)
            {
                if (InsertValue.Number<GetCurrentValue().Number)
                {
                    Insert(InsertValue);//满足条件则插入退出
                    break;
                }
                if (IsEof())
                {
                    Append(InsertValue);//尾部添加
                    break;
                }
                MoveNext();//移动到下一个指针
            }
        }
        /// <summary>
        /// 降序插入
        /// </summary>
        /// <param name="InsertValue"></param>
        public void InsertUnAscending(DoubleLink InsertValue) {
            if (IsNull())
            {
                Append(Insert);
                return;
            }
            MoveFirst();
            if (InsertValue.Number>GetCurrentValue().Number)
            {
                Insert(InsertValue);
                return;
            }
            while (true) {
                if (InsertValue.Number>GetCurrentValue().Number)
                {
                    Insert(InsertValue);
                    break;
                }
                if (IsEof())
                {
                    Append(InsertValue);
                    break;
                }
                MoveNext();//移动到下一个指针
            }
        }

    }
}
using System;
using System.Collections.Generic;
using System.Diagnostics.Metrics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class DoubleLink
    {
        private int number;
        private string name;
        private int counter;

        public DoubleLink(int num, string Name, int count)
        {
            //获取参数值
            number = num;
            name = Name;
            counter = count;
        }

        public int Number { get => number; private set => number = value; }
        public string Name { get => name; private set => name = value; }
        public int Counter { get => counter; private set => counter = value; }
    }
}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class DoubleListNode
    {
        private DoubleLink goods;

        public DoubleListNode(DoubleLink bugs)
        {
            goods = bugs;
        }
        public DoubleListNode Previous;
        private DoubleListNode next;

        public DoubleListNode Next { get => next; set => next = value; }

        public DoubleLink Goods { get => goods; private set => goods = value; }
    }
}

```
秘笈心法
使用MD5算法对密码进行加密
对密码进行加密的算法有很多种,在C#中较常使用的是MD5加密算法,它是一种用于产生数字签名的一项散列算法
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Cryptography;
using System.Text;
using System.Web;

/// <summary>
/// MD5Helper 的摘要说明
/// </summary>
public class MD5Helper
{
   
    public string Encrypt(string strPwd) { 
        MD5 md5=new MD5CryptoServiceProvider();
        byte[] data=Encoding.Default.GetBytes(strPwd);//将字符编码为一个字节序列
        byte[] md5data=md5.ComputeHash(data);
        md5.Clear();
        string str = "";
        for (int i = 0; i < md5data.Length-1; i++)
        {
            str += md5data[i].ToString("x").PadLeft(2,'0');
        }
        return str;
    }
}
```
031.常用算法----堆栈
栈是一种重要的数据结构,从数据结构的角度看,栈也是线性表,其特殊性在于栈的基本操作是线性表操作的子集,它们是操作受限的线性表,因此可以称为限定性的数据结构
关键技术
栈是限定仅在表尾进行插入或删除操作的线性表,因此对栈来说,表尾端有其特殊含义,称为"栈项(top)",相应的,表头端称为"栈底(bottom)".不含元素的空表称为空栈.栈的修改是按照后进先出的原则进行的,因此栈又称为后进先出的线性表,在实现栈的数据结构时一定要注意这个特点.栈的基本操作除了在栈顶进行插入或删除外,还有栈的初始化,判断是否为空以及取栈顶元素等.
``` bash
using Microsoft.VisualBasic.FileIO;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class CStack
    {
        public Clist m_List;//调用链表类
        public CStack() { 
            m_List= new Clist();
        }
        /// <summary>
        /// 压入堆栈
        /// </summary>
        /// <param name="PushValue"></param>
        public void Push(int PushValue) {
            m_List.Append(PushValue);
        }
        //弹出堆栈数据,如果为空,则取得2147483647为int的最大值
        public int Pop()
        {
            int PopValue;
            if (!IsNullStack())
            {
                MoveTop();//移动到顶部
                PopValue = GetCurrentValue();//取得弹出值
                Delete();//删除
                return PopValue;
            }
            return 2147483647;
        }

        /// <summary>
        /// 删除取得当前的节点
        /// </summary>
        /// <exception cref="NotImplementedException"></exception>
        private void Delete()
        {
           m_List.Delete();
        }
        /// <summary>
        /// 取得当前的值
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private int GetCurrentValue()
        {
            return m_List.GetCurrentValue();
        }

      
        /// <summary>
        /// 判断是否为空的堆栈
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsNullStack()
        {
            if (m_List.IsNull())
            {
                return true;
            };
            return false;
        }
        /// <summary>
        /// 堆栈的个数
        /// </summary>
        public int StackListCount
        {
            get {
                return m_List.ListCount;
            }
        }
        /// <summary>
        /// 移动到堆栈的底部
        /// </summary>
        public void MoveBottom() { 
            m_List.MoveFirst();
        }
        /// <summary>
        /// 移动到堆栈的顶部
        /// </summary>
        public void MoveTop() { 
            m_List.MoveLast();
        }
        /// <summary>
        /// 向上移动
        /// </summary>
        public void MoveUp() { 
        m_List.MoveNext();
        }
        /// <summary>
        /// 向下移动
        /// </summary>
        public void MoveDown()
        {

            m_List.MovePrevious();
        }
        /// <summary>
        /// 清空堆栈
        /// </summary>
        public void Clear() { 
        m_List.Clear();
        }
    }
}

```
秘笈心法
C#随机数的产生
其中int型参数Len为产生随机数的个数
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;

/// <summary>
/// RandomHelper 的摘要说明
/// </summary>
public class RandomHelper
{
    public string RandomNum(int Len) {
        string str= "0,1,2,3,4,5,6,7,8,9,";
        str += "a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,";
        str += "A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z";
        string[] S=str.Split(new char[] { ',' });
        string strNum = "";//用于记录上一个随机数的值,避免产生两个重复值
        int tag = -1;
        Random md=new Random();
        for (int i = 1; i <=Len; i++)
        {
            if (tag==-1)
            {
                md = new Random(i*tag*unchecked((int)DateTime.Now.Ticks));//初始化一个Random实例  
            }
            int mdNum = md.Next(61);//返回小于61的非负随机数
            if (tag!=-1&& tag==mdNum)
            {
                return RandomNum(1);
            }
            tag = mdNum;
            strNum += S[mdNum];
        }
        return strNum;
    }
}
```
032.常用算法——队列
实例说明
队列是链表的扩展,它是一种特殊的链表,和堆栈类似,它和堆栈的不同在于,堆栈采用的是后进先出原则,而队列采用的是先进先出原则
关键技术
和栈相反,队列是先进先出的线性表,其只允许在表的一端进行插入,而在另一端删除元素,在队列中,允许插入的一端称为队尾,允许删除的一端称为队头,在队列的实现过程中主要有两个操作,即入队和出队.
1.入队就是在队列的尾部添加数据,队列数据个数加1,尾指针后移.
2.出队就是在队列的头部取数据,然后删除该数据,头指针后移.
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class Queue
    {
        private Clist m_List;
        public Queue() { 
        m_List= new Clist();
        }
        /// <summary>
        /// 入队
        /// </summary>
        /// <param name="DataValue"></param>
        public void EnQueue(int DataValue) {
        m_List.Append(DataValue);
        }
        /// <summary>
        /// 出队
        /// </summary>
        /// <returns></returns>
        public int DeQueue() {
            int QueValue;
            if (!IsNull())
            {
                m_List.MoveFirst();
                QueValue=m_List.GetCurrentValue();
                m_List.Delete();
                return QueValue;
            }
            return 2147483647;
        }
        /// <summary>
        /// 判断队列是否为空
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private bool IsNull()
        {
           return m_List.IsNull();
        }
        /// <summary>
        /// 清空队列
        /// </summary>
        public void Clear() {
            m_List.Clear();
        }
        /// <summary>
        /// 取得队列的数据个数
        /// </summary>
        public int QueueCount { get { return m_List.ListCount; } }
    }
}

``
秘笈心法
判断IP是否正确
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;

/// <summary>
/// IPHelper 的摘要说明
/// </summary>
public class IPHelper
{
    public IPHelper()
    {
        //
        // TODO: 在此处添加构造函数逻辑
        //
    }
    public bool IsIP(string ip) {
        bool b = true;
        string[] lines = new string[4];
        string s = ".";
        lines = ip.Split(s.ToCharArray(), 4);//分隔字符串
        for (int i = 0; i < 4; i++)
        {
            if (Convert.ToInt32(lines[i])>=255 || Convert.ToInt32(lines[i])<0)
            {
                b = false;
                return b;
            }
        }
        return b;
    }
}
```
实例033.常用算法---树的实现
实例说明
树是以分支关系定义的层次结构,树结构在客观世界中广泛存在.
关键技术
树的抽象数据类型定义包含树的结构定义和基本操作
1.结构定义
树是n(n>=1)个节点的有限集,在任意一颗非空树中.
(1).有且仅有一个特定的节点称为根的节点
(2).当n>1时,其余节点可分为m个互不相交的有限集,其中每一个集合本身又是一棵树,并且称为根的子树.
2.基本操作
树的基本操作主要包括构造空树,销毁树,清空树,求树的深度,访问节点,插入子树和删除子树等.
``` bash
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    enum Position { LEFT,RIGHT};
    enum Tag
    {
        LINK,THREAD    //LINK指向孩子,THREAD指向后继
    };

    public interface IBinNode
    {
        bool isLeaf();
        object Element { get; set; }
        IBinNode Left { get; set; }
        IBinNode Right { get; set; }
    }
    //遍历,线索化等操作的接口
    interface ITravelBinTree
    {
        void PreOrderTravel();
        void InOrderTravel();
        void RevOrderTravel();
        void Print(IBinNode t);
    }
    interface IInsertBinTree
    {
        void Insert(IBinNode node, Position pos);
    }
    ///二叉树类
    class BinNodePtr : IBinNode
    {
        private object element;
        private IBinNode left;
        private IBinNode right;

        public BinNodePtr()
        {
            element = null;
            left = right = null;
        }

        public BinNodePtr(object element)
        {
            this.element = element;
            this.left = right = null;
        }


        public BinNodePtr(object element, IBinNode lchild, IBinNode rchild)
        {
            this.element = element;
            this.left = lchild;
            this.right= rchild;
        }

        public object Element { get => element; set => element = value; }
        public IBinNode Left { get => left; set => left = value; }
        public IBinNode Right { get => right; set => right = value; }

        public bool isLeaf()
        {
            if (left == null && right == null)
                return true;
            return false;
        }

    }
    class BinNodeLine : BinNodePtr, IBinNode
    {

        public BinNodeLine(object e, IBinNode left, IBinNode right) : base(e, left, right)
        {
            lTag = rTag = Tag.LINK;
        }

        public BinNodeLine(object element) : base(element)
        {
            lTag=rTag=Tag.LINK;
        }

        private Tag lTag;
        private Tag rTag;

        public Tag LTag { get => lTag; set => lTag = value; }
        public Tag RTag { get => rTag;set=> rTag = value; }
    }
    class TravelBinTree:ITravelBinTree,IInsertBinTree
    {
        const int INIT_TREE_SIZE = 20;
        private IBinNode tree;
        private BinNodeLine head;//线索化后的头指针
        private IBinNode prenode;//指向最近访问过的前驱节点
        public TravelBinTree() { 
        tree=new BinNodePtr();
        }

        public TravelBinTree(IBinNode INode)
        {
            this.tree = INode;
        }

        public void InOrderTravel()
        {
            InOrderTravel(tree);
        }

        private void InOrderTravel(IBinNode tree)
        {
            if (tree == null) return;
            InOrderTravel(tree.Left);
            Print(tree);
            InOrderTravel(tree.Right);
        }

        public void Insert(IBinNode node, Position pos)
        {
            if (node == null)
                throw (new InvalidOperationException("不能将节点插入树"));
            switch (pos)
            {
                case Position.LEFT:tree.Left = node;
                    break;
                case Position.RIGHT:tree.Right = node;
                    break;
                default:
                    break;
            }
        }
        /// <summary>
        /// 按照先序顺序遍历树
        /// </summary>
        public void TreeBuilder() { 
            Stack stk=new Stack(INIT_TREE_SIZE);
            stk.Push(tree);
            Position pos;
            string para;
            pos=Position.LEFT;
            IBinNode baby = new BinNodePtr(), temp=new BinNodePtr();
            while (true)
            {
                para = Console.ReadLine();
                if (para=="")
                {
                    if (pos==Position.RIGHT)
                    {
                        stk.Pop();
                        while (stk.Count!=0 && ((IBinNode)stk.Peek()).Right!=null)
                            stk.Pop();
                        if (stk.Count == 0) break;

                    }
                    else
                    {
                        pos = Position.RIGHT;
                    }
                }
                else
                {
                    if (tree.GetType().Equals(baby)==true)
                        baby = new BinNodePtr(para);
                    temp = (IBinNode)stk.Peek();
                    if (pos == Position.LEFT)
                        temp.Left = baby;
                    else
                        temp.Right = baby;
                    pos = Position.LEFT;
                    stk.Push(baby);
                }
            }
        }
        /// <summary>
        /// 中序线索化
        /// </summary>
        public void InOrderThreading() {
            head = new BinNodeLine("");
            head.RTag = Tag.THREAD;
            head.Right = head;
            if (tree == null) head.Left = head;
            else head.Left = tree;prenode = head;
            
        }
        private void InThreading(IBinNode t) {
            if (t == null)
                return;
            else
                InThreading(t.Left);
        }
        /// <summary>
        /// 先序遍历树,用非递归算法实现
        /// </summary>
        public void PreOrderTravel() {
            IBinNode temptree;
            Stack stk = new Stack(INIT_TREE_SIZE);
            if (tree==null)
                throw (new InvalidOperationException("访问的树为空."));
            temptree= tree;
            stk.Push(tree);
            while (stk.Count!=0)
            {
                while (temptree!=null)
                {
                    Print(temptree);
                    stk.Push(temptree.Left);
                    temptree=temptree.Left;
                }
                stk.Pop();//空指针退栈
                if (stk.Count!=0)
                {
                    temptree = (IBinNode)stk.Pop();
                    stk.Push(temptree.Right);
                    temptree=temptree.Right;
                }
            }
           
        }

        public void RevOrderTravel()
        {
            RevOrderTravel(tree);
        }

        private void RevOrderTravel(IBinNode tree)
        {
            if (tree == null) return;
            RevOrderTravel(tree.Left);
            RevOrderTravel(tree.Right);
            Print(tree);
        }

       

       public void Print(IBinNode t)
        {
            Console.WriteLine(t.Element + ",");
        }
    }
}

```
秘笈心法
如何去掉字符串中的所有空格
由于空格的ASCII码值是32,因此,在去掉字符串中所有空格时,只需循环访问字符串中的所有字符,并判断它们的ASCII码值是否为32即可.去掉字符串中所有空格的关键代码如下:
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class TrimStrHelper
    {
        /// <summary>
        /// 去掉字符串中的所有空格
        /// </summary>
        /// <param name="str"></param>
        /// <returns></returns>
        public string CheckTrim(string str) {
            string Result = "";
            CharEnumerator charEnumerator =str.GetEnumerator();
            while (charEnumerator.MoveNext())
            {
                byte[] array = new byte[1];
                array=System.Text.Encoding.ASCII.GetBytes(charEnumerator.Current.ToString());
                int asciicode = (short)(array[0]);
                if (asciicode!=32)
                {
                    Result += charEnumerator.Current.ToString();
                }
            }
            return Result;
        }
        
    }
}

```
034.排序算法——选择排序算法
实例说明
简单选择排序的基本思想是:每一趟在n个记录中选取关键字最小的记录作为有序序列中第一个记录,本实例实现了用选择排序的方法对一组数据进行排序.
关键技术
选择排序的主要操作是关键字间的比较,显然是从n个数据中进行简单排序的算法为:令I从1至n-1,进行n-1趟选择操作.
设计过程
1.创建一个控制台程序,将其命名为Choice.
2.添加一个类,并将文件命名为SelectionSorter.cs
3.主要程序卸代码如下:
``` bash
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UnilateralismChainTable
{
    public class SelectionSorter
    {
        private int min;
        public void Sort(int[] list) {
            for (int i = 0; i < list.Length; i++)   ///遍历数组中的数据,不包含最后一个
            {
                min = i;            //读取当前数据
                for (int j = j+1; j < list.Length; j++) //遍历数组中的数据,不包含最后一个
                {
                    if (list[j] < list[min])    //如果当前值是最小值
                        min= j;                 //将最小值放在相应位置
                }
                int t = list[min];
                list[min] = list[t];            //交换数据
                list[i] = t;
            }
        }
    }
}

```
秘笈心法
获得字符串中汉字的个数.
获得字符串中汉字的个数时,可以首先定义一个与汉字相匹配的正则表达式,然后使用charEnumerator对象的MoveNext方法循环访问字符串中的每个字符,如果访问的字符与定义的正则表达式相匹配,则将其添加到一个数组中,最后获得该数组的项数即为字符串中汉字的个数
``` bash
 /// <summary>
        /// 获得字符串中汉字的个数
        /// </summary>
        /// <param name="str"></param>
        /// <returns></returns>
        public string GetChineseCount(string str) { 
            int count = 0;
            ArrayList itemList=new ArrayList();
            CharEnumerator charEnumerator = str.GetEnumerator();
            Regex regex = new Regex("^[\u4E00-\u9FA5]{0,}$");
            while (charEnumerator.MoveNext())
            {
                if (regex.IsMatch(charEnumerator.Current.ToString(),0))
                    itemList.Add(charEnumerator.Current);
                count=itemList.Count;
            }
            return count.ToString();
        }
```
035.排序算法——插入排序算法
实例说明
直接插入排序是一种最简单的排序方法,基本操作是将一个记录插入到已排好序的有序表中,从而得到一个新的,记录数增1的有序表,本实例实现了用插入排序对一组数据进行排序
关键技术
实现插入排序主要是先找到一个有序序列,然后将下一个关键字插入上述有序序列,然后再从剩下的关键字中选取下一个插入对象,反复执行至整个序列有序.
``` bash
/// <summary>
        /// 插入排序
        /// </summary>
        /// <param name="list"></param>
        public void InsertSort(int[] list) {
            for (int i = 0; i < list.Length; i++)   ///遍历当前数组,不包含第一个和最后一个
            {
                int t = list[i];    ///获取当前值
                int j = i;          ///记录当前值的标记.
                while (j >0&& list[j]>t) {  ///插入法
                    list[j] = list[j-1];    ///交换顺序
                    --j;
                }
                list[j] = t;
            }
        }
```
秘笈心法
将字母全部转换为大写
将字母转换为大写时,需要调用string类的ToUpper方法
036.排序算法----希尔排序算法
希尔排序又称"缩小增量排序",也是一种插入排序类的方法,但在时间效率上较前述几种排序方法有较大的改进.
在待排序记录按关键字"基本有序"时,直接插入排序效率就可以大大提高,而且记录较少时效率也较高,希尔排序正是从这两点分析出发对直接插入排序进行改进得到的一种方法.
关键技术
先将整个待排序记录分割成为若干子序列分别进行直接插入排序,待整个序列中的记录"基本有序"时再对全体记录进行一次直接插入排序.
希尔排序的特点是:子序列的构成不是简单的"逐段分割",而是将相隔某个"增量"的记录组成一个子序列.
``` bash
 /// <summary>
        /// 希尔排序
        /// </summary>
        /// <param name="list"></param>
        public void ShellSort(int[] list) {
            int inc;
            for (inc = 1; inc <= list.Length / 9; inc = 3 * inc + 1) ;//遍历当前数组
            for (;inc>0;inc/=3) //遍历当前值
            {
                for (int i = inc+1; i <=list.Length; i+=inc)
                {
                    int t = list[i - 1];//获取值
                    int j = i;
                    while ((j > inc) && (list[j-inc-1]>t))  //希尔排序法
                    {
                        list[j - 1] = list[j - inc - 1];    //交换数据
                        j -= inc;
                    }
                    list[j - 1] = t;
                }
            }
            
        }
```
秘笈心法
将字符串首字母转换为大写
将字符串首字母转换为大写时,可以先使用string类的Substring方法将字符串的首字母截取出来,然后调用ToUpper方法将该字母转换为大写.将字符串首字母转换为大写关键代码如下:
``` bash
 /// <summary>
        /// 将字符串首字母转换为大写
        /// </summary>
        /// <param name="str"></param>
        public string ChangeFirststr(string str) {
            string str1 = str.Substring(0,1);
            string str2 = str.Substring(1, str.Length - 1);
            return str1.ToUpper() + str2;
        }
```
037.算法应用——按要求生成指定位数编号
实例说明
实现了按要求生成数字格式的功能
关键技术
本实例用到了TextBox控件的KeyPress事件
1.KeyPress事件
在控件有焦点的情况下按下键发生,其语法格式如下:
public event KeyPressEventHandler KeyPress
2.KeyPressEventArgs事件的Keychar属性
获取或设置与按下的键对应的字符
public char KeyChar{get;set;}
属性值:撰写的ASCII字符.例如,如果用户按下Shift+K快捷键,则该属性返回一个大写的K.
``` bash
private void Textbox25_KeyPress(object sender,KeyPressEventArgs e) {
        if (e.KeyChar==(Char)Keys.Return) //如果按下Enter键
        {
            if (TextBox25.Text.Length>8)  //如果位数大于8
            {
                TextBox25.Text = TextBox25.Text.Substring(0,8);//获取前8位数
            }
            else
            {
                int j=8-TextBox25.Text.Length;      //确定增加的位数
                for (int i = 0; i < j; i++)
                {
                    TextBox25.Text = "0" + TextBox25.Text;
                }
            }
        }
    }
```
秘笈心法
如何设置小数的保留位数
设置小数的保留位数时,用到了Math类的Round方法,该方法用来将小数值舍入到指定精度,其语法格式如下:
public static decimal Round(decimal d,int decimals)
其中,d表示要舍入的小数,decimals表示返回值中的有效数字位数(精度),设置小数保留位数关键代码如下:
textBox2.text=Convert.ToString(Math.Round(Convert.ToDouble(textBox1.Text.Trim()),2))
1.4.发布ASP.NET应用程序
038.发布ASP.NET应用程序
实例说明
将Web应用程序上传到服务器前,为了使Web应用程序源代码具有可靠的保密性和安全性,需要进行网站发布,网站发布后,将保留.aspx文件,.cs文件则被编译成.dll文件存放到bin文件夹下.
关键技术
进行网站发布时,首先要进行预编译网站.为发布进行预编译与就地预编译稍有不同,预编译实际执行的编译过程与通常在浏览器中请求页时发生的动态编译过程相同,预编译器从页产生程序集,包括标记和代码,它同时还编译App_Code,App_GlobalResources,App_LocalResources和App_Themes文件夹中的文件,另外它还可以同时编译单文件Asp.net页和代码隐藏页,发布过程只部署网站文件夹或子文件夹中的文件,而不部署Machine.config文件,因此,目标Web服务器的配置可能与计算机上的配置不同,这可能会影响应用程序的行为
设计过程
新建一个Web应用程序ReleaseAspNet,默认主页为Default.aspx.在程序中添加一个类文件,将其命名为BaseClass.cs.
2.在Default.aspx页面中添加一个Table控件,一个Label控件和一个Button控件,分别用来布局页面,显示文本和执行文本显示功能.
3.在BaseClass类中自定义一个方法用来返回一个字符串.
``` bash
public string TestString(){
	return "大家好";
}
```
在Default.aspx页面的Button1按钮下调用该类并执行显示文本操作.
``` bash
protected void Button1_Click(object sender,EventArgs e){
BaseClass bs=new BaseClass();
this.Label1.Text=bs.TestString();
}
```
4.以上步骤用于建立一个网站,实现简单的功能,网站创建完成后,即可对其进行发布,用户可以将网站发布到本地计算机或网络上能够访问的任何位置.需要注意的是,如果要发布到网络,该网络必须是Vs支持的连接协议.
把网站发布到本地文件夹,选择"生成"/“发布网站”命令,打开"发布网站"对话框
5.单击确定按钮,预编译网站内容,并将输出写入指定的文件夹."输出"窗口显示进度消息.如果编译时发生一个错误,"输出"窗口中会报告该错误.编译完成后,可对编译后的网站进行测试,用户可为编译后的网站创建一个虚拟目录,通过浏览器对网页进行访问.
秘笈心法
如何解决Response.Redirect方法传递汉字时出现的错误.
使用Response.Redirect方法传递汉字时,有时会发现传递的内容与接收的内容不一致,接收的值丢失了几个字或出现乱码.为了确保传递的汉字可以被正确的接收,可以在传值之前使用Server对象的UrlEncode方法对所传递的汉字进行URL编码.
``` bash
/// <summary>
    /// 如何解决Response.Redirect方法传递汉字时出现的错误.
    /// </summary>
    public void SetUrlEncode() {
        string name = Server.UrlEncode("如何解决Response.Redirect方法传递汉字");
        Response.Redirect("Default2.aspx?name="+name);
        ///接收值时,使用Server对象的UrlDecode方法对所接收的汉字进行URL解码
      name = Server.UrlDecode(Request.QueryString["name"]);

    }
```
039.使用"复制网站"发布Asp.Net网站
设计过程
1.在解决方案资源管理器页面选中当前网站,单击鼠标右键,在弹出的快捷菜单那中选择复制网站命令
2.单击连接
3.选择完要将网站复制到的位置后,选中要复制的网站文件或文件夹,将选中的网站文件或文件夹复制到指定位置.
秘笈心法
使用Response.BinaryWrite方法输出二进制图像
``` bash
protected void Page_Load(object sender, EventArgs e)
    {
        ///打开图片文件,并保存在文件流中
        FileStream stream = new FileStream(Server.MapPath(@"~/images/1.jpg"),FileMode.Open);
        long FileSize = stream.Length;//获取流的长度
        byte[] Buffer=new byte[(int)FileSize];//定义一个二进制数组
        stream.Read(Buffer,0,(int)FileSize);//从流中读取字节块并将该数据写入给定缓冲区中

        stream.Close();
        //设定Response类型
        Response.ContentType = "image/jpg";
        Response.BinaryWrite(Buffer);
    }
```
#### 第二章 Asp.Net前端技术应用开发
040.利用SMTP服务发送电子邮件
关键技术
使用Asp.Net应用程序发送电子邮件,应首先在服务器上安装和配置Internet信息服务(IIS)的简单邮件传输协议(SMTP)服务.IIS SMTP服务是一个简单组件,主要用来将电子邮件转发到SMTP服务器进行传递.
1.安装SMTP服务
在默认情况下,不随IIS安装SMTP服务,必须使用控制面板来安装,安装SMTP服务时将创建一个默认的SMTP配置,用户随后可以使用IIS管理器自定义该配置,SMTP服务的安装步骤如下：
(1).选择“开始”/“设置”/“控制面板”/“添加或删除程序“，打开”添加或删除程序“窗口
(2).单击对话框左侧的"添加/删除WIndows组件",打开”Windows组件向导“对话框.
(3).选中”应用程序服务器“复选框，单击”详细信息“按钮,打开”应用程序服务器“对话框
(4).选中”Internet信息服务(IIS)“复选框,单击”详细信息“按钮,在打开的”Internet信息服务器(IIS)“对话框中选中SMTPService复选框,准备安装SMTP服务.
(5).单击"确定"按钮,然后单击"下一步"按钮,安装完成后,单击”完成“按钮,从而完成简单邮件传输协议(SMTP)服务的安装.
2.配置SMTP虚拟计算机
安装SMTP服务后,系统将会在IIS管理器中创建一个新节点,若要配置SMTP虚拟服务器,必须启动IIS管理器.
(1).选择"开始"/"设置"/”控制面板“/"Internet信息服务(IIS)管理器"命令
(2).默认设置
默认SMTP虚拟服务器具有以下默认设置,如果想创建一个新的虚拟服务器,可以使用"新建虚拟服务器向导"来配置.
名称:出现在IIS管理器中的虚拟服务器的名称,可以在IIS管理器中更改虚拟服务器的名称.只需右击该虚拟服务器,在弹出的快捷菜单中选择"重命名"命令即可.
IP地址/TCP端口:IP地址均为"未分配",TCP端口号为25.可以使用"SMTP虚拟服务器属性",对话框中的"常规选项卡"来更改该配置.如果更改该设置,则必须指定一个没有被其他SMTP虚拟服务器使用的IP地址和TCP端口号组合.TCP端口号25是默认的TCP端口号,也是建议的TCP端口号,多个虚拟服务器可以使用同一个TCP端口号,但是必须为它们配置不同的IP地址.如果不设置唯一的IP地址和TCP端口号组合,则SMTP虚拟服务器将不会启动.
默认域:是指在"系统属性"中的"计算机名称"选项卡中列出的域名,一个SMTP虚拟服务器只能有一个默认域,且不能删除该域,若要在IIS管理器中更改域的名称,请双击虚拟服务器,然后双击"域",在右侧列表中选择"本地(或默认)域",然后使用鼠标右键单击本地(或默认)域,在弹出的快捷菜单中选择"重命名"命令即可.
主目录:C:\Inetpub/Mailroot.主目录是SMTP的根目录,必须是运行SMTP服务计算机的本地目录.
3.MailMessage类.
MailMessage类主要用于指定邮件的发送地址,收件人地址等.
Attachments 获取用于存储附加到此电子邮件的数据的附件集合.
Body	获取或设置邮件正文
From	获取或设置此电子邮件的发信人地址
Sender	获取或设置此电子邮件的发件人地址
Subject	获取或设置此电子邮件的主题
To	获取包含此电子邮件的收件人的地址集合
设计过程
用户设置完邮件服务器及邮件的所有信息后,单击"发送"按钮即可完成邮件的发送,"发送"按钮的Click事件代码如下:
``` bash
protected void btnSendClick(object sender,EventArgs e){
	string file=Server.MapPath("testXML.xml");
	MailMessage myMail=new System.Net.Mail.MailMessage();
	myMail=new System.Net.Mail.MailMessage(txtSender.Text.Trim(),txtReceiver.Text.Trim(),txtSubject.Text.Trim(),txtContent.Text.Trim());
	if(ddlAccessories.Items.Count>0){
	for(int i=0;i<ddlAccessories.Items.Count;i++){
	System.Net.Mail.Attachment myAttachment=new System.Net.Mail.Attachment(Server.MapPath(ddlAcce ssories.Items[i].Value),System.Net.Mine.MediaTypeNames.Application.Octet);
	System.Net.Mine.ContentDisposition=myAttachment.ContentDisposition;
	disposition.CreationDate=System.IO.File.GetCreationTime(file);
	disposition.ModificationDate=System.IO.File.GetLastWriteTime(file);
	myMail.Attachments.Add(myAttachment);
}
}
System.Net.Mail.SmtpClient client=new System.Net.Mail.SmtpClient("smtp.163.com",25);
client.Credentials=new System.Net.NetworkCredential("wangxiaoke68","sxwangxiaoke8568");
client.Send(myMail);
}
```
秘笈心法
040.关于SMTP服务器配置
1.安装SMTP服务时,将在C:\Inetpub\Mailroot中创建一个具有消息存储区的默认SMTP服务器配置
2.设置SMTP服务时,可以为SMTP服务配置全局设置,还可以为虚拟服务器的单个组件配置设置.IIS SMTP服务只是一个中继代理,电子邮件将转发到SMTP服务器进行传递.
041.利用Jmail组件发送邮件
关键技术
1.Jmail组件的属性和方法
Charset 	string	字符集
Encoding	string	设置附件的编码方式
ContentType string	邮件的内容类型
ISOEncodeHeaders string 	是否将信头编码为iso-8859-1 字符集
Priority	int	邮件的优先级
From	string	发件人的Email地址
FromName string 	发件人姓名
Subject	string	邮件主题
MailServerUserName string 登录邮件服务器的用户名
MailServerPassword  string  登录邮件服务器的用户密码
Jmail组件的常用方法及说明
AddHeader(XHeader,Value) 添加用户定义的信件标头
AddRecipient(emailAddress,recipientName,PGPKey) 添加收件人E-mail地址,姓名并对其加密
AddRecipientCC(emailAddress,recipientName,PGPKey) 添加抄送人E-mail地址,姓名并对其加密
AddRecipientBCC(emailAddress,PGPKey)	添加密送人E-mail地址并对其加密
AddAttachment(URL,附件名)	添加附件
Send	发送邮件
Connect	和邮件服务器建立连接,并接收邮件
DeleteMessages	清空邮件服务器中的邮件
Disconnect	断开和邮件服务器的连接
2.Jmail组件的使用
在使用Jmail组件之前,首先要添加对Jmail组件的引用
在解决方案资源管理器中找到要添加引用的网站项目,单击鼠标右键,在弹出的快捷菜单中选择"添加引用"命令,在打开的"引用管理器"对话框中选择"浏览",在其中选择要添加的jmail.dll文件,单击确定,将Jmail组件添加到网站项目的引用中,然后就可以直接在后台代码中使用其属性和方法
设计过程
用户设置完邮件服务器及邮件的所有信息后,单击发送按钮即可完成邮件的发送.实现该功能时,用户可以自定义一个发送邮件的方法,这样既可以提高代码的重用度,也可以方便代码的管理,然后在发送按钮的Click事件中直接调用该方法即可.
``` bash
protected void btnSend_Click(object sender,EventArgs e){
	sendEmail(txtSender.Text.Trim(),txtSUser.Text.Trim(),txtEUser.Text.Trim(),txtEPwd.Text.Trim(),txtReceiver.Text.Trim(),txtSubject.Text.Trim(),txtContent.Text.Trim(),txtEServer.Text.Trim());
}
```
发送邮件的自定义sendEmail方法如下:
``` bash
public void sendEmail(string sender,string senderuser,string euser,string epwd,string receiver,string subject,string body,string eserver){
	jmail.MessageClass jmMessage=new jmail.MessageClass();
	jmMessage.Charset="GB3212";
	jmMessage.ISOEncodeHeaders=false;
	jmMessage.From=sender;
	jmMessage.FromName=senderuser;
	jmMessage.Subject=subject;
	jmMessage.MailServerUserName=euser;
	jmMessage.MailServerPassword=epwd;
	jmMessage.AddRecipient(receiver,"","");
	if(ddlAccessories.Items.Count>0){
		for(int i=0;i<ddlAccessories.Items.Count;i++){
	string path=ddlAccessories.Items[i].Value;
	jmMessage.AddAttachment(@path,true,"");
}
}
	jmMessage.Body=body;
	if(jmMessage.Send(eserver,false)){
	Page.RegisterClientScriptBlock("ok","<script language="javascript">alert('发送成功')</script>");
}else{
	Page.RegisterClientScriptBlock("ok","<script language="javascript">alert('发送失败,请仔细检查邮件服务器的设置是否正确!')</script>");
}
jmMessage=null;
}
```
秘笈心法
页面定时刷新如何实现
1.在<head></head>中利用meta标签来定义,页面每隔30s刷新一次
<Meta http-equiv="Refresh" Content="30">
2.在<head></head>中利用Response.AddHeader方法来定义,页面每隔10s刷新一次
<%Response.AddHeader("Refresh","10");%>
042.使用POP3协议接收电子邮件
使用POP3协议实现电子邮件接收功能时,首先需要客户端与服务器进行连接,在客户端连接到服务器之前,注意把端口设为POP3协议默认端口110号,客户端连接服务器成功后,服务器端返回相应的信息,在返回信息中,回应信息不像SMTP协议那样用丰富多变的数字表示,而是用两个字符表示,+OK或者-ERR.其中,+OK表示连接成功,-ERR表示连接失败,接下来,客户端输入"USER <用户名>"命令,该命令告诉服务器用户的用户名,这里需要注意,有些服务器是区分大小写的,服务器返回+OK后,客户端再输入"PASS <口令>".用户名与密码都正确,服务器再次返回+OK,同时还返回一些邮箱的统计信息.例如,+OK 42071,不同的服务器返回的信息格式不太一样,所以可以用STAT命令来查看邮箱的情况.STAT命令的回应中有两个数字,分别表示邮件的数量和大小.如果信箱里有信,就可以用RETR命令来获取邮件的正文,RETR命令的格式为:RETR<邮件编号>.如果返回结果的第一行是+OK信息,则表示成功,第二行起便是邮件的正文,最后一行和SMTP协议一样,是一个单独的英文句号,表示邮件的结尾部分.
关键技术
使用POP3协议接收电子邮件,POP(post Office Protocol,邮局协议)用于电子邮件的接收和发送.现在常用第三版,所以称为POP3,通过POP协议,客户登录到服务器后就可以对自己邮件进行删除,也可以下载到本地操作.
POP3常用命令及说明
命令		说明
USER		此命令与下面的PASS命令都发送成功,将·使状态转换.
PASS		用户名所对应的密码
APOP		Digest是MD5消息摘要
STAT		请求服务器发回关于邮箱统计·资料(邮箱·总数和总字节数)
UIDL		回送邮件唯一标识符
LIST		回送邮件数量和每个邮件·大小
RETR		回送·由参数标识的·邮件·的·全部·文本·
DELE		服务器将·由·参数·标识·的·邮件·标记·为·删除,由QUIT命令·执行·
RSET		服务器·将重置所有标记为删除的邮件,用于撤销DELE命令
TOP		服务器将回送·由·参数·标识·的·邮件·的前·n行·内容·,n是·正整数.
NOOP		服务器·返回·一个·肯定·的·响应,不做任何·操作·
QUIT		退出
注意:SMTP服务器使用的·端口号·一般·为·25,POP服务器·使用·的端口号·一般为110.
设计过程·
1.新建·一个网站,将其命名为POP3,默认主页为Default.aspx
2.在页面·中添加5个TextBox文本框,分别·用于获取POP3服务器IP地址,登录邮箱名,邮箱·密码,读取第几封邮件·和·显示·读取出来的邮件·内容.
3.在页面中添加3个Button控件,分别用于连接邮箱,读取信件和退出邮箱.
4.首先·添加必要的命名空间.
``` bash
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="POP3.aspx.cs" Inherits="POP3" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
        <div>

            </div>
        <div>
             <p>
               服务器:
                <asp:TextBox ID="TextBox1" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
            <p>
               用户名:
                <asp:TextBox ID="TextBox2" runat="server" Height="16px" Width="256px"></asp:TextBox>
            </p>
            <p>
               密 码 :
                <asp:TextBox ID="TextBox3" runat="server" Height="16px" Width="256px"></asp:TextBox>
                 &nbsp;&nbsp;&nbsp;
                 <asp:Button ID="Button3" runat="server" OnClick="Button3_Click" Text="连接邮箱" Width="181px" />
                 &nbsp;
                 <asp:Button ID="Button4" runat="server" OnClick="Button4_Click" Text="退出邮箱" Width="181px" />
            </p>
             <p>
               操作:
               读取第几封邮件:
                <asp:TextBox ID="TextBox4" runat="server" Height="16px" Width="317px"></asp:TextBox>

                  &nbsp;&nbsp;&nbsp;

                  <asp:Button ID="Button2" runat="server" OnClick="Button2_Click" Text="读取邮件" Width="181px" />
            </p>
             <p>
                
                 <asp:TextBox ID="TextBox5" runat="server" Height="262px" Width="777px"></asp:TextBox>
                
            </p>
        </div>
    </form>
</body>
</html>

using System;
using System.Activities.Statements;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Sockets;
using System.ServiceModel.Security.Tokens;
using System.Text;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class POP3 : System.Web.UI.Page
{
    public static string pwd;
    public static int k;
    public static TcpClient tcpc;
    public static string popserver;
    public static StreamReader sr;
    public static string strRet;
    public static string[] arrRet;
    protected void Page_Load(object sender, EventArgs e)
    {

    }
    /// <summary>
    /// 连接邮箱 
    /// </summary>
    /// <param name="sender"></param>
    /// <param name="e"></param>
    protected void Button3_Click(object sender, EventArgs e)
    {
        string user=TextBox2.Text.ToString();
        string pass=TextBox3.Text.ToString();
        pwd = pass;
        popserver=TextBox1.Text.ToString();
        tcpc= new TcpClient();
        try
        {
            tcpc.Connect(popserver,110);
            sr=new StreamReader(tcpc.GetStream(),Encoding.Default);
            Response.Write(sr.ReadLine()+"<br>");//mail server的欢迎语
            strRet = Logon(tcpc,user,pass);
            if (JudgeString(strRet)!="+OK")
            {
                Response.Write("对不起,没有这个用户/密码 不匹配");
                return;
            }
            arrRet = StaticMailBox(tcpc);
            if (arrRet[0]!="+OK") {
                Response.Write("出错了!");
                return;
            }
            Response.Write("当前的用户<font color=blue>"+user+"</font>的信箱中共有<font color=red>" + arrRet[1]+"</font>封,共占<font color=red>" + arrRet[2]+"</font>Byte");
            k = Convert.ToInt32(arrRet[1]);
            Button2.Enabled= false;
            Button3.Enabled= true;
            Button4.Enabled= true;
            TextBox1.Enabled
                = true;
            TextBox2.Enabled = true;
            TextBox3.Enabled= true;
        }
        catch (Exception ex)
        {

           Response.Write("Could not connnect to Server!");
        }
    }
    /// <summary>
    /// 向POP3服务器发送各种·命令
    /// </summary>
    /// <param name="tcpc"></param>
    /// <param name="strCmd"></param>
    /// <returns></returns>
    private string SendPopCmd(TcpClient tcpc,string strCmd) {
        Byte[] arrcmd;
        string strRet;
        StreamReader sr;
        Stream s;
        s=tcpc.GetStream();
        strCmd = strCmd+"\r\n";
        arrcmd=Encoding.Default.GetBytes(strCmd.ToCharArray());
        s = tcpc.GetStream();
        s.Write(arrcmd,0,strCmd.Length);
        sr = new StreamReader(tcpc.GetStream(),Encoding.Default);
        strRet = sr.ReadLine();
        return strRet;

    }
    /// <summary>
    /// StaticMailBox方法用于获取指定的邮箱的当前状态信息,包括邮箱中邮件的数量,邮件的总大小信息.
    /// </summary>
    /// <param name="tcpc"></param>
    /// <returns></returns>
    /// <exception cref="NotImplementedException"></exception>
    private string[] StaticMailBox(TcpClient tcpc)
    {
        string strRet;
        strRet = SendPopCmd(tcpc,"stat");
        if (JudgeString(strRet)!="+OK")
        {
            return "-ERR -ERR".Split("".ToCharArray());
        }
        else
        {
            string[] arrRet = strRet.Split("".ToCharArray());
            return arrRet;
        }
    }
    /// <summary>
    /// 用于判断向服务器发送命令之后返回的字符开头是-ERR还是+OK,如果开头是-ERR,则返回-ERR,如果是+OK,则返回字符+OK.
    /// </summary>
    /// <param name="strRet"></param>
    /// <returns></returns>
    /// <exception cref="NotImplementedException"></exception>
    private string JudgeString(string strRet)
    {
        if (strRet.Substring(0,3)!="+OK")
        {
            return "-ERR";
        }
        else
        {
            return "+OK";
        }
    }
    /// <summary>
    /// 对登录用户身份进行验证,通过SendPopCmd方法向服务器发送"user+用户名"和"pass+密码"两条命令,并返回获取的信息.
    /// </summary>
    /// <param name="tcpc"></param>
    /// <param name="user"></param>
    /// <param name="pass"></param>
    /// <returns></returns>
    private string Logon(TcpClient tcpc, string user, string pass)
    {
        string strRet;
        strRet = SendPopCmd(tcpc,"user"+user);
        strRet= SendPopCmd(tcpc,"pass"+pass);
        return strRet;
    }
    /// <summary>
    /// 读取信件
    /// </summary>
    /// <param name="sender"></param>
    /// <param name="e"></param>
    protected void Button2_Click(object sender, EventArgs e)
    {
        if (Convert.ToInt32(TextBox4.Text)>k|| Convert.ToInt32(TextBox4.Text)<=0)
        {
            ClientScript.RegisterStartupScript(typeof(Page),"","<script>alert('输入的索引错误')</script>");
        }
        else
        {
            string[] arrRets;
            arrRets = PopMail(tcpc,Convert.ToInt32(TextBox4.Text));
            TextBox5.Text = $"当前是第{TextBox4.Text}封信\n邮件日期:{arrRets[1]}\n发信人:{arrRets[2]}\n收信人:{arrRets[3]}\n邮件主题:{arrRets[4]}\n邮件内容:{arrRets[5]}\n";
        }

    }
    /// <summary>
    /// 接收邮件的各个信息,其中i是要读取的邮件的索引数,通过参数i PopMail方法可以读取指定的邮件
    /// </summary>
    /// <param name="tcpc"></param>
    /// <param name="v"></param>
    /// <returns></returns>
    /// <exception cref="NotImplementedException"></exception>
    private string[] PopMail(TcpClient tcpc, int i)
    {
        string strRet;
        string[] arrRet = new string[20];
        bool strBody = false;
        string[] arrTemp;
        strRet = SendPopCmd(tcpc,"retr"+i.ToString());
        if (JudgeString(strRet)!="+OK")
        {
            return "-ERR -ERR".Split("".ToCharArray());
        }
        StreamReader sr;
        sr = new StreamReader(tcpc.GetStream(),Encoding.Default);
        Response.Write("\n<BR>");
        Response.Write("<font color=red>\n<BR>");
        arrRet[0] = "+OK";
        while (sr.Peek()!=46)
        {
            strRet = sr.ReadLine();
            arrTemp = strRet.Split(":".ToCharArray());
            if (strRet == "")
                strBody = true;//现在开始接收Body的信息
            if (arrTemp[0]=="Date")
                arrRet[1] = arrTemp[1];//信件的发送日期.
            if (arrTemp[0]=="From")
                arrRet[2] = (arrTemp[1].Substring(arrTemp[1].LastIndexOf("<")+1).Replace(">",""));//发信人
            if (arrTemp[0] == "To")
                arrRet[3] = (arrTemp[1].Substring(arrTemp[1].LastIndexOf("<") + 1).Replace(">", ""));//收信人
            if (arrTemp[0] == "Subject")
                arrRet[4] = arrTemp[1].ToString();//主题
            if (strBody)
                arrRet[5] = arrRet[5] + strRet + "\n";
        }
        return arrRet;
    }

}
```
秘笈心法
POP3协议接收电子邮件与Jmail组件接收电子邮件的功能对比
POP3协议是利用系统本身自带的功能来实现对电子邮件的接收,虽然使用POP3协议接收电子邮件相对简洁,但是其功能不是很完善,存在许多缺点,而利用Jmail组件接收电子邮件相对POP3协议显得更为简洁方便,而且功能相当强大,现在大多数的电子邮件收发系统都是通过Jmail组件来开发的.
2.2.高端应用:自定义组件应用.
043.自定义GridView数据绑定组件开发
实例说明
当GridView控件的数据源为空时,不显示表头,界面非常不美观.
关键技术
1.实现GridView控件未绑定数据源或绑定的数据为空时,使用Table标记静态设置表头信息.
2.当GridView控件绑定数据源中的数据为空时,给数据源插入一条空记录.
3.自定义GridView控件,在自定义控件内部实现当未绑定数据源或绑定的数据为空时也显示表头.
前两种方案的适应性不强,每次应用都要根据实际情况进行编码,而第三种方案只需将自定义的GridView拖动到应用处即可,所以本实例只介绍第三种方案,实现思路是继承ASP.NET自带的GridView控件,并重写Render方法,在Render方法中判断,当自定义GridView控件绑定的数据为空时根据GridView控件的列信息创建表头.
设计过程
1.EnableEmptyContentRender属性表示当自定义的GridView控件绑定的数据为空时是否显示表头.属性值设置为true时显示表头,false不显示表头,自定义GridView控件的属性代码如下:
``` bash
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

/// <summary>
/// GridView 的摘要说明
/// </summary>
public class GridView : System.Web.UI.WebControls.GridView
{
    [Category("Appearance")]
    [DefaultValue(true)]
    [Description("数据源为空时是否呈现表头")]
    [TypeConverter(typeof(BooleanConverter))]
    public bool EnableEmptyContentRender
    {
        get {
            if (ViewState["EnableEmptyContentRender"]==null)
            {
                return true;
            }
            return Convert.ToBoolean(ViewState["EnableEmptyContentRender"]);
           }
        set { ViewState["EnableEmptyContentRender"] = value; }
    }
    [Category("Appearance")]
    [DefaultValue("")]
    [Description("数据源为空时表格样式类")]
    public string EmptyDataTableCssClass {
        get {
            if (ViewState["EmptyDataTableCssClass"]==null)
            {
                return "";
            }
            return Convert.ToString(ViewState["EmptyDataTableCssClass"]);
        }
        set {
            ViewState["EmptyDataTableCssClass"] = value;
        }
    }

    [Category("Appearance")]
    [DefaultValue("")]
    [Description("数据源为空时标题行样式类")]
    public string EmptyDataTitleRowCssClass
    {
        get
        {
            if (ViewState["EmptyDataTitleRowCssClass"] == null)
            {
                return "";
            }
            return Convert.ToString(ViewState["EmptyDataTitleRowCssClass"]);
        }
        set
        {
            ViewState["EmptyDataTitleRowCssClass"] = value;
        }
    }
    [Category("Appearance")]
    [DefaultValue("")]
    [Description("数据源为空时内容行样式类")]
    public string EmptyDataContentRowCssClass
    {
        get
        {
            if (ViewState["EmptyDataContentRowCssClass"] == null)
            {
                return "没有可显示的数据记录.";
            }
            return Convert.ToString(ViewState["EmptyDataContentRowCssClass"]);
        }
        set
        {
            ViewState["EmptyDataContentRowCssClass"] = value;
        }
    }
    public GridView()
    {
        //
        // TODO: 在此处添加构造函数逻辑
        //
    }
    /// <summary>
    /// 重写Control类的Render方法
    /// </summary>
    /// <param name="writer"></param>
    protected override void Render(HtmlTextWriter writer)
    {
        //如果GridView数据为空
        if (EnableEmptyContentRender && (this.Rows.Count == 0) || this.Rows[0].RowType==System.Web.UI.WebControls.DataControlRowType.EmptyDataRow)
        {
            RenderEmptyContent(writer);//调用自定义呈现方法
        }
        else
        {
            base.Render(writer);//调用基类呈现方法
        }
       
    }
    /// <summary>
    /// GridView数据为空时呈现的样式
    /// 1.创建一个Table对象
    /// 2.根据GridView风格设置Table对象的样式属性,使Table对象的样式与GridView的样式保持一致
    /// 3.创建第一个TableRow对象,并设置TableRow对象的样式及高度.
    /// 4.遍历GridView控件的列,根据列信息创建TableRow对象的单元格TableCell,并设置单元格内显示的文本;
    /// 5.创建第二个TableRow对象,并设置TableRow的样式.
    /// 6.创建第二个TableRow对象的单元格,在单元格内显示当GridView数据为空时的提示信息(如“没有可显示的数据记录.”)
    /// 7.将创建的Table对象发送给HtmlTextWriter对象呈现.
    /// </summary>
    /// <param name="writer"></param>
    /// <exception cref="NotImplementedException"></exception>
    protected virtual void RenderEmptyContent(HtmlTextWriter writer)
    {
        Table t=new Table();    //创建一个Table对象.
        t.GridLines = this.GridLines;   //设置Table的线型与GridView相同
        t.BorderStyle = this.BorderStyle;   //设置边界风格与GridView相同
        t.BorderWidth = 0;
        t.CellPadding = 1;
        t.CellSpacing = 1;
        t.HorizontalAlign = this.HorizontalAlign;   //设置水平对齐风格与GridView相同
        t.Width= this.Width;//设置Table宽度与GridView相同.
        t.CopyBaseAttributes(this);
        t.BorderColor = this.BorderColor;   //设置边界颜色与GridView相同
        t.EnableTheming = this.EnableTheming;   //设置主题是否生效与GridView相同
        t.ForeColor=this.ForeColor; //设置前景色与GridView相同
        t.SkinID= this.SkinID;  //设置皮肤样式与GridView相同
        t.ToolTip = this.ToolTip;   //设置提示信息与GridView相同
        t.Visible=this.Visible;     //设置是否可见与GridView相同
        t.Font.CopyFrom(this.Font); //设置字体对象与GridView相同
        //设置Table的层叠样式表
        t.CssClass =this.EmptyDataTableCssClass!=""?this.EmptyDataTableCssClass:this.CssClass;
        TableRow row=new TableRow();    //创建Table行
        row.CssClass=this.EmptyDataTableCssClass;//设置行的层叠样式表
        row.Height = 25;
        t.Rows.Add(row);    //将行添加到Table对象中
        //根据GridView中的字段创建Table行的单元格
        foreach (DataControlField field in this.Columns)
        {
            if (field.Visible)
            {
                TableCell cell=new TableCell();//创建单元格
                cell.Text = field.HeaderText;//设置单元格文本
                row.Cells.Add(cell);        //将单元格添加到行中
            }
        }
        TableRow row2=new TableRow();   //新建第二行
        row2.CssClass = this.EmptyDataContentRowCssClass;   //设置行的层叠样式表
        t.Rows.Add(row2);
        TableCell msgCell=new TableCell();
        if (this.EmptyDataTemplate!=null)
        {
            this.EmptyDataTemplate.InstantiateIn(msgCell);
        }
        else
        {
            msgCell.Text=this.EmptyDataText;    //设置空数据提示信息
        }
        msgCell.HorizontalAlign = HorizontalAlign.Center;//设置单元格的水平对齐
        msgCell.ColumnSpan = this.Columns.Count;//设置单元格的列合并
        row2.Cells.Add(msgCell);
        t.RenderControl(writer);    //将Table发送给HTML呈现流



    }
}
```
秘笈心法
如何实现单击表格行打开另一页,并传递参数
``` bash
protected void gvGoodsInfo_RowDataBound(object sender, GridViewRowEventArgs e)
    {
        if (e.Row.RowType==DataControlRowType.DataRow)
        {
            e.Row.Attributes.Add("onclick", "window.open('Default.aspx?title=" + e.Row.Cells[1].Text +"');");
        }
    }
```
044.自定义OurPager分页组件开发
关键技术
1.自定义分页控件OurPager使用说明
分页控件只需设置两个属性,即RecordCount(记录总数)和PageSize(页尺寸)即可自动计算分页的页数并绘制控件呈现
以下是在用户管理页的Page_Load事件中设置分页控件属性的代码,首先调用SelectUserInfo方法获得数据源记录总数(第一个参数为-1时,返回列表中的第一行,第一列存储的是数据源的记录总数),其次设置分页控件的RecordCount属性,最后调用自定义方法LoadData加载GridView数据.
``` bash
protected void Page_Load(object sender,EventArgs e){
	List<UserInfo> cntList=new List<UserInfo>();
	cntList=UserInfoService.Instance.SelectUserInfo(-1,OurPager1.PageSize);	//分页控件默认页尺寸为10
	OurPager1.RecordCount=cntList[0].UserID;//取总记录数
	if(!this.IsPostBack){
	LoadData(OurPager1.CurrentPageIndex);	//当前页索引默认为0
}
}
```
提示:由于自定义分页控件的PageSize(页尺寸)默认为10,所以当设置RecordCount属性时分页控件就开始自动计算分页的页数并绘制控件呈现.
分页控件还提供了一个PageChanged事件,如果已实现该事件,单击分页控件任意按钮时触发该事件,开发者只需在这个事件上编写返回指定页数据的代码即可.
在用户管理页(UserList.aspx)中,自定义分页控件的PageChanged事件代码如下:
``` bash
protected void OurPager1_PageChanged(object sender,ServerControl.PageArgs e){
	LoadData(e.NewPageIndex);
}
```
在PageChanged事件中,首先从参数对象e的NewPageIndex属性中取出当前页索引,其次调用自定义方法LoadData加载GridView当前页的数据
2.使用LINQ实现分页功能
本实例中使用LINQ实现返回指定页数据的功能,主要代码如下:
SelectUserInfo().Skip((CurrentPageIndex-1)*PageSize).Take(PageSize).ToList();
上述代码中,SelectUserInfo表示返回所有的用户信息:Skip((CurrentPageIndex-1)*PageSize)表示跳过当前页之前的所有记录;Take(PageSize)表示取指定数量的记录:ToList表示将取到的数据转换成List<T>类型.
3.自定义控件的应用范围
本实例采用开发自定义分页控件的方式实现分页方案,如下情况可以选择开发自定义控件:
	需要将两个或多个内置的Web控件组合在一起.
	需要扩展内置控件的功能时
	需要开发与现在任何控件完全不同的控件时
4.CompositeControl类
如果要开发自定义组合控件,建议将CompositeControl类作为自定义组合控件的超类(基类),与其他创建组合控件的方式相比,其最大的优点是在控件呈现内容的方法中看不到任何HTML,只需重写CreateChildControls方法,在该方法中创建所需要的子控件,并将创建的子控件添加到自定义组合控件的Controls集合中.
所有自定义组合控件的子控件触发的事件都会传递到事件冒泡方法OnBubbleEvent中,只需要设置各个子控件的命令名称,在重写OnBubbleEvent方法根据命令名称判断是哪个子控件触发的事件,然后做出相应的处理.
下面简单介绍自定义组合控件的代码执行顺序:先执行创建子控件方法CreateChildControls,然后执行呈现内容方法RenderContents将子控件呈现在组合控件上,最后才执行事件冒泡方法OnBubbleEvent,冒泡方法执行的代码涉及重新刷新组合控件内容时调用的基类的RecreateChildControls方法再重新创建一遍子控件即可.
5.属性(Attribute)
C#允许在类和成员上声明特性,这个特性也是一个类,可在运行时解释类和类成员的行为.这个特性也称为属性Attribute.
允许在其上声明属性Attribute的类型有:
类
类成员:包括常量,字段,方法,属性,事件,索引器,运算符重载,构造函数和析构函数
结构
接口
接口成员:包括方法,属性,事件和索引器
枚举和枚举成员
委托
在代码中指定属性时,将属性名称放在方括号中,简单属性指定如下所示:
[MyAttribute]
属性Attribute可分为.Net框架提供的属性和自定义属性:.Net框架提供了数百个预先定义好的,内置的属性，这些属性都是从System.Attribute类派生而来,并且这些属性与特定的对象一起使用.例如,System.SerializableAttribute属性(别名是Serializable),应用该属性(Attribute)的类或类成员可将其内容串行到某个存储介质,例如,将对象的内容序列化成XML,需要在该对象的类上应用Seralizable属性.
设计过程
1.创建自定义分页控件
创建自定义分页控件的类,类名为OurPager,OurPager类继承自CompositeControl类.代码如下:
[DefaultProperty("RecordCount")]
[ToolboxData("<{0}:Ourpager runat=server></{0}:OurPager>")]
public class OurPager:CompositeControl{}
在OurPager类声明的上面设置了两个属性(Attribute),DefaultProperty属性表示OurPager类的默认属性是RecordCount,ToolboxData属性表示将自定义分页控件从工具箱拖到设计页面时生成的默认标记.在用户管理页中,自定义分页控件的默认标记为:
<cc1:OurPager ID="OurPager1" runat="server" onpagechanged="OurPager1_PageChanged"/>
2.声明自定义分页控件的属性
``` bash
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

/// <summary>
/// OurPager 的摘要说明
/// </summary>
/// 
[DefaultProperty("RecordCount")]
[ToolboxData("<{0}:OurPager runat=server></{0}:OurPager>")]
public class OurPager:CompositeControl
{
    public OurPager()
    {
        //
        // TODO: 在此处添加构造函数逻辑
        //
    }
    /// <summary>
    /// 页数
    /// </summary>
    private int PageCount { set; get; }
    [Category("Behavior")]
    [DefaultValue(0)]
    [Description("数据源中符合条件的记录数")]
    [TypeConverter(typeof(Int32Converter))]
    public int RecordCount { get; set; }
    [Category("Behavior")]
    [DefaultValue(10)]
    [Description("页尺寸/每页记录数")]
    [TypeConverter(typeof(Int32Converter))]
    public int PageSize { set; get; }

    [Category("Behavior")]
    [DefaultValue(0)]
    [Description("当前页索引")]
    [TypeConverter(typeof(Int32Converter))]
    public int CurrentPageIndex { set; get; }

    private LinkButton _btnFirst;
    private Label _lblPage = new Label();
    private TextBox _txtToPage=new TextBox();
    /// <summary>
    /// 计算页数
    /// </summary>
    /// <param name="recordCount"></param>
    /// <param name="pageSize"></param>
    private void CalculatePageCount(int recordCount, int pageSize) {
        if (pageSize==0)
        {
            return;
        }
        int pagCnt = 0;
        pagCnt = recordCount / pageSize;    //用记录总数整除页尺寸
        if (recordCount%pageSize>0)
        {
            pagCnt++;                       //页数加1
        }
        PageCount = pagCnt;                 //设置页数属性
        RecreateChildControls();            //重新绘制控件
    }
    [Category("Action")]
    public event PageChangedHandler PageChanged;
    /// <summary>
    /// 页改变事件委托
    /// </summary>
    /// <param name="sender"></param>
    /// <param name="e"></param>
    public delegate void PageChangedHandler(object sender,PageArgs e);
    /// <summary>
    /// 实现创建子控件
    /// </summary>
    protected override void CreateChildControls() {
        int recCnt = RecordCount;
        int curPageIdx = CurrentPageIndex;
        int pageCnt = PageCount;
        int pagSiz = PageSize;
        Controls.Clear();               //清空子控件
        //5.实现PageChanged事件 在运行时,单击分页控件上的任意按钮(子控件),都会将子控件触发的事件传递到事件冒泡方法OnBubbleEvent中,在CreateChildControls方法中创建子控件对象时已经设置了子控件的命令名称.设置子控件命令名称代码如下:
        _btnFirst = new LinkButton
        {
            Text = "首页",
            CommandName=((int)PageChangedType.atFirst).ToString(),  //设置子控件的命令名称
            CommandArgument=""
        };


        Controls.Add(_btnFirst);            //将首页按钮添加到分页控件
        ChildControlsCreated = true;        //设置子控件已创建
        CalculateButtonEnable();            //计算分页按钮可见
        CalculateNumericBtnVisible();       //计算页码翻页按钮可见
    }
    /// <summary>
    /// 所以在分页控件中只需重写基类的OnBubbleEvent方法,在该方法中根据命令判断是哪个子控件触发的事件然后做相应的处理.
    /// 首先判断触发的是不是命令事件,其次从参数中取出子控件类型枚举PageChangedType和命令名称cmdArg;最后调用自定义方法DoPageChanged,由该方法实现事件的详细执行过程
    /// 子控件在创建时都制定了CommandName属性,所以子控件触发的应该是命令事件.
    /// </summary>
    /// <param name="source"></param>
    /// <param name="args"></param>
    /// <returns></returns>
    protected override bool OnBubbleEvent(object source, EventArgs args)
    {
        if (args is CommandEventArgs)       //是命令事件
        {
            //取出单击哪类子控件触发的事件
            PageChangedType cmdName=(PageChangedType)Convert.ToInt32(((CommandEventArgs)args).CommandName);
            string cmdArg = ((CommandEventArgs)args).CommandArgument.ToString();
            DoPageChanged(cmdName, cmdArg);     //执行处理自定义分页的过程
            return true;
        }
        return base.OnBubbleEvent(source, args);    //执行父类的冒泡方法
    }
    /// <summary>
    /// 在自定义DoPageChanged中,如果判断是单击"首页"按钮触发的事件,将当前页索引(存储在NewPageIndex变量中)设置为1;其次如果分页控件已实现PageChanged事件,那么执行PageChanged事件.
    /// </summary>
    /// <param name="cmdName"></param>
    /// <param name="cmdArg"></param>
    private void DoPageChanged(PageChangedType ty, string cmdArg)
    {
      int currentPageIdx=CurrentPageIndex;
        int pageCnt=PageCount;
        int NewPageIndex = CurrentPageIndex;        //设置新页索引
        switch (ty)
        {
            case PageChangedType.atFirst:
                NewPageIndex = 1;                   //新页索引设为1
                break;
                case PageChangedType.atLast:
                NewPageIndex = PageCount;
                break;

            default:
                break;
        }
        if (PageChanged!=null)
        {
            PageArgs args = new PageArgs(NewPageIndex);     //设置页改变事件参数
            PageChanged(this, args);                        //执行页改变事件代码

        }
        CurrentPageIndex= NewPageIndex;                     //最终确认当前页索引
        CalculateButtonEnable();
        CalculateNumericBtnVisible();
        _lblPage.Text = string.Format("第{0}页/第{1}页",NewPageIndex,pageCnt);
        _txtToPage.Text = NewPageIndex.ToString();
        //如果是向前或向后翻页码则要重新创建子控件
        //秘笈心法:强制刷新自定义分页控件
        //涉及重新刷新组合控件内容,调用基类的RecreateChildControls方法再重新创建一遍子控件
        if (ty==PageChangedType.atNumeric && (cmdArg=="back"||cmdArg=="front"))
        {
            RecreateChildControls();
        }

    }

    /// <summary>
    /// 4.实现呈现内容的方法
    /// </summary>
    /// <param name="writer"></param>
    protected override void RenderContents(HtmlTextWriter writer) {
        EnsureChildControls();          //确认子控件是否被创建
        writer.RenderBeginTag(HtmlTextWriterTag.Tr);    //写行的开始标记<tr>
        //...
        writer.RenderBeginTag(HtmlTextWriterTag.Td);    //写单元格开始标记<td>
        _btnFirst.RenderControl(writer);                //将首页按钮呈现在分页控件上
        writer.RenderEndTag();                          //写单元格结束标记</td>
        //...
        writer.RenderEndTag();                          //写行的结束标记
        
    }
    /// <summary>
    /// 说明:在自定义分页控件中,重写了基类的TagKey属性,将TagKey属性值指定为HtmlTextWriterTag.Table,表示在RenderContents方法中按Table格式呈现子控件
    /// </summary>
    protected override HtmlTextWriterTag TagKey => HtmlTextWriterTag.Table;
    /// <summary>
    /// 
    /// </summary>
    /// <exception cref="NotImplementedException"></exception>
    private void CalculateNumericBtnVisible()
    {
        throw new NotImplementedException();
    }
    /// <summary>
    /// 
    /// </summary>
    /// <exception cref="NotImplementedException"></exception>
    private void CalculateButtonEnable()
    {
        throw new NotImplementedException();
    }
}
```
2.3.超级网购:网上购物与银行在线支付
045.购物商城网创建个人店铺
关键技术
在创建个人店铺前判断注册用户是否已经创建了个人店铺,如果没有创建,登陆后将会跳转到创建个人店铺页CreatePersonShop.aspx;如果已经创建,则会跳转到个人店铺管理页Shop_Manage.aspx.
``` bash
  public void CheckIfExist(string username,string password) {
        //创建数据库连接对象
        SqlConnection myconn = new SqlConnection(ConfigurationManager.AppSettings["ConStr"]);
        //定义一个字符串型的SQL语句
        string strsql = $"select * from Users where UserName='{username}' and Password='{password}'";
        //声明所创建的数据库连接类conn
        //DBConnection conn = new DBConnection();
        SqlCommand sqlCommand=new SqlCommand(strsql, myconn);

        SqlDataReader dr = sqlCommand.ExecuteReader();
        if (dr.Read())
        {
            //定义一个Session变量
            Session["username"] = username;
            //定义一个查询操作的SQL语句.
            string sql = $"select count(*) from DianPudetail where DianPuUser='{username}'";
            SqlCommand mydb=new SqlCommand(sql, myconn);
            ///如果已经创建个人店铺,则跳转到个人店铺管理页
            if (mydb.ExecuteScalar()!=null)
            {
                Response.Redirect("Shop_Mange.aspx");
            }//如果没有创建个人店铺,则跳转到创建个人店铺页
            else
            {
                Response.Redirect("CreatPresonShop.aspx");
            }

        }
    }
```
在实现上传个人店铺店标时,将上传的店标(图片)以二进制形式存储到数据库中(主要应用到了HttpPostedFile类的InputStream属性,Stream类的Read方法和Byte数据类型).
设计过程
``` bash
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Data;
using System.Data.SqlClient;
using System.IO;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class NetShop : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {

    }
    public void CheckIfFirst() {
        if (!IsPostBack)
        {
            //定义查询店铺类型信息的Sql语句
            string strsql = "select * from PType where parentId=0";
            //创建命令对象
            SqlConnection myconn = new SqlConnection(ConfigurationManager.AppSettings["ConStr"]);
            SqlCommand mycomd=new SqlCommand(strsql,myconn);
            //打开数据库连接
           myconn.Open();
            //创建数据阅读器
            SqlDataReader mydr=mycomd.ExecuteReader();
            Select1.DataSource= mydr;
            Select1.DataTextField = "category_name";
            Select1.DataValueField = "category_ID";
            Select1.DataBind();//从数据库绑定数据信息
            mydr.Close();//关闭数据阅读器
            myconn.Close();//关闭数据库连接

        }
    }
    /// <summary>
    /// 提交
    /// </summary>
    /// <param name="sender"></param>
    /// <param name="e"></param>
    protected void Button1_Click(object sender, EventArgs e)
    {
        string shopName = TextBox1.Text;//店铺名称
        string shopClass = Select1.SelectedValue;//店铺类别
        string shopItem = TextBox3.Text;//店铺主营项目
        string shopJieShao=JieShao.Text;//店铺简介
        if (shopJieShao==null)
        {
            shopJieShao = "";//店铺公告
        }
        string strsql = $"select * from DianPuDetail where DianPuName='{shopName}'";//定义一个查询店铺信息的操作
        SqlConnection myconn = new SqlConnection(ConfigurationManager.AppSettings["ConStr"]);
        SqlCommand command = new SqlCommand(strsql,myconn);
        myconn.Open();
        SqlDataReader dr=command.ExecuteReader();
        string imgPath =uploadFile.PostedFile.FileName;//获取上传图片路径
        string imgName=imgPath.Substring(imgPath.LastIndexOf("\\")+ 1);//获取上传文件名称
        string imgExtend = imgPath.Substring(imgPath.LastIndexOf(".")+1);//获取上传文件扩展名
        Image imageupload = null;//判断上传路径是否为空
        if (uploadFile.PostedFile.FileName!="")//判断上传路径是否为空
        {
            if (dr.Read())//判断数据库中是否已存在创建的店铺
            {
                Response.Write("<script>alert('抱歉,此店名已经存在!');location='javascript:history.go(-1)'</script>");
            }
            //判断上传图片的格式
            else if(uploadFile.PostedFile.FileName!="" && (Path.GetExtension(uploadFile.PostedFile.FileName)!=".gif")&& Path.GetExtension(uploadFile.PostedFile.FileName)!=".jpg")
            {
                Response.Write("<script>alert('上传的图片格式必须为gif或jpg!')</script>");
            }
            else
            {
                dr.Close();//关闭数据阅读器
                //获取登录用户名存储在定义的字符串类型的变量username
                string username = Session["username"].ToString();
                //定义插入的SQL字符串
                string sqlstr = $"insert into DianPuDetail(DianPuName,DianPuUser,DainPuClass,DianPuItem,DianPuJieshao)values('{shopName}','{username}','{shopClass}','{shopItem}','{shopJieShao}')";
                SqlCommand mycomm= new SqlCommand(sqlstr,myconn);
                if (mycomm.Connection.State!=System.Data.ConnectionState.Open)
                {
                    myconn.Open();
                }
                mycomm.ExecuteNonQuery();
                ///以下代码实现将上传的图片以二进制的形式存储到数据库中
                if (uploadFile.PostedFile.FileName!="")
                {
                    Stream imagestream;//创建数据流对象
                    int imageLength = this.uploadFile.PostedFile.ContentLength;//获取上传图片的长度
                    Byte[] image = new byte[imageLength];//定义Byte数组
                    HttpPostedFile hp = uploadFile.PostedFile;
                    imagestream = hp.InputStream;
                    //将图片数据放在image对象实例中,其中0代表数组指针的起始位置,imagelength表示要读取流的长度(指针的结束位置)
                    imagestream.Read(image,0,imageLength);
                    //定义更新数据库中存储的二进制图片
                    string sqlstr0 = $"update [DianPuDetail] set [DianPuLogo]=@imgdata where DianPuId=(select top 1 DianPuId from [DianPuDetail] where DianPuUser='{username}' order by DianPuId desc)"; 
                    //将图片存储到数据库指定字段中
                    SqlCommand comd=new SqlCommand(sqlstr0,myconn);
                    comd.Parameters.Add("@imagedata",SqlDbType.Image);
                    comd.Parameters["@imagedata"].Value = image;
                    comd.ExecuteReader();
                    myconn.Close();//关闭数据库连接
                }
                Response.Write("<script language='javaScript'>alert('恭喜!!!注册成功!!!');location='Shop_Manage.aspx'</script>");
            }
        }
        else
        {
            Response.Write("<script>alert('上传图片不能为空!');location='javascript:history.go(-1)'</script>");
        }

        
    }

    protected void Button2_Click(object sender, EventArgs e)
    {

    }
}
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="NetShop.aspx.cs" Inherits="NetShop" %>
<%@ Register Assembly="FreeTextBox" Namespace="FreeTextBoxControls" TagPrefix="ftb" %>
<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
        你的店名:<asp:TextBox ID="TextBox1" runat="server" Width="252px"></asp:TextBox>
        <br />
         你的店标:<asp:FileUpload ID="uploadFile" runat="server" onpropertychange="document.all.img1.src=this.value"/>

        <br />
&nbsp;&nbsp;&nbsp;&nbsp;
        <asp:Image ID="img1" runat="server" Width="150px" />

        <br />
        店铺类型:<asp:DropDownList ID="Select1" runat="server" Width="252px">
        </asp:DropDownList>
        <br />
        主营项目:<asp:TextBox ID="TextBox3" runat="server" Width="252px"></asp:TextBox>

        <br />
        <ftb:FreeTextBox runat="server" ID="JieShao"></ftb:FreeTextBox>
        <asp:Button ID="Button1" runat="server" Text="提交" Width="150px" OnClick="Button1_Click" />
&nbsp;&nbsp;&nbsp;
        <asp:Button ID="Button2" runat="server" Text="重置" Width="150px" OnClick="Button2_Click" />
    </form>
</body>
</html>

```
秘笈心法
购物商城网创建个人店铺的设计思路
在创建个人店铺前首先需要注册为商铺会员,在登录窗口登录成功后将跳转到创建个人店铺页面CreatePersonShop.aspx,店铺创建成功后,将跳转到个人商铺管理页,在该页中用户可以链接到相关商铺管理页面,如店铺的"我要卖"页面,店铺公告信息发布等.当成为该店铺的会员后再次登录将会直接跳转到个人商铺管理页面.
046.网上商城购物车
实例说明
购物浏览
商品信息查看
继续购物,结账,清空购物车等
后台管理
商品浏览页面(Default.aspx)也是该购物车实例的起始页,主要用来实现用户登录及商品显示的功能,在该页中用户可以浏览商品信息,如果用户的身份为已登录的用户,还可以将物品放入购物车内
购物车页面(ShoppingCart.aspx)主要将用户选择的商品添加到购物车内,用户可以增加某件购买商品的数量,删除某件商品,继续购物,清空购物车或结账等.
后台管理页面(GoodsInfo.aspx)主要添加商品信息,该页中的每项内容都必须填写.管理员添加商品照片时,会将本地图片上传到服务器中,并显示在Image控件上.
关键技术
验证DataList控件中的TextBox控件允许输入数字和计算购物车中账户余额
1.验证DataList控件中的TextBox控件允许输入数字
可以通过CompareValidator控件进行验证,也可以通过该控件的Attribute属性设置,当用户松开按键时进行验证,如果不为数字则取消操作.
在ShoppingCart.aspx页面的Page_Load事件中,首先定义一个正则表达式使购物车中的更改商品数量的TextBox控件中能输入数字
``` bash
protected void Page_Load(object sender,EventArgs e){
	//应用正则表达式屏蔽用户输入非数字字符
	TextBox1.Attributes["onkeyup"]="value=value.replace(/[^\\d]/g,")";
}
```
定义完用于更改商品数量的TextBox控件只能输入数字后,如果该TextBox控件在DataList控件的ItemTemplate模板中,那么就需要对当前项被数据绑定到DataList控件时引发的ItemDataBound事件进行设置,首先要在DataList控件中找到该控件,