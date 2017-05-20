title: "Lua教程二"
date: 2015-05-04 22:39:09
layout: post
comments: true
categories:
- 语言
- LUA
---

## 常用内置方法

- type 判断数据类型

    *type(arg)*
    接收一个参数， 返回值为string类型

``` lua
local a = 1
local b = "1"
local c = true

print(type(a), type(b), type(c))
```

    *返回值为八种数据类型其中之一："nil", "number", "string", "boolean", "table", "function", "thread", and "userdata"。*

    *注意返回的值都是字符串*

- print 打印信息

    *print(arg1, arg2, ...)*
    *可以接收任意个数的参数，不支持格式化输出，要格式化输出，使用string.format()转化后输出*

``` lua
local a = "Hello"
local b = "Lua"
print(a, b)
print(a .. "," .. b)
```

- tostring 转换成字符串类型

    *tostring(arg)*
    
- tonumber 转换成数值类型

    *tonumber(arg)*

- assert 断言

    *assert(v, msg)*
    *v为要判断的值，当v的值为false时， 打印错误，错误的描述为msg字符串*
    *当v的值为false时，打印错误，程序会终止运行*

``` lua
local i = 0
assert(i > 0, "can not run, i <= 0")
```

- pcall 在安全模式下调用方法

    *pcall(func, arg1, arg2, ...)*
    *可以通过pcall(func)以安全模式调用方法，方法func执行成功，pcall返回true, func执行失败，返回false和一个错误描述*

``` lua
function check()
    local id = 12
    assert(id > 10, "id can not less then 10")
end

local status, message = pcall(check)
if not status then
    print(message)
else
    print("success")
end
```

- xpcall 与pcall相似

    *xpcall(func, fh, arg1, arg2, ...)*
    *可以指定一个异常处理方法fh, 返回结果与pcall相同* 

- loadfile 加载外部lua文件
    
    *loadfile(filename)*
    *加载一个外部的lua文件， 返回一个function*

    ``` lua
    local runInit = loadfile("init.lua")
    runInit()
    ```

- dofile 执行外部lua文件

    *dofile(filename)*
    *加载一个外部的lua文件，并执行其中的语句, 返回值为外部lua文件的返回值*

    ``` lua
    local result = dofile("init.lua")
    ```

- require 加载外部模块

    *require(modname)*
    *加载外部外部模块， 返回值为模块的返回值，具体加载过程参考package库部分*

    ``` lua
    local user = require("user")
    user.hello()
    ```

## 全局变量

*_G*
    
*lua语言内部全局变量，类型为table, 系统内置方法、加载的库、全局变量都存在这里*

``` lua
xxxx = {1, 2, 3}
for k,v in pairs(_G) do
    print(k,v)
end
```

## 内置库（模块）

- package
    
    *管理通过require加载的模块*
    *当通过require加载模块时，按一下列过程查找模块*

    + 检查package.loaded, 若package.loaded[modname]不为nil,则返回 package.loaded[modname]的值
    + 若没有加载过，则通过以下四步查找模块
        - 查找package.preload里是否有对应的模块，有则返回，没有进入下一步
        - 根据package.path中的设置路径，拼合上模块的名称后，依次进行查找，找到后进行加载，找不到进入下一步
        - 与上一步相似，从package.cpath的路径中查找
        - 查找c库
    
    *package.path， package.cpath,决定了加载模块时的查找路径，可以通过修改它们的值来添加模块加载路径*

    ``` lua
    package.path = string.format("%s;%s", package.path, "./mods/?.lua")

    print(package.path)
    ```

    *package.loaded保存了已经加载的模块， 当通过require加载模块成功后，模块会自动添加进package.loaded中*

- table

    *table模块提供了对table的基本操作方法*

    + table.insert (list, [pos,] value)
        *向list中插入元素*
        *pos为要插入的位置，插入后pos之后的元素索引自动调整， 省略时，元素插入到list的末尾*

        ``` lua
        local t = {2, 4, 6, 8}
        table.insert(t, 10)
        table.insert(t, 3, 12)
        for i,v in ipairs(t) do
            print(i,v)
        end
        ```

    + table.remove (list [, pos])
        *移除list中的元素*
        *pos为要移除元素的位置，取值1到#list, 移除后pos之后元素的索引自动调整，省略时移除末尾的元素*
        
        ``` lua
        local t = {2, 4, 6, 8}
        table.remove(t, 3)
        for i, k in pairs(t) do
            print(i, k)
        end
        ```

    + table.move (a1, f, e, t [,a2])
        *移动table中的元素*
        *a1为源table, a2为目的table, a2省略时，a1同时为目的table*
        *f和e为源table中要移动的元素的起始和终止位置， t为为目的table中的起始位置*

        ``` lua
        local x = {"a", "b", "c", "d"}
        local y = {"e", "f", "g", "h"}

        -- 移动x中的第1，2个元素到第4个位置
        -- 移动后x中元素为a,b,c,a,b， 原来位置的元素被覆盖，长度也增加了
        table.move(x, 1, 2, 4)
        for i,v in pairs(x) do
            print(i,v)
        end

        -- 移动x中的b, c 到y的1，2位置
        -- 移动后y中的元素为b,c,g,h
        table.move(x, 2, 3, 1, y)
        for i,v in pairs(y) do
            print(i,v)
        end
        ```

    + table.concat (list [, sep [, i [, j]]])
        *连接list中的元素*
        *sep为连接字符串， i为起始位置， j为终止位置*

        ``` lua
        local t3 = {2, 4, 6, 8}
        print(table.concat(t3, "-"))
        print(table.concat(t3, "--", 2, 4))
        ```

    + table.sort (list [, comp])
        *对table的元素进行排序*
        *comp为排序函数，该函数接收两个参数，但函数返回值为true不交换传入的两个参数的位置，返回值为false则交换两个参数的位置*
        *comp省略时，默认使用以下排序函数：*
        ``` lua
        function(x, y)
            return x < y
        end
        ```

        ``` lua
        local t = {34, 29, 88, 99, 123}
        table.sort(t, function(a, b)
            return a > b
        end)
        for i,v in ipairs(t7) do
            print(i,v)
        end
        ```

    + table.pack (···)
        *把传入的参数打包成一个table，并加入一个元素"n", 值为打包进table的元素个数*

        ``` lua
        local a = 123
        local b = 234
        local c = 345
        local t = table.pack(a, b, c)
        for k,v in pairs(t) do
            print(k,v)
        end
        ```

    + table.unpack (list [, i [, j]])
        *把table 拆开, 只会返回数字索引元素的值*
        *i为起始元素的索引，j为终止元素的索引*

        ``` lua
        local t = {1, 2, 3, s = 4, 5}
        local x, y, z, n = table.unpack(t)
        print(x, y, z, n)
        ```

- string

    *对字符串进行处理*

    + string.byte(s, i, j)
    + string.char(...)
    + string.find (s, pattern [, init [, plain]])
    + string.dump (function [, strip])
    + string.format(format, ...)
    + string.gmatch (s, pattern)
    + string.gsub (s, pattern, repl [, n])
    + string.len (s)
    + string.lower (s)
    + string.match (s, pattern [, init])
    + string.pack (fmt, v1, v2, ···)
    + string.packsize (fmt)
    + string.rep (s, n [, sep])
    + string.reverse (s)
    + string.sub (s, i [, j])
    + string.unpack (fmt, s [, pos])
    + string.upper (s)


- math

    *数学常用函数库*

    + math.abs (x)

    + math.acos (x)

    + math.asin (x)

    + math.atan (y [, x])

    + math.ceil (x)

    + math.cos (x)

    + math.deg (x)

    + math.exp (x)

    + math.floor (x)

    + math.fmod (x, y)

    + math.log (x [, base])

    + math.max (x, ···)

    + math.min (x, ···)

    + math.modf (x)

    + math.rad (x)

    + math.random ([m [, n]])

    + math.randomseed (x)

    + math.sin (x)

    + math.sqrt (x)

    + math.tan (x)

    + math.tointeger (x)

    + math.type (x)

    + math.ult (m, n)

- io

    *io库提供对标准输入输出以及文件的基本操作*

    + io.close ([file]) 关闭打开的文件，省略参数时，关闭当前默认的输出文件

    + io.flush () 把对文件进行的改动写到文件中， 等同于io.output():flush()

    + io.input ([file]) 设置文件为默认的输入文件，省略参数时设置，返回当前默认的输入文件

    + io.lines ([filename ···]) 打开文件并返回一个行遍历器
    
    + io.open (filename [, mode]) 以指定的模式打开一个文件，返回文件句柄
    
        *打开方式：*

        - "r": 只读（默认）
        - "w": 只写
        - "a": 追加
        - "r+": 更新，先前的内容保留
        - "w+": 更新，先前的内容清空
        - "a+": 从末尾添加

    + io.output ([file]) 设置文件为默认的输出文件，省略参数时设置，返回当前默认的输出文件

    + io.popen (prog [, mode]) 在新的进程中打开文件， 不是在所有的平台上可用

    + io.read (···) 读取文件的内容

    + io.tmpfile () 创建一个堆里零时文件，返回这个文件的句柄，程序结束后会自动删除这个文件

    + io.type (obj) 检测obj是否是一个打开的文件句柄，返回值为"file", "closed file", nil

    + io.write (···) 向当前输出文件写入内容

- os

    *封装了一些秕方法*

    + os.clock () 返回CPU时间的近似值

    + os.date ([format [, time]]) 返回系统日期

        *返回两种形式的结果， 参数字符串为'*t'时，返回table, 参数为格式化字符串时，返回日期的字符串*
        *格式化参数*

        - %a 星期几的简写
        - %A 星期几的全称
        - %b 月份的简写
        - %B 月份的全称
        - %c 标准的日期的时间串
        - %C 年份的前两位数字
        - %d 十进制表示的每月的第几天
        - %D 月/天/年
        - %e 在两字符域中，十进制表示的每月的第几天
        - %F 年-月-日
        - %g 年份的后两位数字，使用基于周的年
        - %G 年份，使用基于周的年
        - %h 简写的月份名
        - %H 24小时制的小时
        - %I 12小时制的小时
        - %j 十进制表示的每年的第几天
        - %m 十进制表示的月份
        - %M 十时制表示的分钟数
        - %n 新行符
        - %p 本地的AM或PM的等价显示
        - %r 12小时的时间
        - %R 显示小时和分钟：hh:mm
        - %S 十进制的秒数
        - %t 水平制表符
        - %T 显示时分秒：hh:mm:ss
        - %u 每周的第几天，星期一为第一天 （值从1到7，星期一为1）
        - %U 第年的第几周，把星期日作为第一天（值从0到53）
        - %V 每年的第几周，使用基于周的年
        - %w 十进制表示的星期几（值从0到6，星期天为0）
        - %W 每年的第几周，把星期一做为第一天（值从0到53）
        - %x 标准的日期串
        - %X 标准的时间串
        - %y 不带世纪的十进制年份（值从0到99）
        - %Y 带世纪部分的十制年份
        - %z，%Z 时区名称，如果不能得到时区名称则返回空字符。
        - %% 百分号

    ``` lua
    print(os.date("%D"))
    print(os.data())
    ```

    + os.time ([table])返回时间值

        *不带参数时返回当前时间值，有参数时把传入的时间转为时间值*

        ``` lua
        print(os.time())
        print(os.time({year = 2015, month = 3, day = 19, hour = 12, min = 0, sec = 0}))
        ```

    + os.difftime (t2, t1) 返回时间差，单位为秒（t2-t1)

    + os.execute ([command]) 执行系统命令

    + os.exit ([code [, close]]) 终止宿主程序

    + os.getenv (varname) 返回当前程序的环境变量

    + os.remove (filename) 删除文件

    + os.rename (oldname, newname) 文件重命名

    + os.setlocale (locale [, category]) 设置程序的当前设置

    + os.tmpname () 返回一个零时文件名，在unix-like系统上，会创建相应的文件

- utf8

    *提供对utf8编码的支持*

- debug

    *提供一些与调试相关的方法*



