---
layout: post
title: 正则平衡组在.NET下的使用
description: "正则表达式 平衡组在 .net 下的使用"
category: .Net
tags: [.net, regex]
code: true
---
{% include JB/setup %}
今天碰到一个任务，让我从一段js代码中抽取出一段json数据。抽取的代码如下：

{% highlight javascript %}
 var videoListJSON = {
  "count": 5,
  "name": "Bear 101",
  "clips": [
    {
      "clipRefId": "1f3a68a05cf6d5efcc00e5c416c92964d252cbc7",
      "contentId": "842166",
      "programTitle": "Porter Ridge",
      \\...
      "aspectRatio": "16:9"
    },
    {
      "clipRefId": "5f9822763f58fae2ffbf050b64ffe2b0b9c787ed"
      \\...
    },
    {
      "clipRefId": "5f9822763f58fae2ffbf050b64ffe2b0b9c787ed"
      \\...
    }]};
{% endhighlight %}

任务就是抽取这个json变量中的clips，然后数组中每个数据还得单独抽取出来。

刚接到活的任务，第一反应就是用正则去匹配，但是想要准确的把想要的东西提取出来也不能单是利用简单的规则就行了，这里就需要使用到正则的平衡组这个特性了。（注明：以下代码中所使用的正则在C#里有效，并不代表其它语言里也能这么写，因为每个语言对正则表达式的某些特性的写法是有区别的）

{% highlight c# %}
static string[] extractData(string input)
{
    //先抽取clips后整个的字符串
    Regex r1 = new Regex(@"
        ""clips""\s*:\s*
            (
                \[
                    (
                        (?<open>\[)
                        |
                        (?<-open>\])
                        |
                        [^\[\]]+
                    )*
                    (?(open)(?!))
                \]
            )", RegexOptions.IgnorePatternWhitespace);

    //匹配clips数组中每一项
    Regex r2 = new Regex(@"
        {
            (
                (?<open>{)
                |
                (?<-open>})
                |
                [^{}]+
            )*
            (?(open)(?!))
        }", RegexOptions.IgnorePatternWhitespace);

    string clipsString = string.Empty;
    List<string> clipsList = new List<string>();

    Match m1 = r1.Match(input);
    if (m1.Success)
    {
        clipsString = m1.Groups[1].Value;
    }

    var m2s = r2.Matches(clipsString);
    foreach (Match m2 in m2s)
    {
        clipsList.Add(m2.Groups[0].Value);
    }

    return clipsList.ToArray();
}
{% endhighlight %}

关于正则平衡组的详细说明，网上已有好多人写出详解了。具体链接参考如下：

[正则表达式——详细讲解平衡组](http://blog.csdn.net/zm2714/article/details/7946437)

[Matching Balanced Constructs with .NET Regular Expressions](http://weblogs.asp.net/whaggard/archive/2005/02/20/377025.aspx)
