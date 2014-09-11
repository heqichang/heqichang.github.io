---
layout: post
title: "JavaScript循环语句中的闭包陷阱"
description: ""
category: JavaScript
tags: [JavaScript]
code: true 
---
{% include JB/setup %}
最近一段时间一直在写js，给了我很多认真学习js的时间。今天上午正好遇到一个循环语句中写闭包造成的一个问题。看下下面代码，原意是想每隔段时间打印出一个字母出来。

{% highlight javascript %}
var myArray = ['h', 'e', 'q', 'c'];
for(var i in myArray) {
    setTimeout(function(){
        console.log(myArray[i]);
    }, 100 * (i + 1));
}
{% endhighlight %}

但是，实际的结果，它打出了4个c。其实仔细想想其实也不难理解，setTimeout里的函数并不是立即执行，它是过了一段时候后再执行的，这时i的值已经是i = 3了，所以它每次都只输出的是c。这是我个人比较简单的理解。网上有很多关于这种问题的讨论，可以看看[知乎上的这篇文章](http://www.zhihu.com/question/20019257)，解释的比较详细。

那如何解决这个问题？简单的做法就是套个函数给它。代码如下：

{% highlight javascript %}
var myArray = ['h', 'e', 'q', 'c'];
for(var i in myArray) {
    printOut(i);
}
function printOut(index) {
    setTimeout(function(){
        console.log(myArray[index]);
    }, 100 * (index +1));
}
{% endhighlight %}




