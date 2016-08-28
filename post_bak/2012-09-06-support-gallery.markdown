---
layout: post
title: "向Octopress博客添加相册功能"
date: 2012-09-06 17:27
comments: true
categories: octopress
tags: [相册]
---
唉，今天又没干别的，调试了一整天的Octopress，老板我对不起你。。。   
今天向我的博客添加了About Me页面和相册页面，这里主要说说相册功能是如何添加的。    
对于Octopress这种轻量级的静态博客，没有必要将相册功能做的很完善，神马添加相册，上传照片功能啥的都本地手动添加就行了，这里的相册主要就是展示一下照片，而且在简洁素雅的前提下最好有一些酷酷的效果，这就是我的追求。    
下午一直在找JS和Jquery的相册，试验了几个效果比较炫的，相册自带的css总是会和Octopress的css相冲突，导致切换到相册页面的时候整体排版就相当凌乱，在我很无助的时候，找到了这里：[appX](http://www.forceappx.com/blog/2011/12/28/getting-fancybox-to-play-nice-with-octopress/)，这个文章主要讲的是如何将[fancyBox](http://fancyapps.com/fancybox/#license)这个JQuery相册嵌入到Octopress日志中，给日志中的图片添加动态效果。在我试验他的方法之前，先试验了下fancyBox，效果出奇的让我满意，就是我要找的那种功能。    
fancyBox主要用于在网页中展示相册，单击相片墙中的一个相片的时候，能够有淡入淡出的效果，且能自如的通过鼠标滚轮或者相片两侧的向上向下按钮浏览整个相册的内容。从[fancyBox](http://fancyapps.com/fancybox/#license)下载之后运行一下Demo会有更好的理解。     
<center>
<img src="/images/support_gallery.JPG">
</center>

“安装过程”大概是这样的，首先得在博客中添加一个相册的导航按钮，方法是修改
    \source\_includes\custom\navigation.html    
仿造上面的添加一个相册页面的链接，比如这样：    
    <li><a href="{{ root_url }}/blog/gallery/demo">Gallery</a></li>    
我这里偷懒了，直接在他的demo上建立的我的相册功能，这样很方便，而且完全能满足我要的功能。    
添加了链接之后，将demo目录复制到`/source/blog/gallery/`目录下，当然，其他的目录比如`lib`、`source`目录也是必不可少的，这些里面包含了jquery和css等。别急，还得对`index.html`页面进行一下修改。如果不修改的话，这个相册就不能嵌入到网页当中，也就是说，单击相册链接之后，导航，边栏神马的就都不见了，所以得让Octopress将这个相册网页当成子页面，如下修改：将`<html>`、`<body>`、`<head>`等等独立页面拥有的元素统统去掉，然后在页面最开始部位添加    
<pre><code>---
layout: page
title: Gallery
footer: false
---</code></pre>    
这样Octopress就会认为这个页面是子网页。然后选择demo中自己喜欢的样式，剩余的该删就删吧，不要舍不得。。。    
添加相片呢，就按照他的格式添加就好了，对了，每个相片还需一个缩略图。    
嗯，大致就是这样吧，然后生成博客，就能看到相册功能了。    
如果还有什么不明白的，可以在下面给我留言。    