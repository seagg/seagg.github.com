---
layout: post
title: "Lua中实现php序列化及反序列化函数"
date: 2012-12-22 15:07
comments: true
categories: Lua
tags: 
---
好高兴啊，昨天翻遍了lua自带的库，又在网上找了一天如何在lua中序列化以及反序列化，但结果都不是我要的，因为我要在lua中反序列化由php序列化函数序列化的一个字符串，比如这样的：

```php
a:9:{s:10:"comment_id";s:1:"1";s:7:"user_id";s:7:"7082457";s:9:"user_name";s:9:"putulbaby";    
s:11:"user_avatar";s:68:"http://avatar.bbs.miui.com/data/avatar/001/05/37/38_avatar_small.jpg";       
s:15:"comment_content";s:63:"喜欢蓝色，就这么定了～～～～～～～～～～～";s:13:"comment_grade";s:1:"1";    
s:13:"average_grade";s:3:"5.0";s:6:"up_num";s:1:"0";s:13:"reply_content";s:0:"";}
```   
但网上一些函数都只是把lua中table类型的数据转换成字符串，如此而已，而且转换成的字符串样式各异，都没办法解析如上的字符串成数组。。。    
本来想今天写一个的，无意之间发现昨天临走的时候打开的一个页面中有这样的一个函数，仔细看了看，似乎很满足我的需求，运行了一次之后，果然把上面的字符串解析了出来！！但好像有一个问题，就是如果传进去的数组中含有值为数字的字符串，序列化之后就识别成数字而不是字符串。。。 代码如下：    

```lua
Lua port of PHP serialization functions http://gitorious.org/nutria/seawolf/blobs/ae2d5e6919cff5754f9ed5f04c50f70070300f9e/variable/serialize/serialize.lua source code
--[[
  @file
  Lua port of PHP serialization functions.
  
  Port based on PHPSerialize and PHPUnserialize by Scott Hurring
  http://hurring.com/scott/code/python/serialize/v0.4
  
  @version v0.1 BETA
  @author Fernando P. García; fernando at develcuy dot com
  @copyright Copyright (c) 2009 Fernando P. García
  @license http://opensource.org/licenses/gpl-license.php GNU Public License
  
  $Id$
]]
local _serialize_key, _read_chars, _read_until, _unknown_type

function _serialize_key(data)
  --[[
  Serialize a key, which follows different rules than when 
  serializing values.  Many thanks to Todd DeLuca for pointing 
  out that keys are serialized differently than values!
  
  From http://us2.php.net/manual/en/language.types.array.php
  A key may be either an integer or a string. 
  If a key is the standard representation of an integer, it will be
  interpreted as such (i.e. "8" will be interpreted as int 8,
  while "08" will be interpreted as "08"). 
  Floats in key are truncated to integer. 
  ]]
  -- Integer, Long, Float
  if type(data) == 'number' then
    return 'i:' .. tonumber(data) .. ';'
  -- Boolean => integer
  elseif type(data) == 'boolean' then
    if data then
      return 'i:1;'
    else
      return 'i:0;'
    end
  -- String => string or String => int (if string looks like int)
  elseif type(data) == 'string' then
    if tonumber(data) == nil then
      return 's:' .. string.len(data) .. ':"' .. data .. '";'
    else
      return 'i:' .. tonumber(data) .. ';'
    end
  
  -- None / NULL => empty string
  elseif type(data) == 'nil' then
    return 's:0:"";'
  
  -- I dont know how to serialize this
  else
    error('Unknown / Unhandled key  type (' .. type(data) .. ')!')
  end
end

function serialize(data)
  --[[
  Serialize a value.
  ]]
  local i, out, key, value
  -- Numbers
  if type(data) == 'number' then
    -- Integer => integer
    if  math.floor(data) == data then
      return 'i:' .. data .. ';'
    -- Float, Long => double
    else
      return 'd:' .. data .. ';'
    end
  -- String => string or String => int (if string looks like int)
  -- Thanks to Todd DeLuca for noticing that PHP strings that
  -- look like integers are serialized as ints by PHP 
  elseif type(data) == 'string' then
    if tonumber(data) == nil then
      return 's:' .. string.len(data) .. ':"' .. data .. '";'
    else
      return 'i:' .. tonumber(data) .. ';'
    end
  -- Nil / NULL
  elseif type(data) == 'nil' then
    return 'N;'
  -- Tuple and List => array
  -- The 'a' array type is the only kind of list supported by PHP.
  -- array keys are automagically numbered up from 0
  elseif type(data) == 'table' then
    i = 0
    out = {}
    -- All arrays must have keys
    for key, value in pairs(data) do
      table.insert(out, _serialize_key(key))
      table.insert(out, serialize(value))
      i = i + 1
    end
    return 'a:' .. i .. ':{' .. table.concat(out) .. '}'
  -- Boolean => bool
  elseif type(data) == 'boolean' then
    if data then
      return 'b:1;'
    else
      return 'b:0;'
    end
  --~ TODO:
  --~ -- Table + Functions => stdClass
  --~ elseif type(data) == 'function' then
  --~ # I dont know how to serialize this
  else
   error('Unknown / Unhandled data type (' .. type(data) .. ')!')
  end
end

function _read_until(data, offset, stopchar)
  --[[
  Read from data[offset] until you encounter some char 'stopchar'.
  ]]
  local buf = {}
  local char = string.sub(data, offset + 1, offset + 1)
  local i = 2
  while not (char == stopchar) do
    -- Consumed all the characters and havent found ';'
    if i + offset > string.len(data) then
      error('Invalid')
    end
    table.insert(buf, char)
    char = string.sub(data, offset + i, offset + i)
    i = i + 1
  end
  -- (chars_read, data)
  return i - 2, table.concat(buf)
end

function _read_chars(data, offset, length)
  --[[
  Read 'length' number of chars from data[offset].
  ]]
  local buf = {}, char
  -- Account for the starting quote char
  -- offset += 1
  for i = 0, length -1 do
    char = string.sub(data, offset + i, offset + i)
    table.insert(buf, char)
  end
  -- (chars_read, data)
  return length, table.concat(buf)
end

function unserialize(data, offset)
  offset = offset or 0
  --[[
  Find the next token and unserialize it.
  Recurse on array.
  offset = raw offset from start of data
  --]]
  local buf, dtype, dataoffset, typeconvert, datalength, chars, readdata, i,
         key, value, keys, properties, otchars, otype, property
  buf = {}
  dtype = string.lower(string.sub(data, offset + 1, offset + 1))
  -- 't:' = 2 chars
  dataoffset = offset + 2
  typeconvert = function(x) return x end
  datalength = 0
  chars = datalength 
  -- int or double => Number
  if dtype == 'i' or dtype == 'd' then
    typeconvert = function(x) return tonumber(x) end
    chars, readdata = _read_until(data, dataoffset, ';')
    -- +1 for end semicolon
    dataoffset = dataoffset + chars + 1
  -- bool => Boolean
  elseif dtype == 'b' then
    typeconvert = function(x) return tonumber(x) == 1 end
    chars, readdata = _read_until(data, dataoffset, ';')
    -- +1 for end semicolon
    dataoffset = dataoffset + chars + 1
  -- n => None
  elseif dtype == 'n' then
    readdata = nil
  -- s => String
  elseif dtype == 's' then
    chars, stringlength = _read_until(data, dataoffset, ':')
    -- +2 for colons around length field
    dataoffset = dataoffset + chars + 2
    -- +1 for start quote
    chars, readdata = _read_chars(data, dataoffset + 1, tonumber(stringlength))
    -- +2 for endquote semicolon
    dataoffset = dataoffset + chars + 2
    --[[
    TODO
    review original: if chars != int(stringlength) != int(readdata):
    ]]
    if not (chars == tonumber(stringlength)) then
      error('String length mismatch')
    end
  -- array => Table
  -- If you originally serialized a Tuple or List, it will
  -- be unserialized as a Dict.  PHP doesn't have tuples or lists,
  -- only arrays - so everything has to get converted into an array
  -- when serializing and the original type of the array is lost
  elseif dtype == 'a' then
    readdata = {}
    -- How many keys does this list have?
    chars, keys = _read_until(data, dataoffset, ':')
    -- +2 for colons around length field
    dataoffset = dataoffset + chars + 2
    -- Loop through and fetch this number of key/value pairs
    for i = 0, tonumber(keys) - 1 do
      -- Read the key
      key, ktype, kchars = unserialize(data, dataoffset)
      dataoffset = dataoffset + kchars
      -- Read value of the key
      value, vtype, vchars = unserialize(data, dataoffset)
      -- Cound ending bracket of nested array
      if vtype == 'a' then
        vchars = vchars + 1
      end
      dataoffset = dataoffset + vchars
      -- Set the list element
      readdata[key] = value
    end
  -- object => Table
  elseif dtype == 'o' then
    readdata = {}
    -- How log is the type of this object?
    chars, otchars = _read_until(data, dataoffset, ':')
    dataoffset = dataoffset + chars + 2
    -- Which type is this object?
    otype = string.sub(data, dataoffset + 1, dataoffset + otchars)
    dataoffset = dataoffset + otchars + 2
    if otype == 'stdClass' then
      -- How many properties does this list have?
      chars, properties = _read_until(data, dataoffset, ':')
      -- +2 for colons around length field
      dataoffset = dataoffset + chars + 2
      -- Loop through and fetch this number of key/value pairs
      for i = 0, tonumber(properties) - 1 do
        -- Read the key
        property, ktype, kchars = unserialize(data, dataoffset)
        dataoffset = dataoffset + kchars
        -- Read value of the key
        value, vtype, vchars = unserialize(data, dataoffset)
        -- Cound ending bracket of nested array
        if vtype == 'a' then
          vchars = vchars + 1
        end
        dataoffset = dataoffset + vchars
        -- Set the list element
        readdata[property] = value
      end
    else
      _unknown_type(dtype)
    end
  else
    _unknown_type(dtype)
  end
  --~ return (dtype, dataoffset-offset, typeconvert(readdata))
  return typeconvert(readdata), dtype, dataoffset - offset
end
-- I don't know how to unserialize this

function _unknown_type(type_)
  error('Unknown / Unhandled data type (' .. type_ .. ')!', 2)
end
```

之后还遇到一个问题，我的序列化的数据是从redis中通过lrange方法读出来的，我问了老板，读进去的时候已经被php序列化了，但读出来的我认为是序列化的数据用lua中的type函数检测后发现是table类型的。。。这样就不能直接套用unserialize方法进行反序列化了。之后我用lua中便利数组的方法 for key, value in pairs(lists) do print(key .. value) end ,发现序列化的值在value当中，这样就好办了，直接把value反序列化就可以了。好辛苦。。。。
