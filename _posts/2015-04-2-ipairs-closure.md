---
layout: post
title: "ipairs vs pairs and closure "
description: ""
category: Game
tags: [Game]
---

##ipairs vs pairs

标准库提供了集中迭代器，包括迭代文件每行的(io.lines)，迭代table元素的(pairs)，迭代数组元素的(ipairs)，迭代字符串中单词的(string.gmatch)等等。
在这里只对lua中的ipairs和pairs做一个对比。

LUA手册中这样写的：
###ipairs (t)
Returns three values: an iterator function, the table t, and 0, so that the construction
for i,v in ipairs(t) do body end
will iterate over the pairs (1,t[1]), (2,t[2]), ···, up to the first integer key absent from the table.
顺便翻译下，看看最近英语有没有进步。
ipairs(t)
返回三个值：一个迭代器函数，一个table t，一个0。因此像如下结构:
for i,v in iapirs(t) do  <函数体> end 
会迭代得到(1,t[1]),(2,t[2]),.....一直迭代到table中没有的第一个整数索引(key).



###pairs (t)
Returns three values: the next function, the table t, and nil, so that the construction
for k,v in pairs(t) do body end
will iterate over all key–value pairs of table t.
See function next for the caveats of modifying the table during its traversal.
这个函数返回三个值：一个next函数，一个table t ，一个nil。像如下结构：
for k, v in pairs(t) do <函数体> end 
会迭代table t 中所有的key值(数字key和字符(串)索引)和它所对应的值。
参考函数next可以看到在遍历过程中修改table的注意事项。

 
这样就可以看出  ipairs以及pairs 的不同。
pairs可以遍历表中所有的key，并且除了迭代器本身以及遍历表本身还可以返回nil;

但是ipairs则不能返回nil,只能返回数字0，如果遇到nil则退出。它只能遍历到表中出现的第一个不是整数的key
下面举个例子来来更直观的体会差别：

local tabFiles = {

[3] = "test2",

[6] = "test3",

[4] = "test1"

}


for k, v in ipairs(tabFiles) do

print(k, v)

end

猜测它的输出结果是什么呢？

根据刚才的分析，它在 ipairs(tabFiles) 遍历中，当key=1时候value就是nil，所以直接跳出循环不输出任何值。
>lua -e "io.stdout:setvbuf 'no'" "Test.lua"
>Exit code: 0

 

那么，如果是
for k, v in pairs(tabFiles) do

print(k, v)

end

则会输出所有 ：
>lua -e "io.stdout:setvbuf 'no'" "Test.lua" 
3 test2
6 test3
4 test1
>Exit code: 0

现在改变一下表内容，
local tabFiles = {
[1] = "test1",
[6] = "test2",
[4] = "test3"
}
for k, v in ipairs(tabFiles) do
print(k, v)
end
现在的输出结果显而易见就是key=1时的value值test1
key等于2时返回nil跳出循环不输出任何值。



##关于匿名函数和closure的关系。
 
一个匿名函数（anonymous）仅仅是一个函数没有名字。一个closure是一个函数可以捕获到他附近的环境（包含它的函数的局部变量）。一个匿名函数没有必要创建成一个closure（不需要让它捕获附近变量），而一个closure目的不是为了匿名（必须匿名），是为了捕获它附近的环境。

function newcounter()
 local i = 0
   return function ()
	        i = i + 1 
			return i 
		  end 
end 		
c1 =newcounter()
print(c1()) ------>1
print(c1())  ----->2
如果给匿名函数加一个名字(例如加一个name)就会出现问题。
1>c:\users\***\desktop\\a.lua(3): error:'(' expected near 'name'
所以推断closure必须匿名，不知道对不对！





