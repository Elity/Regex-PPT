name:inverse
class: center, middle,inverse
layout: true

---

# 正则表达式

## Regex

Fighting

???

- 表单验证
- 字符串替换
- 字符串匹配
- 字符串分割
- 爬虫
- 安全审计
- 词法分析
- 日志分析

---

> 所谓正则表达式，就是一种描述字符串结构模式的形式化表达方法
> ————《精通正则表达式》

???

- [一本书：《精通正则表达式》](https://item.jd.com/11070361.html)
- [一个网站： REX EGG](https://www.rexegg.com)

---

# 几个问题

???

带着问题出发

---

- 匹配手机号
- 验证密码强度
- 驼峰转连字符风格
- 添加千分位分隔符
- 匹配形如 abba 的字符串

???

### 以上示例，用正则不一定是最佳实践，但可以帮助理解正则

### 匹配手机要求

- 以'86-'开头时，后面必须是 1 开头的十一位数字
- 以'其它数字-'开头时，后面必须是 6 位以上数字
- 纯数字的手机，必须是 1 开头的十一位的纯数字

### 密码强度验证要求

- 必须包含一个数字
- 必须包含一个小写字母
- 必须包含一个大写字母
- 位数为 6-15 位

---

# 风格

### PCRE

### POSIX

???

php 是最吼的编程语言，因为同时支持两种风格

风格归风格，在各家引擎实现的时候功能并非完全照着实现了
比如 javascript 不支持：原子分组(atomic group)、字符类交集(character class intersection)、符类交集(character class subtraction)、共用集合(branch reset group)、占有匹配、注释模式、宽松格式模式(free-spacing mode)
已经进入提案的的特性(新版 chrome 已经支持)：负向断言、命名分组

---

# 引擎

### DFA

### NFA

---

# 引擎

### DFA（确定有穷自动机）

### NFA（非确定有穷自动机）

???

### NFA 其实还分为 传统 NFA、POSIX NFA、DFA、混合引擎

### 测试 NFA 还是 DFA

```javascript
/nfa|nfa not/.match('nfa not');
// 匹配到nfa 则是NFA，否则可能是POSIX NFA 或DFA
```

```javascript
// NFA性能差演示   配合RegexBuddy演示
// 时间长则是POSIX NFA 否则是 DFA
console.time();
'=TTT====='.match(/T(.+)+T/);
console.timeEnd();
```

编译原理、龙书

1.  DFA 对于文本串里的每一个字符只需扫描一次，比较快，但特性较少，不支持捕获与反向引用；NFA 要翻来覆去吃字符、吐字符，速度慢，但是特性丰富，所以反而应用广泛。
2.  只有 NFA 才支持 lazy 和 backreference 等特性；
3.  NFA 急于邀功请赏，所以最左子正则式优先匹配成功，因此偶尔会错过最佳匹配结果；DFA 则是“最长的左子正则式优先匹配成功”。 'perlman book'.match(/perl|perlman/)
4.  NFA 可能会陷入递归调用的陷阱而表现得性能极差。
5.  NFA 缺省采用 greedy 量词

##### 左最长规则 的意思就是把你希望得到的最长的那个表达式写在左边，这样他就会按顺序匹配到我们期望的内容了。

主流的编程语言使用 NFA，mysql、awk 使用 DFA、grep 命令使用混合

---

# 两个函数

--

- String.prototype.match
- RegExp.prototype.test

???

- String.prototype.split
- String.prototype.replace
- String.prototype.search
- RegExp.prototype.exec

---

# 元字符

---

- ###字符匹配

--

- ###位置匹配

--

- ###量词

---

.left-column[

## 字符匹配

]
.right-column[
.left[

.

[xyz]、[^xyz]、[x-y]

ab|cd

\d、\D

\w、\W

\s、\S

\n、\r

\t、\v

\f

]
]

???

- "."指代[^\r\n\u2028\u2029]
- \n 换行符
- \r 回车符
- \f 翻页符

### 既然'.'还有字符不能匹配，那么如何匹配任意字符呢？

- [\d\D]
- [\w\w]
- [\s\S]
- [^]

### 事实上，ES2018 新增了 dotAll 模式，可以让点号匹配任意字符

```javascript
/./s.test('\r\n');
```

---

.left-column[

## 字符匹配

## 位置匹配

]
.right-column[
.left[

^、$

\b、\B

(?=x)、(?!x)

(?<=x)、(?<!x)

]]

???

### 零宽断言、环视

- (?=x)正向肯定预查、正向零宽先行断言、 "aa12bb22cc32".match(/[a-z]{2}(?=22)/)
- (?!x)正向否定预查、负向零宽先行断言、 "aa12bb22cc32".match(/[a-z]{2}(?!22)/)
- (?<=x)负向肯定预查、正向零宽后发断言、 "aa12bb22cc32".match(/(?<=cc)\d{2}/)
- (?<!x)负向否定预查、负向零宽后发断言、 "aa12bb22cc32".match(/(?<!cc)\d{2}/)

---

.left-column[

## 字符匹配

## 位置匹配

## 量词

]
.right-column[
.left.right-img[

\- 匹配 0 次或多次表达式

\* 匹配 1 次或多次表达式

? 表达式可有可无

{n} 匹配 n 次表达式

{n,} 匹配 n 次或多次表达式

{n,m} 匹配 n 至 m 次表达式

]
]

???

## 优先级 /^abc|bcd$/.test("abcd") // 是否为 true

## 符号优先级

![优先级](./images/youxianji.png)

---

# Greedy、Lazy、Possessive

???
我们大多数前端人员可能知道 Greedy 和 Lazy，但是 Possessive 是个什么鬼？
（占有匹配是什么？如何使用占有匹配）

js 下的正则默认是贪婪匹配,那么如何变为惰性模式

---

# 贪婪、懒惰、占有

???

1.贪婪匹配

```javascript
'123445'.match(/(\d*)/);
```

2.贪婪但是良心尚存。

```javascript
'123445'.match(/(\d*)\d\d/);
```

3.惰性匹配

```javascript
'123445'.match(/(\d{2,5}?)/);
```

4.惰性但是乐于助人

```javascript
'123445'.match(/^(\d{2,5}?)\d\d$/);
```

5.占有匹配

```javascript
'AAA'.match(/A++./);
```

6.贪婪与懒惰效率问题

```javascript
// 贪婪好于懒惰
'<abcdefghijklmnopqrstuvwxyz0123456789>'.match(/<.*>/);
'<abcdefghijklmnopqrstuvwxyz0123456789>'.match(/<.*?>/);
// 贪婪等于懒惰
'<abcdefghijklmnopqrstuvwxyz0123456789>aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'.match(
  /<.*>/
);
// 最佳实践
'<abcdefghijklmnopqrstuvwxyz0123456789>aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'.match(
  /<[^>]+>/
);
```

---

# Grouping & Capturing

--

# 分组 & 捕获

???

```javascript
"<a href='/a/b/c' id='test'>百度</a>".match(/href='([^']+)'/); // 分组
'今天的日期是：2018-06-22'.match(
  /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/
); // 命名分组
```

### 括号嵌套的问题

---

# Backreferences

--

# 反向引用

???

##### 匹配

- 2018-06-22
- 2018/06/22

#### 不匹配

- 2018-06/22
- 2018/06-22

```javascript
/\d{4}([\-\/])\d{2}\1\d{2}/.test('2018-06-22');
```

#### 回文匹配问题

```javascript
/(\w)(\w)\2\1/.test('abba');
```

---

# Flags

--

# 修饰符

---

- g 全局搜索
- i 不区分大小写
- m 多行模式
- u unicode
- y 粘性匹配

???

```javascript
'123abc456def789'.match(/\d+/); // 全局搜索案例

'aaaaa'.match(/A/); // 忽略大小写
`aa 
`.match(/^\w+$/); // 多行匹配

// 粘性匹配
var re = /\d{2}/y;
re.lastIndex = 2;
re.exec('12!34');

// 对比全局匹配
var re = /\d{2}/g;
re.lastIndex = 2;
re.exec('12!34');
```

---

# Backtrack

--

# 回溯

???

```javascript
/ab{1,3}bbc/.test('abbbc');
```

```javascript
/".\*"/.test('"abc"de');
```

```javascript
/^\d{1,3}?\d{1,3}$/.test('12345');
```

- [示例 1](./images/huisu.png)

- [示例 2](./images/huisu1.png)

- [示例 3](./images/huisu2.png)

---

### 回到问题

- 匹配手机号
- 验证密码强度
- 驼峰转连字符风格
- 添加千分位分隔符
- 匹配形如 abba 的字符串

--

- 不匹配形如 abba 的字符串
- 验证一个字符串是否至少包含三个数字

???

```javascript
// 匹配手机号
/^((?!86-)\d+-\d{6,}|(86-)?1\d{10})$/.test('12456454542');
// 验证密码强度
const reg = /((?=.*\d)(?=.*[a-z])|(?=.*\d)(?=.*[A-Z])|(?=.*[a-z])(?=.*[AZ]))^[0-9A-Za-z]{6,12}$/;
const reg1 = /(?!^\d{6,12}$)(?!^[a-z]{6,12}$)(?!^[A-Z]{6,12}$)^[0-9A-Za-z]{6,12}$/;
// 驼峰转连字
'AsdfsdBsdasadC'.replace(/\B(?=[A-Z])/g, '-').toLowerCase();
'AsdfsdBsdasadC'.replace(/\B([A-Z])/g, '-$1').toLowerCase();
// 添加千分位
'123212312312312'.replace(/(?!^)(?=(\d{3})+$)/g, ',');
```

[不匹配 abba 类型 demo](./demo/1.html)

```javascript
/^(?!.*(.)(.)\2\1)/.test('312312abbdewr23423');
```

#### 验证字符串是否至少包含 3 位数字

```javascript
/(\D*\d){3}/.test('dasdas12');
```

---

```
    console.log("Thanks!");
```
