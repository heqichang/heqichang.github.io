---
layout: post
title: 我对IoC容器的理解
description: "IoC .NET C# 容器"
category: .Net
tags: [.net, IoC, Autofac, c#]
code: true
---
{% include JB/setup %}
依赖注入相信大部分人都了解了，下面用个简单的代码再描述一下：

{% highlight c# %}
public interface IOutput
{
    void Print(string info);
}

public class ConsoleOutput : IOutput
{
    public void Print(string info)
    {
        Console.WriteLine("Console print: {0}", info);
    }
}

public class Client
{
    private IOutput _output;

    //构造函数注入
    public Client(IOutput output)
    {
        this._output = output;
    }

    public void Print(string info)
    {
        this._output.Print(info);
    }
}
{% endhighlight %}

然后什么时候注入具体的类，我们一般都会再定义一个Assembler来负责决定具体类，这时必然会和ConsoleOutput有依赖关系产生，或者灵活一点可以采用反射加配置的方式，但是代码必然将变复杂，而且需要考虑到许多情况。而IoC容器就是为了解决这样的问题的。下面使用Autofac做例子：

{% highlight c# %}
static void Main(string[] args)
{
    var builder = new ContainerBuilder();

    //注册当前Assembly下所有以Output结尾的类
    builder.RegisterAssemblyTypes(Assembly.GetExecutingAssembly())
        .Where(n => n.Name.EndsWith("Output"))
        .AsImplementedInterfaces();

    //注册Client
    builder.RegisterType<Client>();

    var container = builder.Build();

    var client = container.Resolve<Client>();
    client.Print("I'm heqichang");
}
{% endhighlight %}

引入Autofac之后基本可以不用你再编写代码来注入Client，Autofac帮助你自动完成了这一些事。所以，你只要编写好你的接口、具体类和你要注入的类就行了，剩下由IoC容器来组合运行。


参考链接：
[AutoFac](http://autofac.org/)
[AutoFac使用方法总结](http://niuyi.github.io/blog/2012/04/06/autofac-by-unit-test/)

