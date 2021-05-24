---
layout: post
title: v2ray 路由规则笔记
categories: v2ray
tags: [路由规则]
---

* content
{:toc}

# 前言

最近找到了一些能用的免费的v2节点，然而v2ray的gui版本————v2rayN 从v4版开始就没有pac模式了，取而代之的是路由功能，我用了之后发现还蛮好用的，当然前提是你得把这玩意折腾明白了才行

这里顺便吐槽下v2rayN的路由编辑器是真tm难用，尤其是对着列表项右击居然没有“编辑此项”的菜单，鼠标放在项目上光标也没变化，搞得我以为这玩意是个没开发完的半成品呢，可细想了下也不对，怎么可能添加删除都有了却没有编辑呢，偷懒也不是这么偷的啊，于是我试了下最后的交互方式————双击，果然进去了

总之就是一点交互设计都没有，ok，吐槽到此为止，开始正题吧

# 创建规则集

首先咱们来开启v2rayN的规则集功能并且创建一个规则集，打开v2rayN，点击 设置--路由设置，把“使用路由高级功能”的勾打上

然后右击列表空白处，选择“添加规则集”，你可以先随便输入一个别名，再点击确定，然后再接着折腾

# 规则类型 （Outbound Tag）

一共有三种类型，分别是 Direct，Proxy 和 Block

**这里顺便提醒下，v2ray是由前往后依次匹配规则的，匹配到了就直接返回了，不会往下匹配了**

# 路由作用域

v2ray 提供了三种作用域字段，分别是 Port，IP 和 Domain。  
建议一条规则只使用其中一种作用域，至于为什么嘛，我也不知道，官方示例就这么写的

三种作用域字段的语法都各不相同，首先来看看port的语法

### Port

port 的语法很简单，你可以指定一个端口，也可以指定一个端口号段

```
<port>
<start>-<end>
```

示例如下

```
8080
0-65535
```

### IP

IP的语法除了能直接指定ip地址之外，他还多了个类似于指令的东西，用于导入预定义的ip地址

```
<ip address>

geoip:<tag>
```

示例如下

```
8.8.8.8

geoip:cn
```

至于这个标签有多少，有哪些名称，我就不知道了

### Domain

Domain字段有很多的指令，具体请看下表

关于匹配的指令

|名称|作用|
|-|:-:|
|domain:<域名地址>|匹配指定域名，*可省略指令*|
|keyword:<关键字>|使用关键字匹配域名|
|regexp:<正则表达式>|使用正则匹配域名|
|full:<字符串>|完全匹配指定字符串|

关于导入的指令

|名称|作用|
|-|:-:|
|geosite:< tag >|导入预定义域名|
|include:<文件名>|导入外部文件|

示例如下

```
domain:google.com @attr1 @attr2
keyword:google
regexp:www\.google\.com$
full:www.google.com

include:another-file
geosite:gfw
```

这部分内容参考自官方项目的readme文档： https://github.com/v2fly/domain-list-community#structure-of-data

# 配置推荐

以下配置根据 https://github.com/Loyalsoldier/v2ray-rules-dat 的配置参考修改而来，算是一个修正版了

### 黑名单模式

``` json
{
    "type": "field",
    "outboundTag": "block",
    "domain": ["geosite:category-ads-all"]
},
{
    "type": "field",
    "outboundTag": "proxy",
    "domain": ["geosite:tld-!cn", "geosite:gfw", "geosite:greatfire"]
},
{
    "type": "field",
    "outboundTag": "proxy",
    "ip": ["geoip:telegram"]
},
{
    "type": "field",
    "outboundTag": "direct",
    "port": "0-65535"
}
```

### 白名单模式

``` json
{
    "type": "field",
    "outboundTag": "block",
    "domain": ["geosite:category-ads-all"]
},
{
    "type": "field",
    "outboundTag": "direct",
    "domain": [
    "geosite:private",
    "geosite:apple-cn",
    "geosite:google-cn",
    "geosite:tld-cn",
    "geosite:category-games@cn"
    ]
},
{
    "type": "field",
    "outboundTag": "proxy",
    "domain": ["geosite:geolocation-!cn"]
},
{
    "type": "field",
    "outboundTag": "direct",
    "domain": ["geosite:cn"]
},
{
    "type": "field",
    "outboundTag": "proxy",
    "port": "0-65535"
}
```
