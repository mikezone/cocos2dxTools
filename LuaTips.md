错误的三目运算符

```lua
a and b or c 
```
当b为nil或者false的时候始终返回c的值，纠正为

```lua
(a and {b} or {c})[1]
```
另外当a为0时，C语言对应布尔值为false，而Lua则对应为true。
如C语言：

```c
int a = 0;
printf("%s", a ? "分支1" : "分支2"); // 分支2
```
Lua:
```lua
local a = 0
print((a and {"分支1"} or {"分支2"})[1]) -- 分支1
```
因此and、or组合做三目运算的时候要始终让判断值返回bool，像如下：

```lua
local a = 0
print((a ~= 0 and {"分支1"} or {"分支2"})[1]) -- 分支2
```



