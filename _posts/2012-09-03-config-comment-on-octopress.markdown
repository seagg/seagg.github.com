---
layout: post
title: "给octopress添加Disqus评论功能"
date: 2012-09-03 19:16
comments: true
categories: octopress
tags: [comment, 评论, Disqus]
---

经过了几天的折腾，昨天终于把octopress博客建立好了。更新起来很方便，只需要编写类似Latex语言的markdown文件，然后`rake generate`, `rake deploy`就可以啦。

今天加入了评论功能，这里主要记录下添加评论的过程。Octopress中其实已经集成了Disqus评论插件，只需要简单设置一下就可以。

+ **登录[Disqus网站](http://disqus.com)**, 然后注册用户名密码，邮箱验证。

+ 单击*Get this on your site*按钮

<center>
<img src="/images/add_comment1.PNG" >
</center>

+ **填写网站的信息**

<center>
<img src="/images/add_comment2.PNG">
</center>

+ 修改_config.yml文件:

<pre><code>#Disqus Comments
disqus_short_name: Site Shortname
disqus_show_comment_count: true</code></pre>

之后就可以通过`rake new_post['title']`创建日志，日志中设定：

    comment: true