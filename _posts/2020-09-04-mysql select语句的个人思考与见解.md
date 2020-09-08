---
layout: post
title: mysql select语句的个人思考与见解
categories: mysql
tags: [select语句]
---
   
* content
{:toc}    

# 前言

最近因为要写个前端项目，所以复习了下MySQL并且同时写了笔记。当我学到select语句的时候我就犯难了，有一部分我不知道该咋写了。  
`SELECT columns... FROM table` 就是这段代码的columns部分，我发现一个很“神奇”的事情，他几乎什么都能写，不管是列名称，函数，字面量还是常量，而且列名称还能作为函数的参数来用，这就让我很懵了

# 个人见解

后来经过思考后我发现这好像就是绕弯子的问题，因为当我把数据表与c#的类联系起来后，我突然恍然大悟，敢情数据库里也有变量的概念，然后一切全都想通了，最后总结如下

`SELECT columns... FROM table` 这个columns只是定义了要读取哪些列字段（或者说变量），至于在哪读就是 FROM 指定的了

（我咋感觉这句话是废话呢）
