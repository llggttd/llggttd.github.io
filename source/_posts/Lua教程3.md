title: "Lua教程三"
date: 2015-05-04 22:46:09
layout: post
comments: true
categories:
- 语言
- LUA
---

## 面向对象

*lua中本是没有对象这个东西的。但由于lua中有table这一强大的数据结构，
我们可以通过table来实现面向对象的结构*

``` lua
user = {name = "max", type = "admin", hi = function(user)
    print("Hello, I am " .. user.name)
end}

-- 获取属性
local type = user.type

-- 调用方法
user.hi(user)
```

*这种结构看起来比较怪异，不符合我们一般对面向对象中类的认识，可以改进以下结构*

``` lua
User = {}

User.type = "admin"

function User.new(name)
    User.name = name
    return User
end

function User.hi()
    print("Hi, I am " .. User.name)
end

local user1 = User.new("max")
user1.hi()
```

*这种结构起来要正常多了，但会有一个问题，在这段代码下再加几行*

``` lua
local user2 = User.new("xxx")
user2.hi() -- 输出 "Hi, I am xxx"
user1.hi() -- 输出 "Hi, I am xxx"
```

*创建新的实例user2后，表User的name被修改了，而user1引用的还是User，所以出错了。为了解决这个问题，再次改进一下结构*

``` lua
User = {}
User.type = "admin"

function User.new(self, name)
    instance = {}
    instance.name = name
    -- 这里self指向的是表User, 表insance以User为模板
    setmetatable(instance, self)
    self.__index = self
    return instance
end
function User.hi(self)
    print("Hi, I am " .. self.name)
end

local user1 = User.new(User, "max")
user1:hi(user1)

local user2 = User.new(User, "xxx")
user2.hi(user2)

user1.hi(user1)
```

*现在结构清晰多了，而且创建的实例之间也不会互相影响了，但是这种结构需要在调用类的方法时把自己当成第一个参数传递给方法，写起来有些麻烦，所有lua中引入了新的操作符号":"。通过":"调用方法时会默认添加一个参数self, 指向调用者。对以下代码再次改进：*

``` lua
User = {}
User.type = "admin"

function User:new(name)
    instance = {}
    instance.name = name
    setmetatable(instance, self)
    self.__index = self
    return instance
end
function User:hi()
    print("Hi, I am " .. self.name)
end

local user1 = User:new("max")
user1:hi()

local user2 = User:new("xxx")
user2:hi()

user1:hi()
```
    


