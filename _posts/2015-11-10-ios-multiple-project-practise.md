---
layout: post
title: "ios多工程开发实践"
description: "ios多工程开发实践"
category: ios
tags: [ios]
Code: true
---
{% include JB/setup %}
现在在公司做App，都是分模块分工程做。下面就简单记录下项目工程的配置。


首先肯定是新建一个workspace，命名为QMWorkspace，然后在这个workspace下又有三个工程，如下图

![project navigator](/assets/images/20151110/20151110-0@2x.png)


main是我们的主工程，其余两个都是static lirary (只支持8.0后可以考虑使用framework)。core里包含的可以是其它项目复用的，而loginRegister只是我们这个项目的一个注册登录模块。我们的main工程依赖于core和loginRegister，而loginRegister还可能依赖于core。


所以在main工程里我们需要先链接这两个静态库：

![link binary with libraries](/assets/images/20151110/20151110-1@2x.png)


链接好.a文件，我们还需要查找它们的header文件，所以需要设置main工程的头文件查找路径如下：

![header search paths](/assets/images/20151110/20151110-2@2x.png)


如果loginRegister工程依赖core工程，我们可以依照上面的方法对loginRegister工程进行同样的设置。


模块loginRegister工程可以包含有storyboard或者其它的资源文件，这时需要在loginRegister新建一个target，生成bundle文件。注意修改它的base sdk为latest ios和copy需要的资源文件进去。

![copy bundle resources](/assets/images/20151110/20151110-3@2x.png)


main工程里需要把生产好的loginRegister的bundle文件拖拽进去。

![drag bundle to main project](/assets/images/20151110/20151110-4@2x.png)


最后需要注意的一点就是工程build的顺序，编辑main的scheme，特别注意取消Parallelize Build（取消后build速度会变慢，但是能保证下面的build的顺序）

![main scheme](/assets/images/20151110/20151110-5@2x.png)


如果后面想给现有的workspace添加Pod，也没问题。在workspace那层目录新建一个podfile文件，用以下代码来install


{% highlight ruby %}
workspace 'QMWorkspace'
xcodeproj 'QMMain/QMMain'
pod 'AFNetworking', '3.0.0-beta.1'
{% endhighlight %}


执行 pod install 之后，最后工程的模样变成这样了：

![final project](/assets/images/20151110/20151110-6@2x.png)



参考链接：

[http://www.galloway.me.uk/tutorials/ios-library-with-resources/](http://www.galloway.me.uk/tutorials/ios-library-with-resources/)
