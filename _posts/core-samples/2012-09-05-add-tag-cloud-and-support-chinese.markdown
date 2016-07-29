---
layout: post
title: "Octopress添加tag cloud且支持中文"
date: 2012-09-05 19:06
comments: true
categories: octopress
tags: [octopress, tag, 中文支持]
---
今天上班又不务正业了，主要是昨天晚上就完成了老板最近布置的修改JIRA插件，添加敏感关键字检测及提供修改功能的任务，上午给老板演示了一下，在他提出的各种极端情况下插件都能很好的工作，老板让我今天在界面上加些交互信息，使交互更加友好。   
整个白天都在折腾我的Octopress博客，这个果然很需要耐心，还好我最近已经被java的各种bug折磨的宠辱不惊了。今天主要添加了tag功能。正如[Log4D](http://log4d.com/2012/05/tag-cloud/)所说的那样，一篇文章只能输入一个category，但可以有多个tag，我很同意这个观点。我这里也是用了他所用的两个插件[robbyedwards/octopress-tag-pages](https://github.com/robbyedwards/octopress-tag-pages)和[robbyedwards/octopress-tag-cloud](https://github.com/robbyedwards/octopress-tag-cloud),不过跟他不同的是，我只用了官方网站上提供的两个插件，因为我觉得category还是排列整齐点的好，毕竟一个博客的category不会很多，没有必要弄成tag那种有大有小的样式，所以category我还沿用了Octopress官方的样式。这两个插件安装起来不太难，不过有些细节需要注意的。    
第一个插件的功能是使文章支持`tag`，第二个是集中显示`tag cloud`。    

**关于第一个插件的安装**，我是把官网上提供的文件都复制到了相应的目录，这样在Archives中就能看到tag的信息，[robbyedwards/octopress-tag-pages](https://github.com/robbyedwards/octopress-tag-pages)中提到的`Usage`,上面的我没有用到，只是往`config.yml`中添加了   
    tag_dir: tags    
第二个配置信息添加的时候总是出错，不过不添加也没关系。可以在下属文件中进行修改。  
    \source\includes\post\tags.html    
只需在`span`标签中添加Tags:即可。
添加之后就可以使用tag功能了，具体是在新post出的日志中添加,语法是    
    tags: [tag1, tag2, ...]
为了使每次新post出的日志都能自动附带tags: 标签，可以修改`config.rb`文件，在110行左右添加`post.puts "tags: "`。    
**第二个插件的安装**，复制相应文件到对应的目录，然后到`source\_includes\custom\navigation.html`中添加一个tag cloud页面的链接，指向刚刚添加的tags.html文件。对了，还得修改下样式，到`sass/custom/_styles.scss`中添加如下css样式： 
<pre><code>#content article .cloud li{
  display: inline;
  list-style: none outside none;
  padding: 0 4px;
}</code></pre>    
之后把tags.html中的样式换成cloud即可。    
**关于使category，和tag支持中文**，由于我没有用Log4D修改之后的插件，所以出现了category和tag不支持中文的情况，这个的修改参考了[pf_miles](http://pfmiles.github.com/blog/liquid-error-about-regexp-match-when-using-octopress-tagcloud/)的日志，说的挺好的，不过对于像我这样两个tag插件都用官方原版的来说，就不能直接复制里面的代码了，否则会出现tag和category相混淆的情况。但他的思想还是可以学的嘛，提炼出来，就是把`plugins`目录下的`tag_cloud.rb`、`tag_generator.rb`、`catetory_generator.rb`这几个文件的#正则表达式#部分都加上\u，就可以了。    
思路可能有点乱，这是我凭借记忆写出来的，有可能有些出入，如果有什么不明白的，可以评论，我会及时回复的。
