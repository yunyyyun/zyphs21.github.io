string.gsub 函数有三个参数：目标串，模式串，替换串。
基本作用是用来查找匹配模式的串，并将使用替换串其替换掉： 

```
s = string.gsub("Lua is good", "good", "bad?") 
print(s)   --> Lua is bad
```

string.gsub 的第二个返回值表示进行替换操作的次数。例如，
下面代码计字符串中空格出现的次数： 

```
_, count = string.gsub("test test", " ", " ") 
```

_ 表示哑元变量

模式串

```
.   任意字符 
%a   字母 
%c   控制字符 
%d   数字 
%l   小写字母 
%p   标点字符 
%s   空白符 
%u   大写字母 
%w   字母和数字 
%x   十六进制数字 
%z   代表 0的字符 
```

特殊字符如下：
(). % + - * ? [ ^ $ 
% 也作为以上特殊字符的转义字符。

[] 该方框作为匹配该范围的集合，。
  如[0-9] 则匹配0到9的数字范围

Lua 中的模式修饰符有四个： 

```
+   匹配前一字符 1 次或多次，最长匹配
*   匹配前一字符 0 次或多次，最长匹配
-   匹配前一字符 0 次或多次，最短匹配
?   匹配前一字符 0 次或 1次 
```

'+'，匹配一个或多个字符，总是进行最长的匹配。
如，模式  '%a+'  匹配一个或多个字母或者一个单词： 

注意以上的区别：

如：匹配c中的注释串
用 '/%*.*%*/'  和'/%*.-%*/'

```
str = "int x; /* x */  int y; /* y */" 
print(string.gsub(str, "/%*.*%*/", "<注释串>")) 
  --> int x; <注释串> 
  --> 采用 '.-' 则为最短匹配，即匹配 "/*" 开始到第一个 "*/"  之前的部分： 
str = "int x; /* x */  int y; /* y */" 
print(string.gsub(str, "/%*.-%*/", "<注释部分>")) 
  --> int x; <注释串>  int y; <注释串> 
```


以 '^'  开头表示只匹配目标串的开始部分，
以 '$'  结尾表示只匹配目标串的结尾部分。

%b 表示匹配对称字符，注意其只能针对的ansi码单字符。

```
x = string.gsub("xdddddyxxx", "%bxy", "取代")
print(x)   -->取代xxx
```

如去除字符串首尾的空格： 

```
function trim (s) 
  return (string.gsub(s, "^%s*(.-)%s*$", "%1")) 
end 
```

-----------------------------------------------------------------
以上为转载，仔细看了会，自己写了一个函数，实现数字格式化：

```
while true do
	
	s=io.read("*line")                    -->小数点后留3位
	s=string.format("%.3f", s or 0)
	print(s)
	function formatNum(s)
		local _,count=string.gsub(s, "^(%d+)(%d%d%d)",'%1')   
		-->字符串模式匹配
		if count==0 then
			return s
		else
			return formatNum(string.gsub(s, "^(%d+)(%d%d%d.*)", '%1')) .. (string.gsub(s, "^(%d+)(%d%d%d)", ',%2'))                                                                                                  								-->字符串模式匹配，分割递归调用
		end
	end
	formatNum(s)
	print(formatNum(s))
	
end
```

运行该lua程序后，输入:

```
123456789.987654
则输出 :
123456789.988
123,456,789.988
```