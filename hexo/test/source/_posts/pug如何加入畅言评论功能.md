title: pug添加畅言评论模块
tags: 
  - hexo 
  - 畅言
categories: 
  - hexo建站
---

## 前言

小菜鸡前段时间尝试着用 [Hexo](https://hexo.io/) 建一个个人博客。 安装hexo过程很顺畅，没有遇到什么问题，但是如果你有hexo安装的其他问题可以去看这位 [小仙女](http://cherryblog.site/categories/Hexo%E5%BB%BA%E7%AB%99/) 写的很详细的教程。 相比之下我的教程内容不是很精细，但是如果你想少走弯路可以来瞅瞅。

## 问题

我用的是畅言来当评论后台，但是我在添加评论模块的时候遇到了棘手的问题 -- > 这个主题的语言 > [Pug](https://www.npmjs.com/package/pug) 我不会，哈哈。其实就是把畅言的模块放到你想要的位置，然后配置[SourceID](http://changyan.kuaizhan.com/static/help/f-source-id.html)。但是由于语言不通，所以不能成功的设置 [SourceID](http://changyan.kuaizhan.com/static/help/f-source-id.html)。查到的资料也都是 [Next](http://theme-next.iissnan.com/) 主题相关的解决方案，而我的主题是Pug生成的。难道我要因为一个评论功能就换掉我精挑细选的主题么，当然不阔能。一番翻云覆雨之后我终于搞定了这个东西。
![image](http://p6a4c3kie.bkt.clouddn.com/image/yaco_blogs_comment.jpeg)

### Pug

Pug的前身是jade，百度jade和Pug的教程都是一样的，它们的语法都很相近。

### SourceID

SourceID 是文章的唯一标识符，用来区分的文章的评论。文章的唯一标识符可以根据文章的标题、发布时间或者文章链接来生成独一无二的哈希值， 如果你不想区分开可以不写。

### 注册畅言

注册没什么难的地方，就是繁琐。我就不啰嗦了。

### 安装畅言

在博客里面 [安装畅言](http://changyan.kuaizhan.com/install/code/self-adaption)，不要被这两个字困扰，其实是引入。畅言会自动给你生成相关代码，我用的是自适应版本，可以根据自己的需要去选择。
下面看代码 ：首先是布局的代码，将这段代码放在你想要显示评论的位置。一般都会放在文章的底部。

```hash
<!--PC和WAP自适应版-->
<div id="SOHUCS" sid="请将此处替换为配置SourceID的语句" ></div> 
```

sid 的值就是 SourceID 就是用来区分文章评论的

然后是js部分

```hash
<script type="text/javascript"> 
(function(){ 
var appid = 'xxxxxxxxxx'; 
var conf = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'; 
var width = window.innerWidth || document.documentElement.clientWidth; 
if (width < 960) { 
window.document.write('<script id="changyan_mobile_js" charset="utf-8" type="text/javascript" src="https://changyan.sohu.com/upload/mobile/wap-js/changyan_mobile.js?client_id=' + appid + '&conf=' + conf + '"><\/script>'); } else { var loadJs=function(d,a){var c=document.getElementsByTagName("head")[0]||document.head||document.documentElement;var b=document.createElement("script");b.setAttribute("type","text/javascript");b.setAttribute("charset","UTF-8");b.setAttribute("src",d);if(typeof a==="function"){if(window.attachEvent){b.onreadystatechange=function(){var e=b.readyState;if(e==="loaded"||e==="complete"){b.onreadystatechange=null;a()}}}else{b.onload=a}}c.appendChild(b)};loadJs("https://changyan.sohu.com/upload/changyan.js",function(){window.changyan.api.config({appid:appid,conf:conf})}); } })(); </script>
```

## 操作

### 新建模块

一、新建评论模块文件
  
  路径： themes -> terminal -> layout -> includes   在自己使用的主题下新建 comment.pug  如图：

![image](http://p6a4c3kie.bkt.clouddn.com/image/yaco_blogs_list.jpeg)

  路径： themes -> terminal -> layout -> source -> js  下新建comment文件夹用来存放一系列的js文件 ： comment.js (畅言提供的js代码) common.js (畅言的配置) getHashCode.js (获取文章的独一无二的哈希值，作为 SourceID) 

![image](http://p6a4c3kie.bkt.clouddn.com/image/yaco_blogs_list2.jpeg)

二、加入评论模块

  在 comment.pug 加入代码

```hash
- var hash = page.title + page.date;
div(id="SOHUCS" hash=`${hash}` sid="")
each url in theme.comment.js
  script(src=url) 
```

hash 是 根据文章的标题和文章的时间组合成的字符串 

  在 comment.js 内加入

```hash
<script type="text/javascript"> 
(function(){ 
var appid = 'xxxxxxxxxx'; 
var conf = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'; 
var width = window.innerWidth || document.documentElement.clientWidth; 
if (width < 960) { 
window.document.write('<script id="changyan_mobile_js" charset="utf-8" type="text/javascript" src="https://changyan.sohu.com/upload/mobile/wap-js/changyan_mobile.js?client_id=' + appid + '&conf=' + conf + '"><\/script>'); } else { var loadJs=function(d,a){var c=document.getElementsByTagName("head")[0]||document.head||document.documentElement;var b=document.createElement("script");b.setAttribute("type","text/javascript");b.setAttribute("charset","UTF-8");b.setAttribute("src",d);if(typeof a==="function"){if(window.attachEvent){b.onreadystatechange=function(){var e=b.readyState;if(e==="loaded"||e==="complete"){b.onreadystatechange=null;a()}}}else{b.onload=a}}c.appendChild(b)};loadJs("https://changyan.sohu.com/upload/changyan.js",function(){window.changyan.api.config({appid:appid,conf:conf})}); } })(); </script>
```

  在 getHashCode.js 里面加入 

```hash
  //获取字符串的 哈希值 
  function getHashCode(str,caseSensitive){
    if(!caseSensitive){
      str = str.toLowerCase();
    }
    var hash  =   1315423911,i,ch;
    for (i = str.length - 1; i >= 0; i--) {
      ch = str.charCodeAt(i);
      hash ^= ((hash << 5) + ch + (hash >> 2));
    }
    return  (hash & 0x7FFFFFFF);
  }

  var SOHUCS = document.getElementById("SOHUCS");
  var hashData = SOHUCS.getAttribute("hash");
  SOHUCS.setAttribute("sid", getHashCode(hashData));
```

将获取哈希值的方法添加到 sid的属性值里
 
  在 post.pug 最下面 引入 comment.pug 。post.pug 是文章详情页面， 也就是文章详情页面才会显示评论。

```hash
extends includes/layout.pug

block content
  article#post
    a.article-title= page.title || 'No Title'
    span(id="busuanzi_container_page_pv" class="article-traffic")
      |本文阅读量 
      span(id="busuanzi_value_page_pv")
      | &nbsp次
    br
    time.article-date #[i(class="fa fa-calendar" aria-hidden="true")] !{date(page.date, config.date_format)}
    != page.content
  include includes/pagination.pug
  include includes/comment.pug 
```


  在主题下的 _config.yml 的配置文件里面添加代码，否则生成页面的时候不会引入这3个js文件， 代码如下：

```hash
comment:
  js:
    common: /blogs/js/comment/common.js
    getHashCode: /blogs/js/comment/getHashCode.js
    commemt: /blogs/js/comment/comment.js
```

地址以自己部署博客的地址为准， 比如我生成的页面都部署在 www.shyboy.cc/blogs 文件夹下。所以就是 /blogs/js/comment/xxx.js

至此就大功告成啦，点击文章，下面应该会有畅言的评论出现。 如想修改主题，就在 common.js 加入如下代码：

```hash
var _config={
  skin: 'black' /*黑色皮肤，目前只支持black参数*/
}
```








