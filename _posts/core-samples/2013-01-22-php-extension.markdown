---
layout: post
title: "nginx+fastcgi下开发php扩展"
date: 2013-01-22 15:26
comments: true
categories: [PHP, C语言]
tags: [extension]
---
本文参考：[用C/C++扩展你的PHP](http://www.laruence.com/2009/04/28/719.html)    
要开发扩展，首先需要有php的源码包，可以到php官网下载，我使用的版本是5.3.10，之后解压，进入ext目录里。    
先建立一个函数定义到.def文件当中，比如`string self_concat(string str, int n)`，这样的话，会在稍后生成的扩展框架中自动添加函数原型和一个默认实现。当然，也可以不使用这个文件，手动去建立函数。

    vi myfunctions.def

之后是用php的ext_skel生成php扩展的框架：

    ./ext_skel --extname=myfunctions --proto=../myfunctions.def

会在ext目录下生成一个名为`myfunctions`的目录，里面包含了php扩展框架基本文件。    
之后，是修改`php_myfunctions.h`文件，在`PHP_FUNCTION(confirm_myfunctions_compiled);`之后添加函数定义 

    PHP_FUNCTION(self_concat);

再修改`myfunctions.c`文件，在文件末尾添加函数实现
比如

```c
PHP_FUNCTION(self_concat)
{
    char *str = NULL;
    int argc = ZEND_NUM_ARGS();
    int str_len;
    long n;
    char *result; /* Points to resulting string */
    char *ptr; /* Points at the next location we want to copy to */
    int result_length; /* Length of resulting string */
    if (zend_parse_parameters(argc TSRMLS_CC, "sl", &str, &str_len, &n) == FAILURE)
        return;
    /* Calculate length of result */
    result_length = (str_len * n);
    /* Allocate memory for result */
    result = (char *) emalloc(result_length + 1);
    /* Point at the beginning of the result */
    ptr = result;
    while (n--) {
        /* Copy str to the result */
        memcpy(ptr, str, str_len);
        /* Increment ptr to point at the next position we want to write to */
        ptr += str_len;
    }
    /* Null terminate the result. Always null-terminate your strings
    even if they are binary strings */
    *ptr = '\0';
    /* Return result to the scripting engine without duplicating it*/
    RETURN_STRINGL(result, result_length, 0);
}

```
之后，修改`config.m4`文件,去掉如下两行前的dnl

    PHP_ARG_ENABLE(myfunctions, whether to enable myfunctions support,
    [  --enable-myfunctions           Enable myfunctions support])

之后去php源码跟目录，执行

    ./buildconf --force

如果提示autoconf版本不对的话，我原来的版本是2.69... 很郁闷，版本高了居然还不行。执行

    apt-get install autoconf2.59
    export PHP_AUTOCONF=autoconf2.59
    ./buildconf --force

切换到`ext/myfunctions`目录，执行：

    /usr/local/php/bin/phpize 
    ./configure --with-php-config=/usr/local/php/bin/php-config 
    make
    make install

再就是修改`php.ini`文件，在[extension]部分加入

    extension = "myfunctions.so"

之后重启php

    /etc/init.d/php-fpm restart

如果修改了函数，或者新增加函数，需要重新编译php

    make
    make install
    /etc/init.d/php-fpm restart

