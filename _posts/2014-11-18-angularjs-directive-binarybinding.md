---
layout: post
title: "AngularJS中双向绑定的数据何时更新"
description: ""
category: JavaScript
tags: [JavaScript, AngularJS]
Code: true
---
{% include JB/setup %}
前几天写directive的时候，遇到一个问题：就是directive中双向绑定的数据，在directive中更改它的值，不会在你使用该directive的controller中立即更新。

问题代码如下:

{% highlight javascript %}
 app.directive('myDirective', function() {
    return {
        scope: {
            number: '=',
            onClick: '&'
        },
        template: '<div ng-click="clickMe()">Click Me!</div>',
        link: function(scope, ele, attr) {
            scope.clickMe = function() {
                scope.number++;
                // scope.$apply() // 加上这句有用但会抛一个异常：$apply() already in progress
                console.log('In Directive: ', scope.number);
                scope.onClick();
            }
        }
    }
});

app.controller('mainCtr', function($scope) {

    $scope.myNum = 0;

    $scope.onClick = function() {
        console.log('In controller: ' + $scope.myNum);
    };

    $scope.$watch('myNum', function(val) {
        console.log('In Watch: ' + val);
    });
});

{% endhighlight %}

html上的代码调用如下：
{% highlight html %}
<div my-directive number="myNum" on-click="onClick()"></div>
{% endhighlight %}

之前我猜想的结果是这样的：

In Watch: 1

In Directive: 1

In controller: 1

但实际结果是这样的：

In Directive: 1

In controller: 0

In Watch: 1


也就是在directive中的function还没结束，调用controller中的callback，这时controller中的数据并没有得到更新，所以需要让directive中的function运行完成之后再调用就没问题了。修改代码如下：
{% highlight javascript %}

 app.directive('myDirective', function($timeout) {
    return {
        scope: {
            number: '=',
            onClick: '&'
        },
        template: '<div ng-click="clickMe()">Click Me!</div>',
        link: function(scope, ele, attr) {
            scope.clickMe = function() {
                scope.number++;
                // scope.$apply() // 加上这句有用但会抛一个异常：$apply() already in progress
                
                $timeout(scope.onClick);
                console.log('In Directive: ', scope.number);
                
            }
        }
    }
});

{% endhighlight %}

最后的结果输出是：

In Directive:  1

In Watch: 1

In controller: 1


虽然结果并不是我最先猜测的那样，但至少解决了directive中回调controller方法的数据同步问题。

其实直接使用scope.$apply()方法就是我期待的结果，虽然会报错，但是不影响结果。
