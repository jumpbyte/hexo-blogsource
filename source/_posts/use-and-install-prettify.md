---
title: 在hexo yilia博客主题下配置prettify高亮插件
date: 2016-07-02 21:14:37
tags: [hexo,prettify,高亮插件]
---

使用hexo的默认高亮插件总感觉支持的太少，代码高亮显示的不够细，下面我们来看下怎么把它替换为prettify高亮插件


## 第一步：禁用默认高亮插件

在hexo博客_config.yml中将highlight插件禁用
```
highlight:
  enable: false
  line_number: false
  auto_detect: false
  tab_replace:
```
<!--more-->
## 第二步：引用prettify插件

### **下载插件**

从github下载[prettify](https://github.com/google/code-prettify)源码，解压后将src目录重命名(如prettify)，然后拷贝至你的hexo博客的source目录，建议在博客source目录单独建立一个插件目录(plugins)存放，以后所有的插件都放置到此目录，这里我就是拷贝到source/plugins目录下,如图**（注：必须放在source目录下面，否则生成静态文章的时候无法输出到public目录）**
![](http://ww2.sinaimg.cn/large/7462786bgw1f5h1gmp2m7j20k709bmzm.jpg)

接着我们还要对hexo的_config.yml做如下配置
```
skip_render:
    - "plugins/**"
```
这个配置就是要告诉hexo对plugins目录下的所有文件跳过解析渲染，因为测试时发现如果不配置，加载prettify的相关js会报脚本错误，猜测hexo渲染造成的编码问题

### **引用插件**

在yilia的主题下面找到`yilia\layout\_partial\head.ejs`和`yilia\layout\_partial\after-footer.ejs`两个文件，分别引入样式和脚本文件（如果你用的是其他主题，可参照对应）

在head.ejs中引入样式

```js
.....省略代码.......
<!--prettify代码高亮主题css引入-->
<link href="/plugins/prettify/prettify.css" rel="stylesheet">
.....省略代码.......
```

在after-footer.ejs中引入脚本
```js
.....省略代码.......
<!--prettify代码高亮脚本引入-->
<script src="/plugins/prettify/prettify.js"></script>
<script type="text/javascript">
$(window).load(function(){
$('pre').addClass('prettyprint linenums').attr('style', 'overflow:auto;');
 prettyPrint();
})
</script>
.....省略代码.......
```
**注意：脚本文件引入注意一定要在jquery脚本之后**

**启动生成浏览**

此时我们用`hexo clean && hexo g && hexo server` 发布浏览，结果代码高亮怪怪的，如图:

![](http://ww4.sinaimg.cn/large/7462786bgw1f5gnoo6byij210x0g5wi3.jpg)

此时F12我们定位此块代码样式发现，刚第一步禁用的highlight高亮插件的样式还是会加载

![](http://ww2.sinaimg.cn/large/7462786bgw1f5gnphrzndj21es09kwj8.jpg)

### 第三步：对yilia主题调整修改

 上一步高亮结果显示有问题，于是立马怀疑yilia主题没有对highlight配置项做开关控制，结果发现在`themes\yilia\source\css\style.styl`文件确实没有做开关设置，而是直接引入
![](http://ww2.sinaimg.cn/large/7462786bgw1f5h231d2yjj20i60ccdhb.jpg)

所以，接下来要稍微做下调整
1. 把上面的红框标记的那一行注释掉
2. 仔细观察发现还有样式文件的影响，继续找到`themes\yilia\source\css\_partial\article.styl`文件先备份下，
然后将其中搜下所有pre,code 标签关联的样式删除之
3. 最后再在`themes\yilia\source\css\style.styl`文件中在加入下面几行调整样式

  ```css
  /*解决prettyify在yilia主题下面行号显示问题*/
  pre.prettyprint{
    padding-left: 20px;
  }

  /*代码自动换行*/
  pre{
      word-break: break-all;
      word-wrap: break-word;
    }
  ```

### **大功告成**

总算不白折腾，重新hexo生成发布浏览，prettify就能正常高亮代码了
![](http://ww2.sinaimg.cn/large/7462786bgw1f5h4twn7n2j210k0mitc3.jpg)

但默认主题样式还不够炫(niu)酷(bi),这里给大家提供n套[prettify主题](https://github.com/jmblog/color-themes-for-google-code-prettify/blob/master/dist/themes.zip)供大家下载,下载后解压，在themes目录下面有多套主题样式，如图：
![](http://ww2.sinaimg.cn/large/7462786bgw1f5i9ibsy1uj20cd0jegp9.jpg)

这里面每套主题都对应一个未压缩版和压缩版，而每套主题的效果，可以[点击此链接](https://jmblog.github.io/color-themes-for-google-code-prettify/)查看，喜欢那套样式，就直接重命名为prettify替换原来的prettify.css应用即可

## 继续优化

根据上述步骤，我们已经将默认高亮插件成功的替换成prettify高亮插件，但有两处不方便的地方
1. prettify插件有很多高亮主题样式，如果以后我们想替换其他主题样式，没有提供可配置主题自动替换
2. 想换回默认高亮插件得手动还原回去，不灵活
基于上面的问题考虑，我们来通过加入几个配置项，使其能够做到灵活切换主题和插件

### 增加配置项

1. 在hexo博客的根目录找到_config.yml,加入下面配置
    ```
    #prettify 插件位置
    # enable 启用和不启用
    # theme 使用prettify高亮主题名称
    prettify:
      enable: true
      theme: "这里你可以定义上面下载的themes主题包里面样式文件名，不带.css后缀"
    ```

2. 在你安装的主题根目录下面找到_config.yml,加入下面配置
    ```
    #highlight启用和禁用
    highlight:
      enable: false
    ```
3. 将你上面下载的prettify主题包解压后拷贝到博客`source/plugins/`目录，如下：

   ![](http://ww2.sinaimg.cn/large/7462786bgw1f5ia1mb52vj20bm01ka9y.jpg)

###  加入主题开关判断
1. 修改 themes\yilia\source\css\_variables.styl 文件，在文件任意位置加入下面这行
   ```
    highlight = hexo-config("highlight.enable")
   ```
2. 在themes\yilia\source\css\style.styl文件，加入开关判断
    ```
    if highlight{
     @import "_partial/highlight"
    }
    ```
3. 分别对`yilia\layout\_partial\head.ejs`和`yilia\layout\_partial\after-footer.ejs`两个文件做调整

   head.ejs调整如下：
   ```
   <% if (config.prettify.enable){ %>
     <!--prettify代码高亮主题css引入-->
     <link href="/plugins/prettify/themes/<%= config.prettify.theme %>.css" rel="stylesheet">
   <% } %>
   ```
   after-footer.ejs调整如下：
   ```
   <!--prettify代码高亮js引入-->
   <% if (config.prettify.enable){ %>
   <script src="/plugins/prettify/prettify.js"></script>
   <script type="text/javascript">
   $(window).load(function(){
   $('pre').addClass('prettyprint linenums').attr('style', 'overflow:auto;');
    prettyPrint();
   })
   </script>
   <%}%>
   ```
   这里引用prettify的样式文件统一定位到/plugins/prettify/themes/下面，所以如果你用默认的prettify.css样式
   只要要把默认样式也拷贝到/plugins/prettify/themes/目录下面即可

通过以上的配置，我们就可以灵活的切换prettify高亮主题了，并且通过配置可以来回切换高亮插件，只要你想要，就是这么任性！
