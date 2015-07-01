---
layout: post
title: "layoutMargins给ios7,ios8带来的适配问题"
description: ""
category: ios
tags: [ios, AutoLayout]
code: true
---
{% include JB/setup %}
在ios8上UIView新增加了layoutMargins这个属性，于是autoLayout有了相对于margin（相当于容器中多一条约束的线）的约束。在Stack Overflow上有人也对这个margin展开过讨论，[http://stackoverflow.com/questions/25807545/what-is-constrain-to-margin-in-storyboard-in-xcode-6](http://stackoverflow.com/questions/25807545/what-is-constrain-to-margin-in-storyboard-in-xcode-6)

这个新增的约束给我们适配ios7时造成了一些干扰。

在使用storyBoard的时候，经常想把一些UIView跟UIViewController中的UIView的边缘对齐。等我把元素拖拽跟容器边缘对齐时，添加约束的时候，XCode会很“聪明“的使用-16pt来约束，而不是0。

![Add new constraints](/assets/images/20150701/add_new_constraints.png)

下图是我在xcode设置的约束，Box1是Leading相对于superview的leading margin设置，Box2是Box2的leading maring相对于superview的leading margin设置的， Box3就是leading相对于superview的leading margin间距-16来设置的

![In Xcode AutoLayout](/assets/images/20150701/in_xcode.png)

运行在ios8实机下的效果跟XCode里一样

![In ios8](/assets/images/20150701/in_ios8.png)

但运行在ios7实机下的效果却是这样的

![In ios7](/assets/images/20150701/in_ios7.png)

在ios7下，layoutMargins这个属性没有暴露，但是autoLayout中却应该是存在margin的，边距是8。在ios8时，Root View的间距改为了16（默认的UIView的间距还是8,8,8,8）。

另外，iphone6+的Root View的间距不止是16，我个人模拟器中运行得到的是20，所以我也怀疑margin可能还跟机型有关。（没那么多实机测试。。。）

那如何解决这个适配问题？在autolayout中不要相对于margin进行约束就行了。不清楚苹果为什么要做这个margin哩？

![uncheck margin](/assets/images/20150701/uncheck_margin.png)

<br />
参考链接：

[http://stackoverflow.com/questions/25807545/what-is-constrain-to-margin-in-storyboard-in-xcode-6](http://stackoverflow.com/questions/25807545/what-is-constrain-to-margin-in-storyboard-in-xcode-6)

[http://qiita.com/yimajo/items/10f16629200f1beb7852](http://qiita.com/yimajo/items/10f16629200f1beb7852)





