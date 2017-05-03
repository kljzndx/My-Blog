---
layout: post
title: 推倒重来，开始写blog
tags: [UWP,路由事件]
---

最近在重写歌词编辑器。。。   
我需要在播放进度条被按下和弹起时触发一个我自己写的事件

```XAML
<Slider PointerPressed="Slider_PointerPressed" PointerReleased="Slider_PointerReleased" />
```

```C#
        private void Slider_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            UserChangeTime?.Invoke(this, EventArgs.Empty);
        }

        private void Slider_PointerReleased(object sender, PointerRoutedEventArgs e)
        {
            UserChangeTime?.Invoke(this, EventArgs.Empty);
        }
```
结果Run了之后，发现只有右键才能触发这按下和弹起的事件，对左键根本没反应   
于是我就Google去了。。。Google到这个[帖子](http://stackoverflow.com/questions/14767020/pointerpressed-not-working-on-left-click)。。   

TMD，原来是微软构建控件时设置了 `e.Handled = true;`  
解决方法上面的帖子已经说了，在后台订阅这些路由事件
```C#
Position_Slider.AddHandler(PointerPressedEvent, new PointerEventHandler((s, e) => UserChangeTime?.Invoke(this, EventArgs.Empty)), true);

Position_Slider.AddHandler(PointerReleasedEvent, new PointerEventHandler((s, e) => UserChangeTime?.Invoke(this, EventArgs.Empty)), true);
```

各参数解释
- 第一个参数，路由事件对象
- 第二个参数，处理器（委托）
- （**很关键**）第三个参数，用来指示标记为已处理后是否仍要执行的

把上面两段代码加到构造函数里面，OK Run一下  
**Very Good!** 完美解决
