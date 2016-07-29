---
layout: post
title: "指针数组与数组指针"
date: 2012-09-06 08:59
comments: true
categories: C语言
tags: [指针, 数组, 函数指针]
---
昨天从面试宝典上看了道关于指针的面试题，发现这个上次绿盟科技笔试的时候考过，当时很凌乱，有几个记混了。

题目是这样的：   

+ 定义一个整形数     
+ 定义一个指向整形数的指针    
+ 定义一个指向指针的指针，它指向的指针，是一个指向整形数的指针    
+ 定义一个具有10个整形数的数组    
+ 定义一个具有10个指针的数组，这些指针都是指向整形数的    
+ 定义一个指向有10个整形数 数组的指针    
+ 定义一个指向函数的指针，该函数有一个整形参数 并返回一个整形数据    
+ 定义一个有10个指针的数组，每个指针指向一个函数，该函数有一个整形参数，返回一个整形数   
<pre><code>a) int a; // An integer 
b) int *a; // A pointer to an integer 
c) int **a; // A pointer to a pointer to an integer 
d) int a[10]; // An array of 10 integers 
e) int *a[10]; // An array of 10 pointers to integers 
f) int (*a)[10]; // A pointer to an array of 10 integers 
g) int (*a)(int); // A pointer to a function a that takes an integer argument and returns an integer 
h) int (*a[10])(int); // An array of 10 pointers to functions that take an integer argument and return an integer </code></pre>

<span style="color:red; font-weight:bold">诀窍：弄清楚* [] ()的优先级</span>     
详细解答参考这里：[解答](http://blog.csdn.net/lyl494223339/article/details/7854396)