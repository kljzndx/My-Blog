---
layout: post
title: 常见数据格式文件简介 xml, yaml, json
categories: 简介
tags: [数据格式]
---
   
* content
{:toc}    

# 前言

在此文章中我会通过编写一个用户列表模型的示例来讲解各个语法

# xml

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<users length="3">
  <!-- 张三 -->
  <user>
    <name>张三</name>
    <age>18</age>
    <gender>男</gender>
    <hobbies>
      <hobby>足球</hobby>
      <hobby>篮球</hobby>
    </hobbies>
    <password encrypt="rsa">abc123</password>
  </user>
  <!-- 李四 -->
  <user>
    <name>李四</name>
    <age>18</age>
    <gender>男</gender>
    <hobbies>
      <hobby>足球</hobby>
      <hobby>篮球</hobby>
    </hobbies>
    <password encrypt="rsa">abc123</password>
  </user>
  <!-- 王五 -->
  <user>
    <name>王五</name>
    <age>18</age>
    <gender>男</gender>
    <hobbies>
      <hobby>足球</hobby>
      <hobby>篮球</hobby>
    </hobbies>
    <password encrypt="rsa">abc123</password>
  </user>
</users>
```

从上面的示例可以看出，他是由一行声明和一个根标签构成的，一个xml文件必须有且只能有一个根标签

开头的一行是xml文件声明，里面可以指定版本和编码，不过这个版本只是用来定义文件的版本的，而不是语言的版本。  
例如哪天我要修改一下用户模型，我就可以改这个版本了，如1.1 2.0等

``` xml
<?xml version="1.0" encoding="utf-8" ?>
```

xml声明的下面就是根标签，在继续说下去之前我们先来聊聊 标签(tag) 是什么。  
xml标签有两种类型，在详细说明之前先来看个示例

``` xml
<tag-name> content </tag-name>
<tag-name />
```

第一种叫 **“开闭标签”** 分别由 开标签(<tag-name>) 和 闭标签(</tag-name>) 组成，在两个标签之间就是被标记的内容。  
第二种叫 **“自闭标签”** 很明显，它是个自动关闭的标签，也正因为他是自闭标签，所以它不能够用来标记内容，但是它可以有 **属性(Attribute)**。  
说个题外话，我其实更喜欢把他叫做特性而非属性，因为在c#里它就是这么翻译的，但是在xml中它确实充当着property的角色

既然说到了 **属性(Attribute)**，那么就来看看属性的语法。

``` xml
<tag-name attribute1="value" attribute2="value" attribute3="value"> content </tag-name>
<tag-name attribute1="value" attribute2="value" attribute3="value" />
```

可以看到，属性就是一个键值对(key-value-pair)，下面说下几个语法点。

* 属性的value始终需要写在双引号内，不论是什么类型（如 数字，字符串，布尔(true / false) 亦或其他类型）
* 多个属性之间用空格分隔
* 属性不能写在闭标签中

说完了属性，顺便说一个细节，在绝大多数关于xml的api或库中都是没有 标签(tag) 的概念的，取而代之的是 **节点(Node)**，因为这个词汇更加具体，不会产生混淆。

接着再说一个其他两个数据格式(yml, json) 不具备的特点，他不是纯结构化语言，什么意思呢，就是标签的内容可以混合多种类型，具体来说就是你可以在一段内容中间插入标签，示例如下

``` xml
<p style=“color: skyblue;”> 这是<i style=“color: pink;”>倾斜文字</i>，这是<b style=“color: green;”>粗体文字</b>，完毕。 </p>
```

> 提示：将上面代码保存成html并且用浏览器打开他，你就能看到效果了

最后再说一下注释的语法

``` xml
<!-- 注释内容 -->
```

# yaml (.yml .yaml)

YAML = Yaml Ain't a Markup Language

``` yaml
---
length: 3
list: 
  ZhangSan:    # 张三
    name: 张三
    age: 18
    gender: 男
    hobbies: 
    - 足球
    - 乒乓球
    password: 
      encrypt: rsa
      content: abc123
  LiSi:        # 李四
    name: 李四
    age: 18
    gender: 男
    hobbies: 
    - 足球
    - 乒乓球
    password: 
      encrypt: rsa
      content: abc123
  WangWu:      # 王五
    name: 王五
    age: 18
    gender: 男
    hobbies: 
    - 足球
    - 乒乓球
    password: 
      encrypt: rsa
      content: abc123
...
```

yaml的语法有很多，在这里我只介绍最常用的语法

首先yaml是有开始符和结束符的，开始符是三条杠 `---` ，结束符是三个点 `...` ，在两者之内的就是 yaml 的内容了，不过这两个符号写不写都无所谓，他的作用就是让你可以在外面写一些东西

yaml 使用缩进来表示层级关系，但是这里有个特别坑的规则是：**只能用空格来表示缩进**，而且缩进的空格数并不重要，只要相同层级的元素左侧对齐即可

从上面的示例可以看出他是以键值对做为基本结构的，然而这里有一点需要注意下，就是**冒号后面必须要有一个空格**

接下来看看他的数据类型

* 字面量。 如数字，字符串，布尔值(true / false)，null
* 数组，也叫 集合 或 序列
* 对象。包含键值对的集合，也叫字典

关于字面量我认为只需说两点就够了

1. 在yaml中字符串一般情况下是不需要用引号包裹的，仅在包含特殊字符时才需要用引号包裹，单双引号都可以用
2. 在yaml中 null 的表示方法除了字面量之外还有一种，就是一个波浪号 `~`

接着来看看数组，先来看个示例，然后再看语法

``` yaml
array: 
- item1
- item2
- item3
```

从上面的示例可以看出两个语法点

1. 每个项目都要独占一行
2. 每行都要以 横杠+空格 开头

再来说说对象，yaml的对象其实没有表现形式，或者说他的表现形式就是键值对，具体来说就是：**所有同级键值对都会自成一个对象**。  
顺便说一句，一个yaml文件必须要有一个对象（或者说一个键值对）

最后再来看看注释的语法

``` yaml
# 注释内容
key: value    # 注释内容
```

语法很简单，就是从井号开始一直到行尾都算是注释内容，还有要说明的一点就是：**yaml 不支持多行注释**

# json

``` json
{
  "length": 3,
  "list": [
    {
      "name": "张三",
      "age": 18,
      "gender": "男",
      "hobbies": [ "足球", "乒乓球" ],
      "password": {
        "content": "123abc",
        "encrypt": "rsa"
      }
    },
    {
      "name": "李四",
      "age": 18,
      "gender": "男",
      "hobbies": [ "足球", "乒乓球" ],
      "password": {
        "content": "123abc",
        "encrypt": "rsa"
      }
    },
    {
      "name": "王五",
      "age": 18,
      "gender": "男",
      "hobbies": [ "足球", "乒乓球" ],
      "password": {
        "content": "123abc",
        "encrypt": "rsa"
      }
    }
  ]
}
```

从示例可以看出他的语法跟yaml差不多，最大的不同就是他的层级关系是用括号来表示的，**其中花括号代表的是一个json对象，而方括号代表的是json数组。**  
一个json文件只能包含一个对象或数组

先来看看属性的语法

``` json
{
  "property1": "value",
  "property2": 123
}
```

可以看到写法跟yaml差不多，只是稍微复杂点而已，具体区别如下

* 属性只能存在于对象中（对象就是一对花括号），或者说对象里只能包含属性
* 属性的key要用引号包裹
* 多个属性用逗号分隔

再来看看数组的语法

``` json
{
  "strArray": ["item1", "item2", "item3"],
  "numArray": [1, 2, 3],
  "objArray": [{"a": 1, "b": 2}， {"a": 1, "b": 2}，{"a": 1, "b": 2}]
}
```

可以看到数组就是一对方括号，多个项目用逗号分隔，值得一提的是项目也可以是个对象，这是yaml做不到的

有关字面量相关内容，请去看yaml的字面量

最后提醒一句，**json是不支持注释的**