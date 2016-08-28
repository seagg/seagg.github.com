---
layout: post
title: "Lua中通过ffi调用c的结构体变量"
date: 2012-12-24 18:08
comments: true
categories: Lua
tags: ffi
---
``` lua
local ffi = require("ffi")
ffi.cdef[[
   typedef struct { 
     uint8_t id;
     char * name; 
   } stuInfo;
]]--新建一个结构体
local n = 1
local stu = ffi.new("stuInfo[?]", n)--新建变长的结构体变量
for i=0, n-1 do
   stu[i].id = 1;
   local name = "zhao";
   local ptr=ffi.cast("char *", name);--将lua中字符串转换为c的`char *`
   stu[i].name = ptr;
end

print(stu[0].id);
local myname = ffi.string(stu[0].name);--将c中`char *`类型转换为lua的字符串
print(myname);
```