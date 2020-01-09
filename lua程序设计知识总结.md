#一.LUA简介
- lua是一种**弱类型的动态脚本语言;**
- lua提供了一个虚拟机,代码最终都是以字节码的形式有虚拟机解释执行的.把外部组织好的代码置入内存,让虚拟机解析运行,需要有一个源代码解释器,或者预编译字节码的加载器. 
- Lua不能独立运行

**解释型语言**
`程序不需要编译,程序在运行时才翻译成机器语言,每执行一次都要翻译一次.因此效率较低;但同时依赖解释器,跨平台性好;` 
&ensp;&ensp;官方源码大致分为四部分:
1. 虚拟机运转的核心功能
2. 源代码解析以及预编译字节码
3. 内嵌库
4. 可执行的解析器,字节码编译器

* lua是从上向下一行行执行的,所以调用函数等,需要是已经定义过的才行;否则调用的将是nil
* 语句之间分隔: ; / 空白
* 语句块: do  end
* 赋值语句: 多重赋值 a,b,c = 1,2,3 -- 在多重赋值中，先将等号右边的值进行运算，之后一一放入左边变量中；
* 变量默认为全局变量

&ensp;注释
```
单行: --
多行: --[[ ]] (可以嵌套)
```
#二.类型与值
Lua的索引都是从1开始
type() 		-- 函数返回变量类型(返回的是一个string)

类型|名字|说明
-|-|-
nil     | 空        | 既是类型,也是值;删除某个变量,只需赋值nil即可; 所有未初始化的变量都是nil；
boolean | 布尔      | true/false
number  | 数字      |可以使用科学计数法3.2e2,表示3.2*10的2次方；
string  | 字符串    | ' 和 " 都表示字符串：其字符串是不可变得值,若需要修改,则重新创建字符串
table   | 表        |是一个对象(hash)，通过构造表达式创建
function| 函数      | 第一类值，可以将函数名作为变量使用，或作为参数，或作为返回值；
userdata| 自定义类型|
thread  | 线程      |并不是不是真正的抢先式多线程


* lua只有nil和false是假|;0，""在判断真假时，为真，
* \ddd 	:数值转换成字符；
* “10”+1	：字符串参与运算会自动字符串转化成number；
* [[ ]] 		:存放多行字符串
* ..			:字符串连接符(操作符左右有数字会自动将数值转换为字符串)
* t = {}    	: 创建了一个table,lua的赋值即是构造
* #t :table最大索引值(数组部分)

#三.表达式
1. 算术：+ 、-、*、/ 、%、 ^(乘幂)
平方根：x^0.5 ,平方: x^2
得到数的小数部分：X%1 
整数部分：x-x%1
取得小数点两位后的小数部分X%0.01
2. 关系 ：<,>,<=,>=,==,    ~=(不等于)
3. 逻辑 : and, or, not

4 and 5 :为真返回第二个数5；
nil and 5:为假返回nil；
x = x or v --等价于 if not x then x = v end
Max = (a > b) and a or b   -- 等价于c语言 的a>b?a:b
4. 优先级
一级：^				二级:not # -(一元)
三级:* / %			四级: + -
五级: ..			六级: <  > 	>= 	<= 	~=   ==
七级:and			八级:or


#四.语句
1.local -- 局部变量
2.if语句：if (条件) then (语句) elseif  (条件) then  (语句) else (语句) end
3.while  (条件true)  do (语句) end
4.repeat (语句) until (条件true) 	-- 重复直到为真；   
5.for：
for var=i,n,k do (语句) end
-- k是步长,(可选的)，若不写则默认k为1,从i到n;若不想设置上限，使用常量math.huge;
-- 注意:控制变量会在开始前一次性求值，并自动声明为for语句的局部变量；

for i,v in ipairs(a) do (语句) end -- 是通过iterator来遍历所有值。
-- 迭代文件每行(io.lines)/迭代table元素(pairs)/迭代数组(ipairs)/迭代字符串单词	(string.gmatch).

6.break/return -- 只能是块的最后一条语句，或者end/else/until之前的一条语句。若想添进其他语句之间需要用do return end 进行包装；
--注意：lua没有continue；


#五.函数
1. ： 			-- 冒号操作符 o.foo(o,x)-----等价于-----o:f(x)
2. 函数可以有多个返回值，以逗号隔开；形参也可多可少;多个返回值多重赋值给变量，若变量数量多了，则以nil填补，若返回值多了，则后面的丢弃；
3. 函数返回值若没有，则返回nil，若函数调用不是最后一个元素，则只返回第一个值；函数调用加上括号，也只返回第一个值；
4. unpack 		-- 接受一个数组作为参数，并将所有元素依次从下标1开始返回；主要用途:泛型调用
5. ...			-- 变长参数 接受不同数量的实参，函数中访问也是用 "..."
6. select(s, ...)-- 函数：变长参数中可能存在一些nil，此时需要select使用函数访问变长参数，否则nil后的参数将不会访问；
				-- 调用select时,需传入一个固定实参s,若s为数字n,则select返回第n个可变实参;
				-- 否则,selector只能为”#”,返回边长参数的总数；
select('#', ...)-- 返回变长参数的总长(包含nil);

7. #长度操作符:将其放在字符串前，可得到字符串长度；放在table前得到最大索引值

+ 基本常用函数
tonumber(e [, base]) 	-- 参数e转换为数字,当不能转换时返回nil;base(2~36)指出参数e当前使用的进制，默认10进制
tostring() 				-- 转换成字符串；此函数将会触发元表的__tostring事件
type()					-- 返回参数的类型名("nil"，"number", "string", "boolean", "table", "function", "thread", "userdata")
print()					-- 简单的以tostring方式格式化输出参数的内容
next(table[,index]) 	-- 返回table的索引index的下一个值,index为nil,返回当前的table的第一个值;当索引号为最后一个索引或表为空时将返回nil
注意:table的判空next(t) == nil


#六.函数进阶:
f=function() 等价于 function f()
1.第一类值(与传统类型具有相同权利)：可以存储到变量中；有特定的词法域：内部函数可以访问外部函数中的变量。
2.函数与其他值一样都是匿名的，
3.高阶函数：就是参数由函数构成，
4.闭合函数(closure)：就是一个函数加上该函数所需访问的所有非局部变量（upvalue）;当没有非局部变量时，closure就是一个function；Lua中的函数都是closure,
5.沙盒（sandbox）:使用相同的技术来创建一个安全的运行环境；将一些不安全的代码放入一个安全的环境中(封装出一层能对代码进行检测的环境)进行运行;
6.非全局的函数(non -global function):local f = function() / local function f()
7.正确的尾调用(proper tail call): 尾调用就是当一个函数调用的是另一个函数的最后一个动作;lua中正确的是形式为:"return <fun>(<arg>)"
	lua支持尾调用消除,正确的尾调用可以有效减少栈的开消，减轻函数调用的层次。
  	尾调用消除：当在进行尾调用时不消耗任何栈空间，这种实现就称为尾调用消除，所以一个程序可以有无数嵌套的尾调用。


#七.迭代器与泛型for
1.迭代器Iterator:一种可以遍历一种集合中所有元素的机制;lua的迭代器本质表示为闭合函数;
2.泛型for在循环过程内部保存了迭代器函数(迭代函数,恒定状态,控制变量).
3.泛型for <var-list> in <exp-list> do <body> end	
var-list 为n个变量名列表,<exp-list> n个表达式列表;(多个表之间以逗号隔开)
变量列表的第一元素为控制变量:当为nil时循环结束;
4.for的初始化及循环内部原理:首先对in之后的表达式求值,
返回上述三个值供for保存(仅保存三个变量,不足则以nil补足,第一个为nil则结束);
之后for将状态和变量作为参数循环调用迭代函数返回值赋予变量列表中的变量;

5.无状态的迭代器:自身不保存任何状态的迭代器(eg:ipairs),避免创建新的闭合函数开销
-- ipairs
local function iter(a, i)
	i=i+1
	local v = a[i]
	if v then
		return i,v
	end
end

function ipairs(a)
	return iter,a,0
end

-- pairs
function pairs(t)
	return next, t, nil
end


-- 遍历链表的迭代器,小技巧:将链表头节点作为恒定状态,当前节点作为控制变量
local function getnext(list, node)
	if not node then
		return list		
	else
		return node.next
	end
end

function traverse(list)
	return getnext, list, nil
end

6.尽可能的尝试编写无状态迭代器,其次不能则选择closure,而不要编写使用table的迭代器;
创建一个closure比创建一个table廉价;访问upvalue也比table更快;
协程编写迭代器功能最强,但稍微有点开销;



#八.编译、执行与错误
1.编译:
dofile 		-- 运行lua代码块,实际上dofile是一个辅助函数,loadfile才负责核心的工作;
loadfile 	-- 从文件加载lua代码块,单步运行,只编译代码,将编译结果作为一个函数返回;
			-- 与dofile不同的点,loadfile不会引发错误,只返回错误的值但不处理;
function dofile(filename)
	local f = assert(loadfile(filename))
	return f()
end

loadstring -- 从字符串读取代码,而非文件,返回一个函数;
注意:首先,loadstring功能强大,但也是一个开销比较大的函数,并且可能导致难以理解的代码;
其次,loadstring在重新编译时不涉及词法域,loadstring总是在全局环境中编译它的字符串;
i=32
local i = 0
f = loadstring("i = i + 1; print(i)")
g = function() i = i + 1; print(i) end
f()		-- > 33		-- 不涉及词法域
g()		-- > 1

load --loadfile和loadstring的底层函数,接受一个"读取器函数",并在内部调用它来获取程序块;
读取器函数可以分多次返回一个程序块,load会反复调用直至返回nil;
一般很少使用load,特殊情况下:程序块过大或者不在文件中;
注意:lua将所有独立的程序块视为一个匿名函数的函数体,并具有一个变长参数.

2.Lua提供的有关动态链接的功能都聚集在一个函数中,即package.loadlib.
该函数有两个字符串函数:动态库的完整路径和一个函数名称.
例子:
local path = "/user/socket.so"
local f = package.loadlib(path, "luaopen_socket")

3.error
assert -- 内建函数,检测第一个参数是否为true,为true返回true;否则引发错误,第二个参数为信息字符串(可选)



#九.协同程序coroutine
1.原理:有一个条执行序列,拥有自己独立的栈、局部变量和指令指针,同时又与其他协程共享全局变量和其他大部分东西;
但协程任意时刻只能执行一个协程,切正在运行的协程只会在显示的要求挂起时,它的执行才会暂停.
2.lua将所有有关协程的函数放置在一个coroutine的table中;
协程的四种状态:挂起(suspended)/运行(running)/死亡(dead)/正常(normal)
当A协程唤醒另一个协程B时,协程a就处于normal状态
coroutine.create(f())		-- 创建协程,返回一个thread类型的值;当前处于挂起
coroutine.status(thead)		-- 查询协程的状态
coroutine.resume(thead)		-- 启动/再次启动一个协程的执行,并修改状态由挂起到运行;执行成功返回true
注意:resume是在保护模式中运行的,因此lua不会显示其错误消息;
coroutine.yield()			-- 挂起
lua提供的是一种完整的"非对称的协同程序"(semi-coroutine):即lua提供了两个函数控制协程的执行,一个用于挂起执行,一个用于恢复执行.
3.管道(pipe)与过滤器(filter)
coroutine.wrap()			-- 唤醒协程..

4.非抢先式的(non-preemptive)多线程
协程与常规多线程区别:协程是非抢先式的,即当一个协程运行时,无法从外部停止它.
这样协程的同步就是显式的,但有若一个非抢先式的线程的一条线程调用了阻塞(blocking)操作,
整个程序都会在阻塞完成前停止.这对很多程序是无法接受的.
解决办法:



#十、完整的示例
1.数据描述:
2.马尔科夫链原理:是一种数学相关的概率统计理论.
简单的说,以一个状态机为例,n个前序状态影响到达的后序状态称为这个状态序列为n阶马尔科夫链.
应用:自然界的状态演算,google的搜索用户习性推演等;



#十一、数据结构
1. 数组:下标从1开始
```
a = {}
for i=1,1000 do
	a[i] = 0
end
```

2. 矩阵与多维数组:两种方式表示矩阵:
```
a. table中的元素为table,N*M的矩阵
mt = {}
for i=1,N do
	mt[i] = {}
	for j=1,M do
		mt[i][j] = 0
	end
end
```
```
b. 将索引合并,(若字符串为索引同样进行合并)
mt = {}
for i=1,N do
	for j=1,M do
		mt[(i-1)*M+j] = 0
	end
end
```
3. 链表:
```
a. 反向链表
list = nil	-- head
list = {next = list, value = v}	-- 在表头insert a value:第一个list是新定义的节点,后一个list是上一行的list;

-- 遍历
local l = list
while l do
	<访问l.value>
	l = l.next
end
```

4. 队列和双向队列:(先进先出)
* 数据量小可以直接使用table.insert和table.remove进行对数组的操作达到队列的效果,操作后table会自动移动元素(数据量大会造成开销大);
* 数据量大就需要实现队列:使用两个索引用于表示首尾的元素

```
Queue = {}
function Queue.New()
	return {first = 0, last = 1}
end

function Queue.PushFirst(queue, value)
	queue.first = queue.first - 1
	queue[first] = value
end

function Queue.PushLast(queue, value)
	queue.last = queue.last + 1
	queue[last] = value
end

function Queue.PopFirst(queue)
	local first = queue.first
	if first >queue.last then error("queue is empty") end
	local value = queue[first]
	queue[first] = nil		-- 为了允许垃圾回收
	queue.first = first + 1
	return value
end

function Queue.PopLast(queue)
	local last = queue.last
	if queue.first > last then error("queue is empty") end
	local value = queue[last]
	queue[last] = nil		-- 为了允许垃圾回收
	queue.last = last - 1
	return value
end
```

5. 集合与无序组(bag)
* lua表示集合,只需要将集合元素以key的形式存入table,查看是否存在只需要用该值索引是否为nil;
* 无序组(包):也称多重集合,只需要将value作为计数器,key作为集合元素保存到table中即可;

6. 字符串缓冲:
```
典型的读取代码:lua字符串是不可变值(immutable),每次赋值都会重新移动字符串,开销极大
local buff = ""
for lines in io.lines() do
	buff = buff .. lines .. "\n"
end
```

方案1: 将table作为一个字符串缓冲,使用table.concat来连接字符串
```
local t = {}
for line in io.lines() do
	t[#t + 1] = line
end
t[#t + 1] = ""		-- 目的在文本最后添加一个换行符;
local s = table.concat(t, "\n")
```

方案2: 通过io.read("*all")读取整个文件
 * concat & io.read 的内部原理:**算法核心是一个栈,已创建的大字符串位于栈底,较小的字符串则通过栈顶进入.
 类似于汉诺塔,栈中的任意字符串都比下面的字符串短;若压入的新字符串比下面的字符串长,则将两者连接,
 然后继续跟下面的字符串比较,直至长度小于下面的字符串或者到达栈底;**
```
function addString(stack, s)
	stack[#stack + 1] = s 		-- 压入栈顶
	for i = #stack -1, 1, -1 do
		if #stack[i] > #stack[i+1] then
			break
		end
		stack[i] = stack[i] .. stack[i+1]
		stack[i+1] =nil
	end
end
```
7. 图:以table为节点构建图
* name查找图的节点
```
local function name2node(graph, name)
	if not graph[name] then
		-- 节点不存在,构建一个新的
		graph[name] = {name, adj={}}
	end
	return gtaph[name]
end
```
* 从文件中读取数据并写入图
```
function readgraph()
	local graph = {}
	for line in io.lines() do
		-- 切分行中的两个名称
		local nameFrom, nameTo = string.match(line, "(%S+)%s+(%S+)")
		-- 查找相应的结点
		local from = name2node(graph, nameFrom)
		local to = naem2node(graph, nameTo)
		-- to加入到from的临界集合
		from.adj[to] = true
	end
	return graph
end

function findPath(curr, to, path, visited)
	path = path or {}
	visited = visited or {}
	if visited[curr] then 	-- 节点是否已经访问过
		return nil
	end
	visited[vurr] = true 	-- 将节点标记为已访问
	path[#path +1] = curr	-- 加入到路径中
	if curr == to then
		return path
	end
	-- 尝试所有的临界节点
	for node in pairs(curr.adj) do
		local p = findPath(node, to, path, visited)
		if p then return p end
	end
	path[#path + 1] = nil	-- 从路径删除节点
end

-- 测试代码
function printpath(path)
	for i=1, #path do
		print(path[i].name)
	end
end

g = readgraph()
a = name2node(g, "a")
b = name2node(g, "b")
p = findPath(a,b)
if p then printPath(p) end
```
#十二、数据文件与持久性
1.数据文件:可借用lua的table构造式来定义文件格式;将数据作为lua代码来输出;
eg:tab{"川", 24, gameplayer, ...}

2.串行化(Serialization):(序列化)将数据转换为字节流;之后就可以将数据存储到文件或者发送到网络中;
序列化后的数据可以用lua代码来表示;这样当运行这些代码时,存储的数据就可以在读取程序中得到重构;

#十三、元表
1.Lua中的每一个值都有一个元表.table和userdata可以有各自独立的元表,而其他类型的值则共享其类型所属的单一元表.
lua在创建元新table时不会创建元表;即元表为空
getmetatable(table)				-- 得到指定table的元表；
setmetatable(table,metatable) 	-- 设置指定table的元表，如果元表(metatable)中存在__metatable键值，setmetatable会失败。

2.任何table可以作为任何值得元表;table也可共享同一个元表;一个table的元表也能是其自己;
在lua 中只能设置table的元表;要设置其他类型的值得元表必须通过c代码编写;
字符串默认设置了一个元表;而其他类型默认没有元表;

3.元方法
-- 算术类元方法:可以混用类型
__add		-- 加法
__sub		-- 减法
__mul		-- 乘法
__div		-- 除法
__unm		-- 相反数
__mod		-- 取模
__pow		-- 乘幂
__concat	-- 字符串连接符
--注意:两个不同元表的值得表达式,元方法选择规则:
--第一个有元表and有对应字段则选择这个字段为元方法;否则选第二个;若两个都没有则引发错误;
-- 关系类元方法:只能用于相同类型比较,否则会引发错误(唯独等于不会引发错误,但会直接返回false);
__eq		-- 相等
__lt		-- 小于
__le		-- 小于等于
--其他关系运算符会自动转换成以上三种;

__tostring 	-- 库定义的元方法,函数print()总是调用tostring进行格式化输出;

__index:表查询(可以用于模拟继承);当通过键来访问table的时候，若table没有当前键,则会寻找
metatable（metatable存在）中的__index键;如果__index包含一个表，LUA会在表中进
行查找相应键；如果__index包含一个函数，lua会调用函数，函数定义__index更灵活，开销大。其实就是如下3个步骤:
a.在表中查找，如果找到，返回该元素，找不到则继续
b.判断该表是否有元表，如果没有元表，返回nil，有元表则继续。
c.判断元表有没有__index方法，如果__index方法为nil，则返回nil；如果__index	方法是一个表，则重复1、2、3；如果__index方法是一个函数，则返回该函数的	返回值。

__newindex:表更新;对表中不存在的索引赋值,则会查找元方法,若__newindex是一个函数,则调用;
若元方法是一个table，则在此table中执行赋值;

--注意:通过__index和__newindex可以实现很多强大的功能;比如,利用__index设置默认值;

4.Lua中只能设置table的元表，如要设置其他类型的值的元表，则必须通过C代码来完成。
5.__metatable:防止用户修改和查看集合的元表，设置了该字段，用getmetatable则返回这个字段的值，用setmetatable则引发错误；
rawset(t,k,v)：可以不涉及元方法直接对表设置；
rawget(t,i):不考虑元方法直接访问table；
7.常规table中的任何字段都是nil；


Lua与c#中类型的对应:
nil 				null
string              System.String
number 				System.Double
boolean				System.Boolean
table				LuaInterface.LuaTable
function 			LuaInterface.LuaFuction

#十四、环境
1.Lua将所有全局变量保存在一个常规table中,这个table叫做环境(environment);
Lua将环境table保存在一个全局变量_G中;这样就可以方便的操作这个table;
--注意:实际上环境不止几个;
value = _G[varname]
_G[varname] = value

2.全局变量的声明需要注意,在进行大型项目时,尽量进行封装和记录,避免直接声明全局变量,尽量使用rawset(t,k,v);
不允许有值为nil的全局变量,因为nil的全局变量自动认为是未声明的;

3.非全局环境:
setfenv()		-- 设置函数环境,参数是一个函数和一个新的环境tab;
--第一个参数可以是函数本身,或者数字(表示当期那函数调用栈中的层数,1表示当前函数,2表示当前函数的函数)
注意:setfenv在使用时,记得将之前的全局环境保存在新的环境中,否则,将会丢失所有之前环境;eg:setfenv(1, {g = _G})

--继承的方法组装新环境:
a = 1;
local newgt = {};
setmetatable(newgt, {__index = _G})
setfenv(1, newgt)
print(a)			-- >1

a = 10;
print(a)			-- >10
print(_G.a)			-- >1
_G.a = 20
print(_G.a)			-- >20


#十五、模块和包
1.require:用来加载模块;该调用有返回值,但行为由模块自己控制(模块可以自己控制返回值);
function require(name)
	if not package.loaded[name] then		-- 先判断是否加载过
		local loader = findloader(name)		-- 伪代码
		if loader == nil then
			error("unable to load module".. name)
		end
		
		package.loaded[name] = true			-- 若没有则标记,并初始化模块
		local res = loader(name)			-- 初始化模块
		if res ~= nil then
			package.loaded[name] = res
		end
	end
	return package.loaded[name]
end

注意:若require为制定模块找到一个lua文件,则通过loadfile加载;若是c程序库则通过loadlib加载;loadfile和loadlib
都只是加载了代码,并没有运行;为了运行,require会以模块名作为参数来调用代码;
如果加载器有返回值,则存储到table package.loaded中,若没有使用package.loaded的值;

2.require搜索路径采用的是串模式(pattern),其中每一项都是将模块名转换为文件名的方式;
其中还可以包含可选?,require会用模块名替换?;
--eg
?;?.lua;c:/windows/?
require "sql"		-- require试着打开sql,sql.lua,c:/windows/sql
require函数只处理了分号和问号.

3.require用于搜索lua文件的路径存放在package.path中,当lua启动后,环境变量LIA_PAHT初始化该值;
若没有找到,则使用编译时定义的默认路径来初始化;在使用LUA_PATH时,Lua会将其中所有子串";;"替换成默认路径;

4.require没有找到相符的Lua文件,就会去找C程序库;这类搜索会从package.cpath获取路径;环境变量LIA_CPAHT初始化该值;

5.模块编写
-- 模块设置
local modname = ...
local M = {}					-- 创建
_G[modname] = M					-- 赋予全局变量 
package.loaded[modname] = M		-- 赋予loaded table

-- 引入待使用的全局值
local sqrt = math.sqrt
local io = io

setfenv(1, M)					-- 修改环境

6.module函数:将上面模块设置全部包含进去;最后还会修改主程序块的环境;
默认情况下,该函数不提供外部访问.
方法一:必须在调用前,为需要访问的外部函数/模块进行局部声明;
方法二:通过继承,调用module时加一个选项package.seeall.

7.module函数还提供有额外功能:
_M:包含了模块table自身
_NAME:包含了模块名;
_PACKAGE:包含了包名称;

8.子模块:Lua支持层级性的模块名,用'.'来分隔;eg:mod.sub--sub是mod的子模块
--注意:在require查询时,'.'会自动转换为目录分隔符(unix:'/';windows:'\')
9.包:一个完整的模块树,lua的发行单位;

10.Lua中模块除了环境table时嵌套的,模块与子模块等没有显示的关联;加载模块a不会加载任何子模块,
同样加载子模块a.b不会加载a;要是实现关联,只需编码时,显示地加载即可;


#十六、面向对象编程
1.lua的table就是一种对象,都可以拥有状态;都有其独立于其值的标志(self);都有独立于创建者和创建地的生命周期;

2.self:面向对象的核心;代表table自身,类似于this;

3.':'的作用:将一个方法定义中添加一个额外的隐藏参数(self),以及在方法调用中添加一个额外实参(self);这只是一种语法糖

4.类:lua是没有类的概念的;每个对象只能定义其行为和属性;但我们可以轻易的模拟一个类,准确的说法是模拟一个原型;
原型:也是一张常规的对象,当其他对象遇到未知的操作时,原型会先查找他;
--注意:原型和类都是共享相同对象间的一类事物;



5.继承:
Account = {balance = 0}

function Accoutn:deposit(v)
	self.balance = self.balance + v
end

function Accoutn:withdraw(v)
	if v > self.balance then error"insufficient funds" end
	self.balance = self.balance - v
end

function Account:new(o)		-- Account 作为原型(类)
	o = o or {}
	setmetatable(0, self)
	self.__index = self
	return 0
end

SpecialAccount = Accoutn:new()	-- 继承,SpecialAccount可以重写方法

s = SpecialAccount:new()
s:deposit(100.00)				-- 查找相应字段,s-->SpecialAccount-->Account

6.多重继承
local function search(k, plist)
	for i,#plist do
		local v = plist[i][k]
		if v then return v end
	end
end

function createClass(...)
	local c = {}
	local parents = {...}

	setmetatable(c, {__index = function(t, k) return search(k, parents) end})	-- 类在其父类列表中的搜索方法
	c.__index = call		-- c 作为其实例的元表
	
	function c:new(o)		-- construction
		o = o or {}
		setmetatable(o, c)
		return o
	end
	
	return c				-- 返回新类
end

Named = {getname = function(self) return self.name end, setname = function(self, n) self.name = n end,}	-- 基类

NameAccount = createClass(Account, Named)	-- 派生类
account = NameAccount:new({name = "Paul"})	-- 实例
print(account:getname())	-- >paul

7.私密性:隐藏原表,返回一个有原表数据的新表
function newAccount(initialBanlance)
	local self = {balance = initialBanlance}
	local withdraw = function(v) self.balance = self.balance - v end
	local deposit = function(v) self.balance = self.balance + v end
	return {withdraw = withdraw, balance = balance , deposit = deposit}
end
8.单一方法:当对象只有一个方法时,可以不用创建接口table,但要将这个方法作为对象表示来返回.
--具有状态的迭代器就是一个单一方法对象
--调度(dispatch)方法
function newObject(value)
	return function(action, v)
		if action == "get" then return value
		elseif action == "set" then value = value
		else error("invalid action")
		end
	end
end

d = newObject(0)
print(d("get"))	-- > 0
d("set", 10)
print(d("get")) -- > 10
#十七、弱引用table
1.lua有内存管理机制,垃圾收集器(GC);
--GC要收集全局变量中对象,必须先将其赋值nil;
--保存在数组中的对象,即使对象不再使用,但数组引用着对象,那对象就始终不会被收集;解决办法就是弱引用;

2.弱引用(weak reference):是一种被GC忽视的对象引用;
若对象的所有引用都是弱引用,那当前对象就会被回收,同时还可以以某种形式删除弱引用;
--三种weak table:k为weak的table;v为weak的table;k,v同时为weak的table;无论哪种,只有其一被回收,对应的kv条目都会被回收;
--weak reference 通过元表字段__mode来控制,传入字符串"k"或"v"或"kv";
--例子
a = {}
setmetatable(a, {__mode = "k"})
key = {}
a[key] = 1
key = {}
a[key] = 2
collectgarbage()
for k,v in pairs(a) do
	print(v)			-- >2
end

3.弱引用只能用于对象,对于数字key,字符串,boolean,收集器永远不会回收;除非数字key的value被回收;



#十八、数学库
function|describe|栗子
--|:--:|--
math.pi | 圆周率(3.14159265358979323846)
math.abs| 绝对值|math.abs(-5.1) == 5.1
math.ceil| 不小于x的最小整数|math.ceil(2.1) == 3 
math.floor| 不大于x的最大整数|math.floor(1.3) == 1 
math.rad|角度转弧度|math.rad(180) == math.pi
math.deg| 弧度转角度|math.rad(math.pi) == 180
math.sin| 正弦|math.sin(math.pi) = 0
math.cos|余弦|math.cos(math.rad(60))== 0.5
math.tan|正切|math.tan(math.rad(45))| 1
math.asin|反正弦|math.asin(1) == 1.57
math.acos|反余弦|math.acos(0) == 1.57
math.atan|反正切|math.atan(1) == 0.785
math.atan2|x / y的反正切值|math.atan2(45, 45) == 0.785
math.sinh|	双曲线正弦|	math.sinh(0.5) == 0.5210953
math.tanh|	双曲线余弦|	math.tanh(0.5) == 0.46211715
math.cosh|	双曲线正切|	math.cosh(0.5) == 1.276259652
math.pow|得到x的y次方|math.pow(2, 5) == 32
math.sqrt|开平方函数|math.sqrt(16) == 4
math.exp|计算以e为底x次方值|math.exp(2) == 2.718281828
math.fmod|取模运算|math.fmod(14, 5) == 4
math.modf|把数分为整数和小数|math.modf(15.98) == 15,98
math.frexp|把双精度val分解为数字部分(尾数)和以2为底的指数n，即val=x*2^n|math.frexp(10.0) == 0.625,4
math.ldexp|value * 2的n次方|math.ldexp(10.0, 3) == 80
math.log10|以10为基数的对数|math.log10(100) == 2
math.log|一个数字的自然对数|math.log(2.71) == 0.9969
math.max|取得参数中最大值|math.max(2.71, 100, -98, 23) == 100
math.min|取得参数中最小值|math.min(2.71, 100, -98, 23) == -98
math.randomseed|设置随机种子,在使用math.random之前须使用此函数|math.randomseed(os.time())
math.random|获取随机数|math.random():[0,1)的随机数;math.random(100):[1,100]的随机数;math.random(2, 6):[2,6]的随机数

#十九、table库
function|describe|栗子
--|:--:|--
table.insert(tab, pos, value)		| 指定位置pos(默认结尾)插入value
table.remove(tab, pos)				| 移除指定位置pos(默认结尾)的数据
table.getn(tab)						| 返回元素个数
table.setn(tab, nSize)				| 设置table中的元素个数
table.maxn(tab) 					| 返回tab中所有正key值中最大的key值.若不存在正key值, 则返回0.此函数不限于数组部分
table.concat(tab, sep, s, e)		| 把从索引s(默认1)开始到索引e(默认总长)把表中所有数据(数组部分)以分隔符sep链接成一个字符，遇到nil则结束；
table.sort(tab, fun)				| 升序排序(fun可选)fun:规范带两个参数,并返回一个boolean;
table.foreachi(tab, function(i, v))	| 会期望一个从数字1开始的连续整数范围，遍历table中的key和value逐对进行function(i, v)操作
table.foreach(tab, function(i, v))	| 与foreachi不同的是，foreach会对整个表进行迭代


#二十、string库
function|describe|栗子
--|:--:|--
string.len(s)								| 返回字符串长度。
string.rep(s, n)							| 返回重复n次字符串s的串
string.upper(s):      						| 字符串全部转为大写字母。 
string.lower(s):    						| 字符串全部转为小写字母。
string.sub(s,i,j)							| 返回s的子字符串,从i到j(可缺,默认为末尾(-1));还可以使用负数索引(-n,倒数第n个)
string.reverse(s)							| 字符串反转| string.reverse("Lua") == auL		
string.format(...)							| 返回一个类似c中printf的格式化字符串 | string.format("value is:%d",4)== value is:4
string.char(num) 							| 将整型数字转成字符并连接，num可以多个;
string.byte(arg[,int][,int])				| 字符转整数,参数二可选(默认:1),参数三可选(表示参数二到三之间所有（默认:参数二)|string.char(97,98,99,100) == abcd ;string.byte("ABCD",4) == 68
string.find (str, substr, [init, [end]])	|模式匹配函数:在一个指定的目标字符串中搜索指定的内容(第三个参数为索引),返回其具体位置。不存在则返回nil。| string.find("Hello Lua", "Lua", 1)-- 7 9

string.match()								-- 搜索匹配的部分,并返回部分字符串
-->string.match("Hello World", "World")		-- World
-->string.match("today is 1994/8/7", "%d+/%d+/%d+")	-- 1994/8/7

string.gmatch(s, pattern)					-- 返回一个迭代器函数,每一次调用这个函数,返回一个在s找到的下一个符合pattern描述的子串。
-- 如果参数pattern描述的字符串没有找到，迭代函数返回nil
-- 获取字符串中的所有单词
words = {}
for	w in string.gmatch( s, "%a+") do 	-- "%a+":模式表示匹配一个或多个字母字符的序列
	words[#words + 1] = w
end

-- 模拟require寻找模块的功能
function search(modname, path)
	modname = string.gsub(modname, "%.", "/")		-- "%.":因为在模式中点"."有特殊意义,因此需要加一个%
	for c in string.gmatch(paht, "[^;]+") do		-- 遍历路径中所有组成部分
		local fname = string.gsub( c, "?", modname)
		local f = io.open(fname)
		if f then
			f:close()
			return fname
		end
	end
	return nil
end


string.gsub(mainStr,findStr,replaceStr,num)	-- 字符串中替换,mainStr为要替换的字符串,findStr为被替换的字符,replaceStr要替换的字符，num替换次数(忽略则全替换)
-- 注意:gsub返回值有两个,一个是新字符串,二个是替换的总次数
-- 特殊用法:gsub的第三个参数可以是table/function:
-- function:gsub每次找到匹配的函数就会调用该函数,函数参数就是捕获的内容,返回值则作为要替换的字符串;
-- table:gsub将捕获的内容作为key,在table中查找并将对应的value作为要替换的字符串(若没有key则不改变该匹配);
> string.gsub("all lii", "l", "x", 1) 	-- >axl lii
> string.gsub("all lii", "l", "x", 2) 	-- >axx lii
> count = select(2, string.gsub("str", " ", " ") 	-- >统计字符串中空格数量

--模式:对lua来说,模式就是普通的字符串,并一样遵守相同规则
1.字符分类:
%a	-- 字母
%c 	-- 控制字符
%d  -- 数字
%l 	-- 小写字母
%p 	-- 标点符号
%s 	-- 空白字符
%u 	-- 大写字母
%w 	-- 字母和数字字符
%x 	-- 十六进制数字(%x也可以表示由%转移后字符x)
%z 	-- 内部表示为0的字符
--注意:这些分类的大写形式表示其补集
> print(string.gsub("hello, up-down!", "%A", "."))	--> hello..up.down. 4

2.魔法字符:带有特殊含义
()		-- 捕获
.		-- 表示任何字符
-- "/%*.*%*/" :查找c中注释
%		-- 转义符
--注意：在模式中放入引号,需要用转义符'\';
+		-- 表示重复一次或多次
--"%a+" :表示一个或者多个字母(word)
--"%d+":表示一个或多个数字
-		-- 表示重复0次或多次:会匹配最短子串
*		-- 表示重复0次或多次:匹配最长子串
--典型用途:"%(%s*%)":表示();其中%s*可为一个空格或者多个空格;
--"[_%a][_%w]*":匹配乱程序中的标志符(以下划线/字母/数字组成,开头只能是字母/下划线)
?		-- 可选部分(出现0或1次)
--"[+-]?%d+":查找可带正负符号的数字.
注意:lua的模式修饰符只能用于修饰一个字符分类;
[]		-- 在方括号内将不同的字符进行分类或者单个字符组合起来,即可创建自己的字符集;
--"[%w_]":表示同时匹配字母,数字,下划线
--"[01]":表示二进制数字;"[%[%]]"表示方括号本身;
--字符集包含一段字符的范围,写法是第一个字符和最后一个字符,并用横线连接:
--"[0-9]"<-->%d
--"[0-9a-fA-F]"<-->%x,表示16进制
^		-- 补集;以其开头,则只会匹配目标字符串的开头部分
--"[^0-7]":表示非8进制的字符
--"[^\n]":表示除换行符以外的其他字符
--注意:对于简单分类,使用其大写形式也可以表示补集;"%S" <-->"[^%s]"表示所有非空格字符
-- if string.find(s, "^%d") then ... --字符串s是否以数字开头
$		-- 以其结尾表示只会匹配字符串的结尾部分.
-- if string.find(s, "^[+-]?%d+$") then ... 	-- 字符串是否以表示一个整数(即是字符串必须以数字开头,同时必须以数字)

--注意:%b用匹配成对的字符;"%b<x><y>"以x开始y结束;"%b()"

3.捕获capture:将模式中需要捕获的部分放到一对圆括号内.
对于具有捕获的模式,函数string.match会将所有捕获到的值作为单独的结果返回.
string.match([[then he said:"it's all right"!]], "([\"'])(.-)%1")	--%1:指定了第1个捕获的值(引号)
--"%[(=*)%[(.-)%]%1%]":匹配lua的长字符串


4.URL编码:是HTTP使用的一种编码方式,用于扎起URL中传递各种参数.
该编码方式会将特殊字符('=','&','+')编码为"%<xx>"十六进制的字符形式;空格转换为'+';
-- a+b = c  <==> "a%2Bb+%3D+c"

-- URL 解码
function unescape(s)
	s = s.gsub("+", " ")
	s = s.gsub("%%(%x%x)", function(h) return string.char(tonumber(h, 16)) end
	return s
end

-- URL 编码
function escape(s)
	s = s.gsub("[&=+%%%c]", function(c) return string.format("%%%02x", string.byte(c)) end
	s = s.gsub(" ", "+")
	return s 
end

5.tab(制表符)扩展
在lua中,()的空白捕获有特殊意义.该模式不是代表捕获空内容,而是捕获它的目标位置,并返回index;
print(string.match("hello", "()ll()"))		-- 3 5
第二个空捕获与string.find结果不一样,其原因是第二个是在匹配之后;

-- 方法一:gsub匹配所有的tab,捕获其位置,内嵌匿名函数根据tab位置,计算出还需要多少空格达到tab整数列.
-- 先对位置－1,时期冲0开始计数,然后加上补偿corr(补偿其前面的tab)
function expandTabs(s, tab)
	tab = tab or 8		-- 制表符的"大小"(默认为8)
	local corr = 0
	s = string.gsub(s, "()\t", function(p)
		local sp = tab - (p - 1 + corr)%tab
		corr = corr - 1 + sp
		return string.rep(" ", sp)
		end)
	return s
end
-- 方法二:在字符串中没8个字符后面插入一个标记,无论标记是否位于空格前都用tab替换;
function unexpandTabs(s, tab)
	tab = tab or 8
	s = expandTabs(s)
	local pat = string.rep(".", tab)
	s = s.gsub(pat, "%0\1")
	s = s.gsub(" +\1", "\t")
	s = s.gsub("\1", "")
	return s
end

6.模式使用技巧
(1)应当尽量精确的使用模式,宽泛的模式容易造成计算量过大;
-- "(.-)%$" :捕获美元符号之前的所有字符串;若字符串中没有美元符号,该模式从起始位置开始遍历未搜索到,会继续从第二位置重新遍历;时间复杂度为二次方;
-- 可以修改为"^(.-)%$":这样就只会从最开始搜索一次
(2)尽量少用空模式,即会匹配空字符串的模式;类似"%a*"



#二十一、I/O库
1.简单模型:所有操作都作用于两个当前文件;I/O库将当前输入文件初始化为进程标准输入(stdin),当前输出文件初始化为进程标准输出(stdout);在执行io.read()操作时,就会从标准输入中读取一行;
io.input():以只读模式打开指定文件,并将其设定为当前输入文件;
io.output():以只写模式打开指定文件,并设定为当前输出文件;
--两个函数报错则引发raise错误;
io.write:接受任意数量的字符串参数,并将它们写入当前输出文件;也接受数字参数,数字参数会根据常规的转换规则转换为字符串.
-- 注意:应当尽量避免调用io.write(a.. b.. c)的代码,而是io.write(a,b,c);
--print和write的区别:
a.write在输出时不会添加像制表符/回车这样的额外字符;
b.write使用当前输出文件,而print总是使用标准输出;
c.print会自动调用其参数的tostring()方法,因此它还能显示table/函数/nil;
io.read:其传入的参数决定要读取的数据:
"*all":读取整个文件
"*line":会返回当前文件的下一行,但不包括换行字符;当达到文件末尾时,该调用会返回nil;(read的默认模式)
"*number":读取一个数字,返回一个数字;会忽略数字前的所有空格,并能处理-3,-3.4e-23这样的数字格式;如果无法读取到数字,则返回nil;
<num>:读取一个不超过num个字符的字符串

-- lua可以高效的处理长字符串,因此在lua中一种简单的、编写过滤器的技术就可将整个文件读到一个字符串中,之后处理字符串,最后写入到输出
--以下示例:使用MIME quoted-printable编码方式对文件进行编码;这种编码,非ASCII字符被编码为=<xx>16进制数字代码.为了保持一致性,字符"="也需要被编码;
t = io.read("*all")	-- 读取
t = t.gsub("[/128-255=]", function (c)
			return string.format("=%02X", string.byte(c))
		end)		-- 处理
io.write(t)			-- 输出

local lines = {}
for line in io.lines() do		--io.lines() 迭代器
	lines[#lines + 1] = line	
end
table.sort(lines)		-- 排序

for _, v in ipairs(lines) do
	io.write(l, "\n")
end

io.read(0):检测是否还有数据可以读取,有数据则返回空字符串,否则返回nil;

2.完整模型:基于文件句柄等价于c的流(FIIE*);
io.open(name, mode):参数文件名和模式字符串("r"读取,"w":写入,同时会删除文件原来的内容, "a"追加,"b"打开二进制文件)
函数返回表示文件的新句柄;发生错误,则返回nil以及一条错误消息和错误代码;
--打开一个文件并读取其所有内容
local f = assert(io.open(filename, "r"))	-- 错误检测的典型做法:若打开失败assert第二个参数就会接受错误消息并显示信息
local t = f:read("*all")
f:close()

I/O库提供三个预定义c语言流的句柄:io.stdin,io.stdout,io.stderr;
io.stderr:write(message) -- 直接将信息写入错误流

--用户可以混合使用完整模式和简单模式;通过不指定参数调用io.input(),可以得到当前文件的句柄;

--临时改变输入文件
local temp = io.input()	 	-- 保存当前文件
io.input("newinput")		-- 打开新文件
--<< 操作新输入文件>>--		
io.input():close()			-- 关闭当前文件
io.input(temp)				-- 恢复原输入文件

注意:lua中,一次性读取整个文件比逐行读取要快一些;但一些大文件无法一次性读取,最高性能的处理法:
以最快的方法读取足够大的块(eg:8kb)来读取;避免在行中间断开,可以加上一行:
local BUFSIZE = 2^13
local lines, rest = f:read(BUFSIZE, "*line")

3.二进制文件
简单模式中io.input和io.output总是以文本方式打开文件(默认行为).
--在UNIX中,二进制文件和文本文件是没有差别的;
在处理二进制文件时,必须在模式字符串中加上"b";

lua中二进制数据的处理和文本处理类似;lua的字符串可能包含任意字节,lua库中几乎所有函数都能处理任意字节.
只要模式字符串不含值为0的字节,甚至还能对二进制数据进行模式匹配.若模式字符串需要包含值为0的字节,可以用%z来表示;

--转换dos格式文本文件到UNIX格式
local inp = assert(io.open(arg[1], "rb"))
local out = assert(io.open(arg[2], "wb"))

local data = inp:read("*all")
data = string.gsub(data, "\r\n", "\n")
out:write(data)

assert(out:close())

4.其他文件操作
tempfile():返回一个临时文件句柄,该句柄以读/写方式打开并在程序结束时自动删除;
flush():缓冲中的数据写入文件;与write一样;io.flush()刷新当前输出文件;f:flush()刷新某个文件;
seek():获取和设置一个文件的当前位置;f:seek(whence, offset),
--whence:一个string指定了如何解释offset;
--"set":offset解释为相对于文件起始的偏移量
--"cur":解释为相对于当前位置的偏移量;(默认值)
--"end":解释为相对于文件结尾的偏移量;
--offset:默认值为0
返回值:与whence无关,总是返回当前文件位置(相对于文件起始处的偏移字节数)
因此调用file:seek()不会改变文件当前位置,并返回当前文件位置;

```
-- 不改变文件位置并返回文件大小;
function getFileSize(file)
    local cur = file:seek()			-- 获取当前位置
    lcoal size = file:seek("end")	-- 获取大小
    file:seek("set", cur)			-- 恢复
    return size
end
```

#22.操作系统库
	操作系统库定义在os中,包含文件操作,获取当期那日期和时间的函数,以及其他一些与操作系统相关的功能;
	为保证lua的可移植性,lua只使用ANSI标准;而目录操作和套接字这类操作系统功能并不是ANSI的一部分,因此操作系统库不含他们;
	luasocket:提供网络支持;
	对文件的操作只提供了两个函数,改名os.rename()和删除文件os.remove()**

1. **os.time()**	
	不传参数,则返回数字形式的时间(大多数系统返回秒数)
	传参:tab:返回一个数字,表示该table中描述的日期和时间.tab具有以下有效字段:
	+ year:完整年份		-- 必须
	+ month:01-12		-- 必须
	+ day:01-31			-- 必须
	+ hour:00-23		-- 默认值(12:00)
	+ min:00-59		
	+ sec:00-59
	+ isdst:一个bool,true表示夏令时
```
os.time(year = 1970, month = 1, day =1 , hour = 0) -->10800(不同系统不同地区不一样)
```
2. **os.date()**		
	time的反函数,第一个参数是格式字符串,指定时间表现形式;第二参数是时间的数字,(可不填)默认为当期时间
```
	os.date("*t", number)	-- 返回一个table，结果基于当前时区的计算(例如北京时间1970.1.1,08:00:00)
	os.date("!*t", number)	-- 返回一个table，结果基于标准时区的计算
	os.date("%m/%d/%Y", number)	-- 返回一个string,如:1/1/1970
	os.date("%X", number)	-- 返回一个string,如:23:48:10
```
**注意**:os.date返回的table还包含一些额外的字段,星期数(wday:1表示星期天),一年中的第几天(yday:1是一月一日)
其他格式字符串,os.date会将日期转化为一个字符串,该字符串是格式字符串的一个复制,但其中的特殊标记被替换为时间信息.所有标记以"%"开头;
* %a  星期的简写(Wed)
* %A	-- 星期的全写(Wednesday)
* %b	-- 月份简写(Sep)
* %B 	-- 月份全写(September)
* %c	-- 日期和时间(eg:09/16/98 23:48:10) 默认值	*随区域和系统变化
* %d	-- 一个月的第几天(16)
* %H	-- 24小时制的小时数(00-23)
* %I	-- 12小时制(01-12)
* %j	-- 一年中的第几天(001-366)
* %M  -- 分钟数(00-59)
* %m  -- 月份数(01-12)
* %p  -- 上午/下午(am/pm)
* %S  -- 秒数(00-59)
* %w  -- 一周中的第几天(0-6)星期天-星期六
* %x  -- 日期(09/16/98) 	*随区域和系统变化
* %X  -- 时间(23:48:10)	*随区域和系统变化
* %y  -- 两位数的年份(00-99)
* %Y	-- 完整年份(1998)
* %% 	-- 字符'%'

3. **os.clock()**
```
-- 返回当前CPU时间的秒数,一般可计算一段代码的执行时间;
local x = os.clock()
local s = 0
for i = 1,100000 do s = s+1 end
print(string.format("elapsed time: %.2f\n", os.clock() - x))
```
4. **os.exit()**	
	终止当前程序执行.
5. **os.getenv()**	
	获取一个环境变量的值,并接受一个变量名.返回对应的字符串值;
6. **os.execute()**	
	执行一条系统命令,等价于c的system函数,需要接受一个命令字符串,并返回一个错误代码.
7. **os.setlocale()** 
	设置当前lua程序所使用的区域.区域定义了不同文化或语言间的差异;
	区域中有6种分类:
	- "collate"控制字符串的字母顺序,
	- "ctype"控制单个字符的类型及其大小写的转换
	- "monetary"不影响lua程序
	- "numeric"控制如何格式化数字
	- "time"控制如何格式化时间
	- "all"控制上述所有功能.(default)


#23.调试库
调试库需要慎重使用,首先其性能不高,其次会打破语言的一些固有原则;通常用户不希望在产品的最终版本中打开这个库,或者删除它,
可以使用debug = nil移除库.
调试库由两类函数组成:自省函数(introspective function)和钩子(hook).
自省函数允许检查一个正在运行中程序的各个方面,如活动函数栈、当前执行行、局部变量的名称和值;
栈层(stack level):本质是数字,表示一个活动的函数(被调用但尚未返回).
钩子则允许跟踪一个程序的执行;
1.自省机制:
--主要的自省的函数
debug.getinfo(foo) 	-- 第一个参数可以是一个函数(foo)或者栈层.
返回一个table,table中包含:
source函数定义位置,如果函数是通过loadstring在一个字符串中定义的,则source就是这个字符串
short_src:source的短版本(60字符)
linedefined:该函数定义在源代码中第一行的行号
lastlinedefind:该函数定义的源代码中最后一行的行号;
what:函数类型;"lua":普通函数;"C":c函数;"main":;lua程序块的主程序部分
name:该函数的一个适当名称
namewhat:上个字段的含义;"global","local","method","field","":没有找到函数名称,
nups:该函数的upvalue数量;
activelines:一个table,包含了该函数的所有活动行的集合.活动行:含有代码的行,这是相对于空行或只包含注释的行而言的.
func:函数本身

注意:当foo是一个c函数时,lua没有更多的信息;只有what,name,namewhat是有意义的.

debug.getinfo(n)	-- 得到栈层上的函数数据;若n大于栈中函数总数,则返回nil;
数字调用getinfo,table会包含一个额外的字段currentline:表示正在运行的那一行;func就是出于那层的函数;
字段field有些特殊,由于函数在lua中是第一类值(first-class value)它可以没有名称也可以有多个;lua检查该函数代码,
查看如何被调用,从而试着找出该函数名称.这种方法只有当用数字调用getinfo才起作用;
getinfo效率不高,为了更好的性能,getinfo有第二个可选参数(字符串),用于指定希望获取那些信息:
'n':name和namewhat
'f':func
'S':source,short_src,what,linefined,lastlinedefined
'l':currentline
'L':activelines
'u':nups


--简单的当前栈的追溯:traceback
function traceback()
	for level = 1, math.huge do
		local info = debug.getinfo(level, "Sl")
		if not info then break end
		if info.what == "C" then
			print(level, "C function")
		else
			print(string.format("[%S]:%d", info.short_src, info.currentline))
		end
	end
end

debug.traceback -- 返回一个表示追溯结果的字符串

debug.getlocal(level, index)	-- 检查任意活动函数的局部变量;level:栈层;index:变量索引
注意:若变量索引大于活动变量的总数,getlocal会返回nil;若栈层无效,getlocal引发错误;
lua按局部变量在一个函数中出现的顺序为变量编号,编号只限于在函数当前作用域中活跃的变量;

debug.getupvalue(closure, index)	-- 访问lua函数使用的upvalue;closure:闭合函数;index:索引
debug.setupvalue(closure, index, value)	-- 修改非局部变量

调试库的所有自省函数都接受一个可选的协同程序参数作为第一个参数;这样就可以从外部检测这个协同程序;

2.钩子:有四种时间会触发一个钩子:
--注册一个钩子
debug.sethook(hookfunc, eventType[, num])
关闭钩子只需不带任何参数调用sethook

a.每当lua调用一个函数时产生的call事件;eventType:"c"
b.函数返回时产生的return事件;eventType:"r"
c.lua执行一行新代码时产生的line事件;eventType:"l"
d.当执行完指定数量的指令后产生的count事件;num

#24.CAPI概述
lua是一种嵌入式语言;lua是不能单独运行的,需要作为库链接到其他程序;
c语言与lua之间的两种交互形式:
a.c具有绝对的控制权,lua是一个库;
b.lua具有控制权,c是一个库;
应用程序代码和库代码都使用同样的API来与lua通信,这些API称为CPI;

1.CAPI:是一组使c与lua交互的函数;包含读写lua全局变量,调用lua函数,运行代码,注册c函数以及lua代码调用;
CAPI遵循c语言的操作模式,需要注意类型检测,错误恢复,内存分配错误和其他源代码复杂性.
API中的大多数函数不会检测其参数的正确性,但必须保证传入参数的合法性.如果传入了错误的参数,
并不会得到一个经过设计的错误消息,而是得到一个"segmentation fault"或类似的错误.
Lua和c通信的主要方法是一个无所不在的虚拟栈;所有数据交换都是通过这个栈来完成;
同时,栈还可以解决lua和c的两大差异:1是lua是垃圾收集,c是显示释放;2是lua使用动态类型,c使用静态类型.

--解释器程序
#include<stdio.h>
#include<string.h>
#include"lua.h"
#include"lauxlib.h"
#include"lualib.h"

int main(void)
{
	char buff[256];
	int error;
	lua_State *L = luaL_newstate();	// open lua
	luaL_openlibs(L);				// open standard lib
	
	while(fgets(buff, sizeof(buff), stdin) != NULL)
	{
		error = luaL_loadbuffer(L, buff, strlen(buff), "line") || lua_pcall(L, 0, 0, 0);
		if(error)
		{
			fprintf(stdrr, "%s", lua_tostring(L, -1));
			lua_pop(L, 1);	// 从栈中弹出错误消息
		}
	}
	lua_close(L);
	return 0;
	
}

lua.h:定义了lua提供的基础函数,包含创建lua环境,调用lua函数(lua_pcall),读写lua环境中全局变量,以及注册供lua调用的新函数等
其所有内容都有一个lua_前缀.

lauxlib.h定义了辅助库(auxiliary library, auxlib)提供的函数;辅助库是一个使用lua.h中API编写出的一个较高的抽象层.
lua的所有标准库都用到了辅助库;基础API的设计保持原子性和正交性,而辅助库则侧重于解决具体的任务;
注意:辅助库并没有直接访问lua的内部,它都是用官方的基础API来完成所有工作;
其所有内容有以luaL_为前缀;
lua库中没有定义任何全局变量,它将所有的状态都保存在动态结构lua_State中,所有的CAPI都要求传入一个指向该结构的指针.
这种实现使得lua可以重入(reenter),稍加修改即可用于多线程的代码.

luaL_newstate函数用于创建一个新环境(或状态).当luaL_newstate创建一个新环境时,新环境并没有包含预定义的函数,甚至没有print;
辅助库函数luaL_openlibs则可以打开所有标准库.

当创建好一个状态,并在其中加载了标准库后,就可以解释用户的输入了;
程序调用luaL_loadbuffer来编译用户输入的每行内容.若无错,返回0,并向栈中压入编译后的程序块.
然后,调用lua_pcall,函数将程序块从栈中弹出,并在保护模式中运行它(同样没有错误返回0,出错则压入一条错误信息).
lua_tostring可以获取错误信息,打印后可以用lua_pop把他从栈中删除.
注意:在发生错误时,这个程序只是简单的将错误信息打印到标准输出流.在实际应用中真正的错误处理则可能更复杂.

2.栈:
a.动态类型和静态类型的问题:使用一个抽象的栈,在lua和c间交换数据;栈能保存任何类型的lua值;
要获取lua的值时,只要调用一个luaAPI的函数,lua就会获取该值并从栈中弹出.
为了将c类型的值压入栈,或从栈中获取不同类型的值,就需要为每种类型定义一个特定的函数.

注意:由lua管理,垃圾收集器能确定c使用了哪些值.

3.压入函数:
lua_pushnil(lua_State *L)									-- 压入nil
lua_pushboolean(lua_State *L, int bool)						-- 压入布尔
lua_pushnumber(lua_State *L, lua_Number n)					-- 压入双精度浮点数
lua_pushinteger(lua_State *L, lua_Integer n)				-- 压入整数
lua_pushlstring(lua_State *L, const char *s, size_t len)	-- 压入任意字符串(char*及长度)
lua_pushstring(lua_State *L, const char *s)					-- 压入以零结尾的字符串

lua的字符串不以0结尾,可以包含任意二进制数据,但同时必须保存期显式的长度.
lua不会持有指向外部字符串的指针.
注意:所有lua的字符串,都会生成一个内部副本,或复用现有的内容.

向栈中压入一个元素,应该确保栈中具有足够的空间.当lua启动时,lua调用c语言时,栈中至少会有20个空闲的槽. 
int lua_checkstack(lua_State *L, int sz)	-- 检测栈中空间足够

4.查询函数:
API使用索引来引用栈中元素;第一个压入栈中的元素索引为1,以此类推;还可使用负数,-1表示栈顶第一个元素;
lua_tostring(L,-1)会将栈顶的值作为字符串返回;
-- 检测元素是否为特定的类型;lua_isnumber和lua_isstring不会检查值,而是判断能否转为对应的类型;
lua_is*(lua_State *L, int index);

lua_type() --返回栈中元素的类型.每种类型对应于一个常量,这些常量定义在头文件lua.h中,他们是
LUA_TNIL,LUA_TNUMBER,LUA_TSTRING,LUA_TTABLE,LUA_TTHREAD,LUA_TUSERDATA,LUA_TFUNCTION

-- 从栈中获取值:
lua_to*(lua_State *L, int index);
int lua_toboolean(lua_State *L, int index)
lua_Number lua_tonumber(lua_State *L, int index)
lua_Integer lua_tointeger(lua_State *L, int index)
const char *lua_tolstring(lua_State *L, int index, size_t *len)	-- 返回一个指向内部字符串副本的指针,并将字符串长度存入最后一个参数len(可以为NULL).
--const说明了内部副本不能修改,所有lua_tolstring返回字符串在其末尾都会有一个额外的零,不过这些字符串中间也可能有零,
-- lua_tostring:就是用NULL作为第三个参数调用lua_tolstring
size_t lua_objlen(lua_State *L, int index)	-- 返回一个对象的长度.对于字符串和table这个值时长度操作符#的结果.该函数还可以获取一个"完全uerdata"的大小

--栗子:打印整个栈中的数据
static void stackDump(lua_State *L)
{
	int;
	int top = lua_gettop(L);
	for (i =1;i<= top; i++)
	{
		int t = luatype(L,i);
		switch(t)
		{
			case LUA_TSTRING:
			{
				printf("'%s'", lua_tostring(L, i));
				break;
			}
			case LUA_TBOOLEAN:
			{
				printf(lua_toboolean(L, i)? "true": "false");
				break;
			}
			case LUA_TNUMBER:
			{
				printf("%g", lua_tonumber(L, i));
				break;
			}
			default:
			{
				printf("%s", lua_typename(L, t));
				break;
			}
		}
		printf(" ")
	}
	printf("\n");
}

5.普通栈操作:
int lua_gettop(lua_State *L)				-- 返回元素的个数,即是栈顶元素的索引;
void lua_settop(lua_State *L, int index)	-- 将栈顶设置为一个指定的数字,如之前的栈顶比新设置的更高,那么高的将被抛弃;反之压入nil补足;
--lua_luasettop(L, 0)清空栈
#define lua_pop(L,n) lua_settop(L, -(n)-1) 	--索引使用负数-n表示设置顶部为相对于栈顶位移n的位置为栈顶

void lua_pushvalue(lua_State *L, int index)	-- 将指定索引的值的副本压入栈;
void lua_remove(lua_State *L, int index)	-- 移除指定索引的元素,并下移栈元素;
void lua_insert(lua_State *L, int index)	-- 插入栈顶元素到指定索引;
void lua_replace(lua_State *L, int index)	-- 弹出栈顶值,并设置该值到指定索引;

6.CAPI的错误处理
C没有异常处理机制;lua采用c中的setjmp机制,类似于异常处理;
如果内存分配错误,又不想结束应用程序,做法1:设置一个紧急(panic)函数,让其不要把控制权返回给lua;
做法2:让代码在"保护模式"下运行;
lua解释器采用第二种,调用lua_pcall来运行lua代码;如果发生错误lua_pcall会返回错误代码,并将解释器封固在一致的状态;
若要保护哪些lua于c交互的代码;可以使用lua_cpcall.
lua_cpcall:接受一个c作为参数,然后调用这个c函数

库代码的的错误处理:c程序的的错误行为只能用底层硬件术语解释,而错误位置则是"程序计数器"寄存器给出的.
当新c函数加入lua,就有可能打破这种安全性;每个c程序都有其各自处理错误的方法,当为lua编写库函数时,却只有一种标准的错误处理方法.
当c检测到错误,它就应该调用lua_error;
lua_error:会清理lua中所有需要清理的东西,然后跳转回发起执行的那个lua_pcall,并附上一条错误消息;

#25.扩展应用程序
lua的一项重要用途就是作为一种配置语言(configuration language).
lua_newtable:创建一个空table,并将其压入栈中;
lua_setglobal:将栈顶数据名字.并将其设置为全局变量
lua_getglobal:更具名字获取全局变量


lua_gettable(lua_State *L, int index)				-- 获取table,需要知道table的栈位置,然后才会弹出table[key];
lua_getfield(lua_State *L, int index, string key)	-- 获取栈中的table[key];
--栗子
lua_pushstring(L, key)
lua_gettable(L, -2)		-- 获取background[key]
<=====>
lua_getfield(L,-1,key)


lua_settable(lua_State *L, int index)				-- 设置table的kv,并出栈k,v(前两个值);
lua_setfield(lua_State *L, int index, k)			-- 设置table的kv,并出栈v;

--栗子
lua_pushstring(L, index);
lua_pushnumber(L, (double)value/MAX_COLOR);
lua_settable(L, -3);
<=====>
lua_pushnumber(L, (double)value/MAX_COLOR);
lua_setfield(L,-2, index);

lua_pcall(lua_State *L,int nargs,int nres,int errf)	-- nargs:参数个数,nres返回值个数,errf:错误处理函数(0表示无)
在压入结果前,会先弹出栈中的函数及其参数;若有多个结果,那么第一个结果先压入;
注意:运行过程中有任何错误,会返回一个非零值,并在栈中压入错误消息;但函数仍然会弹出函数及其参数;
在压入错误消息前,若存在错误处理函数,会先调用错误处理函数(错误处理函数必须先压入栈中,即必须位于待调用函数及其参数的下面);
对于普通的错误,lua_pcall会返回错误代码LUA_ERRRUN;有两种特殊情况不会运行错误处理函数:
a.内存分配错误,lua_pcall返回LUA_ERRMEN;
b.发生在lua运行错误处理函数时,lua_pcall立即返回错误代码LUA_ERRERR;

call_va(const char *func, const char *sig, ...)	-- 函数名字,描述参数类型和结果类型的字符串,以及所有参数变量和所有存放结果的指针;

--栗子
call_va("f", "dd>d",x,y,&z);	-- "dd>d":表示两个双精度类型参数和一个双精度类型的结果

#26.从lua调用c#
lua能调用c函数,但不意味可以调用所有c函数.
对于一个能被lua调用的c函数,它必须遵循一个获取参数和返回结果的协议.
此外,还必须注册c函数,以便用某种适当的方式将函数地址告诉lua.

lua_pushcfunction():注册函数要求传入一个指向c函数的指针;



#27.编写c函数技术


#28.用户自定义类型


#29.管理资源



#30.线程和状态
lua不支持真正的线程,即不支持内存共享的抢先式(preemptive)多线程;
不支持的两个原因:
* ANSI C没有这种功能,且没有可移植的方法能实现这种机制;
* lua引入多线程并不是一个不好的选择;

lua的线程是**协程**(collaborative),以此来避免不可预知的线程切换带来的问题;

##30.1 多个线程
&emsp;&emsp;从实现的角度看一个线程就是一个栈,每个栈保留着一个线程中所有未完成的函数调用信息,这些信息包含调用的函数、每个调用的参数和局部变量;即一个栈拥有一个线程继续运行的所有信息;多个线程即有多个栈;

CAPI的第一个参数不仅表示一个Lua状态,还表示一个记录在该状态的线程;只要创建一个lua状态,lua就会自动创建一个新线程,该线程称为**主线程**.

调用`lua_newthread`便可以在一个状态中创建其他的线程:

	lua_State *lua_newthread(lua_State *L)	
	返回一个lua_State指针,表示新线程,还会将新线程作为类型thread的值压入栈中;
* 除了主线程外,其他线程都是和lua对象一样都是垃圾回收的对象;
* 注意不要使用未被正确系缚(anchored)的线程.指既不是lua对象又不在栈中,又不被任何lua对象引用的情况;

`lua_xmove(F,T,n)`在两个栈之间移动lua值,从F弹出n个值并压入到栈T中
`int lua_resume(lua_State *L, int narg)`:启动协程,将带调用的函数压入栈中,并压入参数,最后在调用lua_resume时传入参数数量narg

##30.2 Lua状态
`lua_newstate/luaL_newstate`都会创建一个新的lua状态,不同的lua状态是各自完全独立的,
它们之间不共享任何数据.若要交换数据只能通过c代码中转,同时数据只能是c的类型;
#31.内存管理






















