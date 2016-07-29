---
layout: post
title: "使用github开发项目"
date: 2013-03-29 10:45
comments: true
categories: 
tags: [js]
---
###上传本地项目到github###
1、进入代码所在目录并在github下创建目录    
如`myProject`    
2、初始化    
 `git init`    
3、将本目录下的文件添加    
 `git add .`    
4、提交并增加注释    
 `git commit -am "first commit"`    
5、提交到远程服务器上的指定目录`seagg`用户下的`myProject`目录    
`git remote add origin git@github.com:seagg/myProject.git`    
6、将其push到指定分支，其中master是远程分支，origin是前面创建的本地名称    
 `git push -u origin master`    

下载代码    
`git clone git@github.com:seagg/myProject.git`    

###在别人项目基础上开发###
1、进入别人的项目主页，点fork链接    
2、之后就可以clone到自己的电脑里进行开发    
3、提交时执行如下命令即可：

    git add .
    git commit -am "first commit"
    git push -u origin master

