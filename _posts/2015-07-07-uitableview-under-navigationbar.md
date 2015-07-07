---
layout: post
title: "UITableView被navigationBar和tabBar遮挡原因及解决方案"
description: "UITableView被navigationBar和tabBar遮挡原因及解决方案"
category: ios
tags: [ios, AutoLayout]
Code: true
---
{% include JB/setup %}
自从ios7后，在UI布局上加上automaticallyAdjustsScrollViewInsets，topLayoutGuide，bottomLayoutGuide还有edgesForExtendedLayout和extendedLayoutIncludesOpaqueBars这几个属性，让人在StoryBoard直接使用UITableView或者其它一些滚动视图布局时经常会有些莫名奇妙的问题出现。比如，tableview上一些cell被遮挡，要不就是tableview的头部或者底部会多出一些空白的空间。

![under navigationbar](/assets/images/20150707/tableview1.png)

![white space in tableview header](/assets/images/20150707/tableview2.png)

要在布局上解决这些问题，就要弄清楚上面说的那几个属性。先查看下官方文档的解释：[Appearance and Behavior](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/TransitionGuide/AppearanceCustomization.html)


automaticallyAdjustsScrollViewInsets属性会自动调整根视图内第一个滚动视图的contentInsets（注意是第一个，也就是说有多个时，只有一个会生效）。它在ios7后默认是开启的，但它会依赖很多其它条件判断是否调整contentInsets，所以我认为它是产生这些问题的罪魁祸首。

还有一个值得注意的坑就是topLayoutGuide和bottomLayoutGuide在自动布局中的高度会随着你是否使用nav或者tab来调整的。在StoryBoard里添加相对于根视图Top和Bottom的约束时会自动使用这两个属性。

automaticallyAdjustsScrollViewInsets和edgesForExtendedLayout在面板的这个位置进行设置

![config automaticallyAdjustsScrollViewInsets and edgesForExtendedLayout](/assets/images/20150707/tableview3.png)


<br />
我总结了之前我经常遇到问题的一些情况：

1，在UIViewController中添加一个UITableView，并且设置约束好了tableview的上下约束是相对于TopLayoutGuide和BottomLayoutGuide，（直接在StoryBoard里设置相对于根视图的top和bottom约束貌似只能设置为TopLayoutGuide和BottomLayoutGuide）如果不取消勾选Adjust Scroll View Insets，如果有navbar或者tabbar，则tableview头部和底部分别会有64和49的空白空间。

2，代码中直接使用frame来布局时，tableview在根视图中坐标(0,0)或者直接使用UITableViewController时，如果取消勾选Adjust Scroll View Insets，则cell有可能被navbar或者tabbar遮挡。


<br />
参考链接：

[http://mued.sohu.com/2013/06/ios-7-ui-transition-guide/](http://mued.sohu.com/2013/06/ios-7-ui-transition-guide/)

[http://www.kyleduo.com/?p=302](http://www.kyleduo.com/?p=302)





