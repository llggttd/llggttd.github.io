title: "Lua教程一"
date: 2015-05-04 22:38:09
update: 2017-05-20T17:26:22+08:00
layout: post
comments: true
categories:
- 语言
- LUA
---

## 变量

- 动态类型， 变量无类型， 值才有类型
- 区分大小写
- 不要以'_大写字母'开头命名变量，这是lua 内部变量的全名规则
- 未初始化的变量的值为nil (demo)
- 多重赋值， 可以不对称 (demo)

##数据类型

- 共8种数据类型
- 都是一等公民，可以做作为参数， 也可以作为返回值

###number

    数值类型， 1.2, 10等
    lua 5.3之前， 无论是整形还是浮点运算时都转换成浮点型

###string

    "hello", "hello\n world"

###boolean

    true, false
    只有nil, false的值为假，其它值都为正
    *注意: 0， ""， {}的值都是true*

###table

    存储键、值对，
    {
        key = value,
        key = value,
        ...
    }
    键可以为除了nil以外的其它任意类型
    当键对应的值为nil时，这对键值会被忽略


``` lua
info = {
    name = "uniflor",
    4 = "TianJin",
    callback = function()
        print("ok")
    end,
    status = true,
    size = {
        width = 200,
        height = 100
    }
}
```

    *在对table进行遍历时，字符串索引的元素位置与书写的顺序无关*

###function

    *同其它语言中的函数*
    *两种定义方式*
    *参数可变，按顺序对应*
    *可以返回多个值*

``` lua
function test()
    local a = 1
    local b = 2
    return a, b
end

local test = function(x, y)
    local a = 1 + x
    local b = 2 + y
    return a, b
end
```

###nil

    定义变量，不赋值时，系统会自动赋值 nil
    变量没有值，变量未定义，记住写法，"nil"

###userdata

    代表c中内存的一块原生数据或指向原生数据的指针

###thread

    Lua中协程程序中的线程，与所运行的平台无关


与类型相关的方法：
    - type

-------

## 关键词

    and       break     do        else      elseif    end
    false     for       function  goto      if        in
    local     nil       not       or        repeat    return
    then      true      until     while

## 语法标记

    +     -     *     /     %     ^     #
    &     ~     |     <<    >>    //
    ==    ~=    <=    >=    <     >     =
    (     )     {     }     [     ]     ::
    ;     :     ,     .     ..    ...

*与其它语言不同的地方：*

* //
* ~=
* \#
* ..
* ...

## 运算符
  
* +: 加
* -: 减
* *: 乘
* /: 除
* //: 取商运算
* %: 取余运算
* ^: 乘方
* -: 一元减

## 逻辑运算

* and 逻辑与
* or  逻辑或
* not 逻辑非

*没有&&, ||, !操作符*

* ==: 相等 先判断类型是否相同，再判断值是否相同
* ~=: 不相等
* <: 小于
* \>: 大于
* <=: 小于等于
* \>=: 大于等于

``` lua
if id >= 10 and price <5 then
    print("")
end

local exist = false
if not exist then
    print("do not exist")
end
```


## 流程控制

- if-else-end 结构

    *注意语句体前面的then，这与java, C不同*

``` lua
local a
if a > 100 then
    ...
elseif a > 50 then
    ...
else
    ...
end
    ```

- while-do-end 结构

    *在while-do-end中可以使用break跳出循环，但没有continue语句*

``` lua
local a = 10
while a > 0 do
    ...
    a = a - 1
end
```

- repeat-until 结构

    *在repeat-until中，until之后的表达式与repeat中的语句在一个语句体中，所以在until中可以引用repeat中语句块的局部变量, 而在while-do-end结构中不行*

``` lua
local u = 10
repeat
    ...
    u = u - 1
until u < 0
```

``` lua
local u = 10
repeat
    local j = u
    u = u - 1
    print(j)
until j < 0
```

- for-do-end 结构

    *第一个参数是起始值*
    *第二个参数是终止值*
    *第三个参数是步进值, 可以省略，默认为1*
    *循环变量前虽没有local修饰，但也是for-do-end语句体中的局部变量*

``` lua
for i = 1, 10, 3 do
    print(i)
end
print(i)
```


- for-in-do-end 结构

    *lua中两种table的遍历结构*
    *ipairs()只遍历table中以数字为索引的元素*
    *paires()遍历table中所有的元素*

``` lua
local user = {12, 14, 15, name = "max"}
for i,v in ipairs(user) do
    print(i,v)
end
```

``` lua
local user = {12, 14, 15, name = "max"}
for k,v in pairs(user) do
    print(k,v)
end
```



