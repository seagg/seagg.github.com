---
layout: post
title: "textarea的内容中含有html的情况"
date: 2012-09-10 11:22
comments: true
categories: web前端
tags: [textarea]
---
还有一周就可以回学校然后回家了，上周五很顺利的跟老板和经理请了两周假，所以这周得认真工作了。   
接着测试我写的插件，果然发现了问题。。。    
大概是这样的，这个插件是JIRA系统间传递issues，每个issue有很多的field，老板为了保证安全，要求在传输的时候检测每个域，看有没有指定的敏感关键字，如果有要求用户传输issue中进行修改，今天测试的时候，发现有个description域传输有问题，即如果description中含有关键字，要求用户在textarea中修改，程序再读取textarea中的内容重新存入issue中供传输。    
可是今天传输过程中，发现有个issue的description居然有很多html标签，显示到textarea中倒是没问题，可是读取的时候就郁闷了：    
<pre><code>本来应该是这样：    
<textarea ...>string</textarea>
可是这个issue的description是这样的。。。
<textarea cols="60" rows=“2”> &lt;feature name= &quot;http://... required= true &quot; /&gt;...	</textarea>
</code></pre>
这样的话用textarea的value属性读取的时候就会遇到问题，双引号，大于小于号都会对value产生影响，导致传过去的有错误。    
我记得好像有个类似的函数来用字符串传输html的，但函数名称想不起来了，只能用java的字符串替换函数将"替换为`&quot;`，将<>分别替换为`&lt;`，`&gt;`    
然后就传输正确了。好惊险啊，这要是突然出了什么大bug，我就走不了了。。。    
附上[转义字符表](http://114.xixik.com/character/)  