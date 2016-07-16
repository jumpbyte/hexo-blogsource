---
title: jquery实现页面置顶功能
date: 2013-11-26 09:14
categories: 代码片段
tags: ['js']
---

### 实现代码
<br/>
```js
<html>
<head>
 <title></title><br><script type='text/javascript>
//回到顶部功能
function gotoTop(min_height) {
    $("#toTop").click(//定义返回顶部点击向上滚动的动画
        function () {
            $('html,body').animate({ scrollTop: 0 }, 700);
        });
    //获取页面的最小高度，无传入值则默认为600像素
    min_height ? min_height = min_height : min_height = 600;
    //为窗口的scroll事件绑定处理函数
    $(window).scroll(function () {
        //获取窗口的滚动条的垂直位置
        var s = $(window).scrollTop();
        //当窗口的滚动条的垂直位置大于页面的最小高度时，让返回顶部元素渐现，否则渐隐
        if (s > min_height) {
            $("#totop").fadeIn(100);
        } else {
            $("#toTop").fadeOut(200);
        };
    });
};
</script>'
 </head>
    <body>
       <div id='toTop' ></div>
   </body>
</html>
```

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/p/3442638.html)
