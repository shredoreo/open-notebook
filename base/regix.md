# 正则表达式学习

正则表达式是一个可以帮助我们匹配复杂字符串模式的工具

## 匹配字符(What)

匹配单个字符

- `.`  Matches any character other than newline (or including newline with the /s flag)
- `[abc]` `[a-zA-Z]`    Matches either an a, b or c character
- `[^abcd] ` Matches any character except for an a, b or c
- `\d`  anydigit
- `\s`  Matches any space, tab or newline character.(blank in general) 
-  `\t` tab
-  `\w`  Matches any letter, digit or underscore. Equivalent to `[a-zA-Z0-9_]`.
- `\D \S \T \W`  reverse to its lowercase

## 匹配数量(How)

修饰在字符后面，如a?表示匹配0个或1个a

- `?`  Matches an `a` character or nothing.
- `*` Matches zero or more consecutive `a` characters.
- `+` Matches one or more consecutive `a` characters.
- `{n}`  Matches exactly n times consecutive `a` characters.
- `{n,}` Matches at least n times consecutive `a` characters.
- `{n,m}` Matches between n and m (inclusive) times consecutive `a` characters.

## 子匹配

- `()`

## 其他

- `a*` Greedy 贪婪  Matches as many characters as possible.
- `a*?` Lazy   Matches as few characters as possible.
- `|` 或
- `^`  Matches the start of a string without consuming any characters. If multiline mode is used, this will also match immediately after a newline character.
- `$` Matches the end of a string without consuming any characters. If multiline mode is used, this will also match immediately before a newline character.

## mode

- `m` The ^ and $ anchors now match at the beginning/end of each line respectively, instead of beginning/end of the entire string.

## 性能

不同正则表达式，对通用字符配平，性能相差会很大。减少“回溯”是最好的方法，减少回溯其中最主要的方法是：”用最小范围的元字符，尽量避免用过大的元字符！”。一般规律如下：

1、使用正确的边界匹配器（^、$、\b、\B等），限定搜索字符串位置

2、使用具体的元字符、字符类（\d、\w、\s等） ，少用”.”字符

3、使用正确的量词（+、*、?、{n,m}），如果能够限定长度，匹配最佳

4、使用非捕获组、原子组，减少没有必要的字匹配捕获用(?:)

## 例子

```
93.180.71.3 - - [17/May/2015:08:05:32 +0000] "GET /downloads/product_1 HTTP/1.1" 304 0 "-" "Debian APT-HTTP/1.3 (0.8.16~exp12ubuntu10.21)"
93.180.71.3 - - [17/May/2015:08:05:23 +0000] "GET /downloads/product_1 HTTP/1.1" 304 0 "-" "Debian APT-HTTP/1.3 (0.8.16~exp12ubuntu10.21)"
80.91.33.133 - - [17/May/2015:08:05:24 +0000] "GET /downloads/product_1 HTTP/1.1" 304 0 "-" "Debian APT-HTTP/1.3 (0.8.16~exp12ubuntu10.17)"
217.168.17.5 - - [17/May/2015:08:05:34 +0000] "GET /downloads/product_1 HTTP/1.1" 200 490 "-" "Debian APT-HTTP/1.3 (0.8.10.3)"
217.168.17.5 - - [17/May/2015:08:05:09 +0000] "GET /downloads/product_2 HTTP/1.1" 200 490 "-" "Debian APT-HTTP/1.3 (0.8.10.3)"
93.180.71.3 - - [17/May/2015:08:05:57 +0000] "GET /downloads/product_1 HTTP/1.1" 304 0 "-" "Debian APT-HTTP/1.3 (0.8.16~
```