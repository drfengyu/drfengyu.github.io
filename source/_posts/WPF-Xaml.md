---
title: WPF-Xaml
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
abbrlink: 2abf7006
date: 2022-11-23 09:16:15
pic:
---

{% blockquote David Levithan，Wide Awake %}
Do not just seek happiness for yourself.Seek happiness for all.Through kindness.Through mercy.
{% endblockquote %}

### 附加属性
附加属性是可以用于多个控件,但是在另一个类中定义的属性.附加属性的命名方式是"定义类型.属性".这样可以让Xaml解析器将其与普通属性区分开.附加属性的设置可以使用Attribute和Property-Element语法.使用后者时类型必须是包含该属性的类型.
### Content属性
### 类型转换器
所有的类型转换器都派生自TypeConverter,TypeConverter提供的4个重要方法是CanConvertTo,CanConvertFrom,ConvertTo和ConvertFrom.
ConvertFrom方法将Xaml中的字符串转换为相应的CLR对象.ConvertTo方法将CLR对象转换为相应的字符串.CanConvertFrom用来检查能否从字符串转换为相应的CLR对象.CanConvertTo检查ClR对象能否转换为相应的字符串.可以true.
#### 自定义类型转换器
``` bash
    [TypeConverter(typeof(MoneyConverter))]
    public class MoneyType
    {
        private string _value;

        public MoneyType()
        {
            _value = "0";
        }

        public MoneyType(string value)
        {
            _value = value;
        }
        public override string ToString()
        {
            return _value.ToString();
        }
        public static MoneyType Parse(string value) {
            string str = (value as string).Trim();
            if (str[0] == '$') {
                string newprice = str.Remove(0,1);
                double price = double.Parse(newprice);
                return new MoneyType("￥" + (price * 7.24).ToString("0.00"));
            }
            else if (str[0] == '￥')
            {
                string newprice = str.Remove(0, 1);
                double price = double.Parse(newprice);
                return new MoneyType("$" + (price / 7.24).ToString("0.00"));
            }else
            {
                double price = double.Parse(str);
                return new MoneyType(""+price);
            }
        }
    }

    public class MoneyConverter : TypeConverter
    {
        /// <summary>
        /// 对象转换为Xaml字符串是否
        /// </summary>
        /// <param name="context"></param>
        /// <param name="sourceType"></param>
        /// <returns></returns>
        public override bool CanConvertFrom(ITypeDescriptorContext context, Type sourceType)
        {
            if(sourceType==typeof(string))
                return true;
            return base.CanConvertFrom(context, sourceType);
        }
        /// <summary>
        /// Xaml上下文转换为对应类型
        /// </summary>
        /// <param name="context"></param>
        /// <param name="destinationType"></param>
        /// <returns></returns>
        public override bool CanConvertTo(ITypeDescriptorContext context, Type destinationType)
        {
            if (destinationType == typeof(string))
                return true;
            return base.CanConvertTo(context, destinationType);
        }
        /// <summary>
        /// 将Xaml上下文转换为对象
        /// </summary>
        /// <param name="context"></param>
        /// <param name="culture"></param>
        /// <param name="value"></param>
        /// <returns></returns>
        public override object ConvertFrom(ITypeDescriptorContext context, CultureInfo culture, object value)
        {
            if (value.GetType()!=typeof(string))
            {
                return base.ConvertFrom(context, culture, value);
            }
            return MoneyType.Parse((string)value);
        }
        /// <summary>
        /// 将对象转为Xaml上下文
        /// </summary>
        /// <param name="context"></param>
        /// <param name="culture"></param>
        /// <param name="value"></param>
        /// <param name="destinationType"></param>
        /// <returns></returns>
        public override object ConvertTo(ITypeDescriptorContext context, CultureInfo culture, object value, Type destinationType)
        {
            if(destinationType==typeof(string))
            return base.ConvertTo(context, culture, value, destinationType);
            return value.ToString();
        }
    }

```
### 标记扩展
{x:null}表示一个空值 {x:static}代表引用一个类的静态变量 {}{HelloXaml}表示普通字符串







