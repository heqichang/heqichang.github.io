---
layout: post
title: "ngRepeat小心使用$index"
description: ""
category: JavaScript
tags: [JavaScript, AngularJS]
Code: true
---
{% include JB/setup %}

偶尔在写ng-repeat的时候，会用到$index这个变量，但要注意，如果ng-repeat中加入了filter就要小心了。比如下面代码：

{% highlight html %}


<!DOCTYPE html>
<html ng-app="myApp">
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        ul {
            width: 100%;
            float: left;
        }
        li {
            float: left;
            width: 150px;
        }
    </style>
</head>
<body ng-controller="mainCtr">
<ul ng-repeat="num in numbers | filter : customFilter" ng-show="$even">
    <li>{{"{{num"}}}}</li>
    <li ng-show="numbers[$index + 1]">{{"{{numbers[$index + 1]"}}}}</li>
</ul>
<script src="bower_components/angularjs/angular.js"></script>
<script src="controller.js"></script>
</body>
</html>

{% endhighlight %}

controller.js的代码如下：

{% highlight javascript %}

(function(){
    var app = angular.module('myApp', []);

    app.controller('mainCtr', ['$scope', function($scope)	{
        $scope.numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
        $scope.customFilter = function(data) {

            if(data % 3 === 0) {
                return true;
            }

            return false;
        };

    }]);
})();

{% endhighlight %}



之前我想要的结果是这样的：

3         6

9


但实际结果是这样的：

3         2

9         4

错误的原因是我错误的以为$index是原数组的索引，就算加入filter也不会有影响，实际上$index是实际循环数组的索引，filter之后会$index也会有相应的变化。

将html中的循环改成以下这个样子就可以解决这个问题了：



{% highlight html %}

<ul ng-repeat="num in (numByFilter = (numbers | filter : customFilter))" ng-show="$even">
    <li>{{"{{num"}}}}</li>
    <li ng-show="numByFilter[$index + 1]">{{"{{numByFilter[$index + 1]"}}}}</li>
</ul>
 
{% endhighlight %}


