正则在平时工作中用的非常多, 最开始接触正则的时候感觉这个东东好难记啊,最近把正则的内容整理了一下,写成以下文章。

先给大家介绍一个在线解析正则的网站,来帮助我们理解正则,特别是复杂的正则表达式,非常好用

http://www.regexper.com

比如/^@[a-zA-Z]\d+@$/,解析之后图形帮助理解如下:

![reg解析](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8B%E5%8D%882.41.14.png)

# 什么是正则

正则表达式是用于匹配字符串中字符组合的模式。主要应用于正则对象的test和esec方法,以及字符串的search、split、match、replace中。

# 创建正则

## 字面量创建
```
var reg = /pattern/flag;
```

在正则表达式保持不变,也就是不需要每次都创建一个新的正则对象的时候,使用字面量创建的正则性能更好

每个正则表达式都可以带有一个或多个(也可以不带)表明正则表达式行为的标志

![正则flag](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8A%E5%8D%889.19.47.png)

## 构造函数创建

在js中提供了一个内置构造函数RegExp来创建一个正则对象
```
var reg =  new RegExp(pattern [, flags]);
```

```
var reg = /[ab]c/g;
// 等价于
var reg = new RegExp("[ab]c", g);
```

# 正则表达式中的特殊字符

## 元字符

> 元字符是在正则表达式中有特殊含义的非字母字符,js中正则表达式元字符包括:

? + *  ^ $ . | \ ( ) [ ] { }

因为这些字符在正则表达式中具有特殊含义,所以如果要想在字符串中匹配这些字符,就必须对它们进行转义.
```
// 匹配字符串中的ac或者bc
var reg = /[ab]c/;

// 如果要匹配字符串的"[ab]c", 需要对[]进行转义
var reg = /\[ab\]c/;

```

**注意:**
另外需要注意的是,由于使用构造函数创建正则,pattern参数必须为字符串,所有元字符如果需要在字符串中匹配这个字符,需要进行双重转义

```
var reg = /\[ab\]c/g;

如果使用构造函数创建正则表达式,应该写成:

var reg = new RegExp("\\[ab\\]c", g);
```

## 字符集合

我们可以使用[]来构建一个简单的类[xyz],类指符合某些特性的对象,是一个泛指,并不是指某个字符,表示匹配方括号的中任意字符,对于点（.）和星号（*）这样的特殊符号在一个字符集中没有特殊的意义。他们不必进行转义，不过转义也是起作用的。
```
var reg = /[abc]/g;
var reg2 = /[abc.]/g;  // 字符集合中的.
var reg3 = /[abc\.]/g;  // 字符集合中转义的.

var str = 'a1b2c3';
var str2 = 'a1b2c3d.';

var res = str.replace(reg, 'X');
var res2 = str2.replace(reg2, 'X');
var res3 = str3.replace(reg3, 'X');

console.log(res);  //  X1X2X3
console.log(res2);  //  X1X2X3dX
console.log(res3);  //  X1X2X3dX

```

## 字符集合取反

一个反向字符集[\^xyz], 它匹配任何没有包含在方括号中的字符
```
var reg = /[^abc]/g;
var str = 'abcdefg';
var res = str.replace(reg, 'X');
console.log(res);  //  abcXXXX
```
## 范围类 

在字符集合中可以使用(-)来指定一个字符范围, 如[a-z],表示匹配从a到z的任意字符
```
var reg = /[a-z]/g;
var str = 'a1b2c3d4e5F6';
var res = str.replace(reg, 'X');
console.log(res); //  X1X2X3X4X5F6
```
在范围类[]中可以连写,如同时匹配大小写,[a-zA-Z]

```
var reg = /[a-zA-Z]/g;
var str = 'a1b2c3d4e5F6';
var res = str.replace(reg, 'X');
console.log(res); //  X1X2X3X4X5X6
```
## 预定义类

正则表达式提供了预定义类,来匹配常见的字符类,不需要都通过字符集合去定义正则表达式

| 字符     |  等价类 |  含义 |
| -------- | --------| ---- |
|.|[\^\\n\\r]|匹配除回车符合换行符之外的任何单个字符|
|\\d|[0-9]|数字字符|
|\\D|[\^0-9]|非数字字符|
|\\s|[\\t\\n\\x0B\\f\\r]|空白符|
|\\S|[\^\\t\\n\\x0B\\f\\r]|非空白符|
|\\w|[a-zA-Z_]|单词字符(字母、数字、下划线)|
|\\W|[\^a-zA-Z_]|非单词字符|

## 边界
正则还提供了边界匹配符
| 字符     | 含义 |
| -------- | ---- |
|^|匹配输入的开始|
|$|匹配输入的结束|
|\\b|单词边界|
|\\B|非单词边界|

```
// ^的应用
var reg = /^@./g
var str = '@123abc@';
var res = str.replace(reg, 'X');
console.log(res);  // X23abc

// $的应用
var reg2 = /^.@$/g;
var str2 = '@123abc@';
var res2 = str2.replace(reg2, 'X');
console.log(res2);  // 123abX

// \b的应用
var reg3 = /\bis\b/g;
var str3 = 'this is javaScript';
var res3 = str3.replace(reg3, 'X');
console.log(res3); // this X javaScript
```

### 正则的m标志应用
```
var reg = /^@./g;
var str = `@abc
@123
@XYZ
`
var res = str.replace(reg, 'X');
// 因为即使字符串看上去换行,本质上还是一些换行符,只有结尾和结束
console.log(res);  //  Xbc
                       @123
                       @XYZ
```
当正则表达式使用m标志的时候,在一行文本末尾结束的时候,还会去匹配下一行是否存在与模式匹配的项,例子如下:
```
var reg = /^@./gm;
var str = `@abc
@123
@XYZ
`
var res = str.replace(reg, 'X');
console.log(res);  //  Xbc
                       X23
                       XYZ
```
## 量词

|字符|含义|
| -- | -- |
|?|匹配前面一个表达式0次或者1次(至多出现一次)|
|+|匹配前面一个表达式1次或者多次(至少出现一次)|
|*|匹配前一个表达式0次或多次(任意次)|
|{n}|n是一个正整数，匹配了前面一个字符刚好发生了n次|
|{n,m}|n 和 m 都是整数。匹配前面的字符至少n次，最多m次。如果 n 或者 m 的值是0， 这个值被忽略。|
|{n,}|匹配前面字符n此或者更多次(至少出现n次|

### 贪婪模式
贪婪模式是让正则表达式尽可能多的匹配
```
var reg = /\d{2,5}/;
var str = '12345678';
var res = str.replace(reg, 'X');
console.log(res); // X678
```
### 非贪婪模

非贪婪模式是让正则表达式尽可能少的匹配,一旦匹配成功不在继续匹配,做法是在量词后面加上?即可

```
var reg = /\d{2,5}?/
var str = '12345678';
var res = str.replace(reg, 'X');
console.log(res);  // X345678   
```

## 分组

### 使用括号()进行分组
量词不作用于紧挨着的某个字符,使用/(x)/匹配 'x',并且记住匹配项,括号被称为补货括号

```
var reg = /([a-zA-Z]\d)/g;
var str = 'a1bbc3D4efg';
var res = str.replace(reg, 'X');
console.log(res);  // XbbXXefg
```
使用在线解析正则工具如下图:
![res正则解析](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8B%E5%8D%883.01.07.png)

另外可以添加量词

```
var reg = /(abc){3}/g;
var str = 'abcabcabcabc';
var res = str.replace(reg, 'X');
console.log(res);  // Xabc
```
### 使用或' | '进行分组

```
var reg = /apple|pear/g
var str = 'appleappleHpearpear';
var res = str.replace(reg, 'X');
console.log(res);  // XXHXX

var reg2 = /appl(e|p)ear/g;
var str2 = 'appleearHapplpear'
var res2 = str2.replace(reg2, 'X');
console.log(res2);  // XHX
```
在线解析上面代码的reg和reg2如下:
reg:
![reg](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8B%E5%8D%883.18.46.png)

reg2:
![reg2](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8B%E5%8D%883.19.05.png)

### 捕获匹配到的分组内容

在replace替换环节,可以使用$1、$2、$3...$n等捕获分匹配到的分组

比如想把'25/12/2016'转换成'2016-12-25':

```
var reg = /(\d{2})\/(\d{2})\/(\d{4})/;
var str = '25/12/2016';
var res = str.replace(reg, '$3-$2-$1');
console.log(res);  // 2016-12-25
```
### 忽略分组

如果不希望捕获分组,只需要在分组内加上(?:)就可以

```
var reg = /(?:\d{2})\/(\d{2})\/(\d{4})/;
var str = '25/12/2016';
var res = str.replace(reg, '$3-$2-$1');

此时$2为2016,$1为12,而$3捕获不到,按普通字符串显示
console.log(res);  // $3-2016-12
```
## 正向肯定查找
x(?=y)
匹配x,并且x后必须跟着y,这就是正向肯定查找

```
var reg = /\w(?=\d)/g;
var str = 'a1b2ccd4';
var res = str.replace(reg, 'X');
console.log(res); // X1X2ccX4
```

## 正向否定查找
匹配x,并且x后必须不跟着y,这就是正向否定查找

```
var reg = /[a-z](?!\d)/g;
var str = 'a1b2ccd4';
var res = str.replace(reg, 'X');
console.log(res); // a1b2XXd4
```
# 正则对象属性和方法

## 属性

![正则对象属性](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-14%20%E4%B8%8B%E5%8D%884.03.10.png)

属性皆为只读,不可修改

```
var reg = /\[abc\]/;
console.log(reg.ignoreCase) // false;
console.log(reg.global) // false;
console.log(reg.multiline) // false;

reg.ignoreCase = true;
reg.global = true;
reg.multiline = true;

console.log(reg.ignoreCase) // false;
console.log(reg.global) // false;
console.log(reg.multiline) // false;
```
还是以上代码看一下source属性
```
console.log(reg.source) //  \[abc\]
```
如果使用构造函数创建正则对象,再来看一下source属性
```
var reg = new RegExp("\\[abc\\]");  //需要对元字符进行双重转义
console.log(reg.source);   // \[abc\]
```
通过以上对比可知,source属性是字面量形式创建正则对象所有的字符串
## 方法

### RegExp.prototype.test()

test() 方法执行一个检索，用来查看正则表达式与指定的字符串是否匹配。匹配到返回 true,否则返回false。

**语法**
```
regexObj.test(str)
```
```
var reg = /\w/;
var str = 'ab';
console.log(reg.test(str));  // true
console.log(reg.test(str));  // true
console.log(reg.test(str));  // true
```
**注意**
当正则表达式使用全局模式时,lastIndex属性会影响test()方法的返回值,看下面例子
```
var reg = /\w/g;
var str = 'ab';
console.log(reg.test(str));  // true
console.log(reg.test(str));  // true
console.log(reg.test(str));  // false
console.log(reg.test(str));  // true
```
为什么会出现这种现象呢,是因为正则表达式执行test方法时,每次都会把结果作用到操作的正则实例上,由于是全局匹配,第一次匹配到之后reg的lastIndex属性为1,继续匹配,此时从lastIndex的位置开始匹配,即从b开始,结果又匹配到,此时lastIndex属性为2，当继续匹配时,从2开始匹配,没有匹配到,此时返回false,lastIndex被重置为0,所以第4次执行console.log(reg.test(str))就会从新从0开始,所以返回值为true。结合while循环来说明一下:

```
var reg = /\w/g;
var str = 'ab';
while(reg.test(str)){
    console.log(reg.lastIndex);  // 循环执行两次,分别打印出1, 2
}
```
### RegExp.prototype.exec()

exec() 方法在一个指定字符串中执行一个搜索匹配.

**语法**
```
regexObj.exec(str)
```
**返回值:**

1.如果匹配失败，返回 null。
2.如果匹配成功，exec() 方法返回一个数组，并更新正则表达式对象的属性,一般来说主要是lastIndex属性值的更新。返回的数组将完全匹配成功的文本作为第一项，将正则括号里匹配成功的作为数组填充到后面,返回值虽然是Array实例,但是包含了index和input属性

index: 表示匹配项在字符串中的位置,也就是匹配项第一个字符的位置
input: 表示应用正则表达式的字符串

#### 非全局调用

返回数组内容包括:
1. 第一个元素是与正则表达式相匹配的文本
2. 第二个元素是与正则对象第一个子表达式相匹配的文本,也就是第一个分组(如果有的话)
2. 第三个元素是与正则对象第二个子表达式相匹配的文本,也就是第而个分组(如果有的话),以此类推

```
var reg = /\d(\w)(\w)\d/;
var str = '@1bb2c3dd4f';
var res = reg.exec(str);
console.log(reg.lastIndex);  // 0  非全局模式忽略lastIndex属性
console.log(res.index);  // 1
console.log(res.input);  // @1ab2c3dd4f
console.log(res);  // ["1ab2", "a", "b"]
```

#### 全局调用

```
var reg = /\d(\w)(\w)\d/g;
var str = '@1bb2c3dd4f';
var res = reg.exec(str);
console.log(reg.lastIndex);  // 5  非全局模式忽略lastIndex属性
console.log(res.index);  // 1
console.log(res.input);  // @1ab2c3dd4f
console.log(res);  // ["1ab2", "a", "b"]
console.log(reg.lastIndex); 
```
使用while循环加深一下理解
```
var reg = /\d(\w)(\w)\d/g;
var str = '@1bb2c3dd4f';
while(reg.exec(str)) {
    console.log(reg.lastIndex, res.index, res);
    // 打印两次结果分别为
    // 5, 1, ["1bb2", "b", "b"]
    // 10, 6, ["3dd4", "d", "d"]
}
```

# 字符串对象方法

## String.prototype.search()
方法执行正则表达式和 String对象之间的一个搜索匹配,如果匹配成功,返回正则表达式在字符串中首次匹配项的索引,否则返回-1。

**语法:**
```
str.search(regexp)
```

如果传入一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象。

## String.prototype.match()

用于搜索字符串,找到一个或多个与regexp匹配的文本
**语法:**
```
str.match(regexp);
```

**返回值:**

一个包含了整个匹配结果以及任何括号捕获的匹配结果的 Array ；如果没有匹配项，则返回 null。regexp是否有g标志对返回值有很大影响。

### 非全局调用(不包含g标志)

返回值和RegExp.prototype.exec()方法一样,就不细说了。

### 全局调用(包含g标志)

1. 没有找到任何匹配的字符串,返回null
2. 如果找到了一个或多个匹配的字符串,则返回一个数组,存放字符串中所有匹配的字符串,不包含捕获内容,也不具有index和input属性。

```
var reg = /\d(\w)\d/g;
var str = '1a2b3c4d';
var res = str.match(reg);
console.log(res);  // ["1a2", "3c4"]
console.log(res.index);  // undefined
console.log(res.input);  // undefined
```

## String.prototype.split()

split() 方法使用指定的分隔符字符串将一个String对象分割成字符串数组

**语法:**
```
str.split([separator[, limit]]);
```
![split](http://ovqwwz784.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-10-16%20%E4%B8%8A%E5%8D%8810.17.52.png)

### separator 参数
当separator为字符串时,其实也是默认转成正则去执行

```
var str = 'a, b, c, d';
var arr = str.split(',');
var arr2 = str.split(/,/);
console.log(arr);  // ["a", " b", " c", " d"]
console.log(arr2);  // ["a", " b", " c", " d"]
``` 
### separator带捕获括号
如果 separator 包含捕获括号（capturing parentheses），则其匹配结果将会包含在返回的数组中。
```
var str = 'a1b2c3d';
var arr = str.split(/(\d)/);
console.log(arr); // ["a", "1", "b", "2", "c", "3", "d"]
```

### limit参数
限制返回值中分割元素数量
```
var str = 'a b c d e';
var arr = str.split(' ', 3);
console.log(arr); //  ["a", "b", "c"]
```

## String.prototype.replace()

replace() 方法返回一个由替换值替换一些或所有匹配的模式后的新字符串。模式可以是一个字符串或者一个正则表达式, 替换值可以是一个字符串或者一个每次匹配都要调用的函数。

**注意:** 原字符串不会改变。

**语法:**

```
str.replace(regexp|substr, newSubStr|function)
```
###  String.prototype.replace(substr, newSubStr) 

```
var str = 'a1b2c3d';
var resStr = str.replace('1', 'X');
console.log(resStr); //  
```
###  String.prototype.replace(regexp, newSubStr)  
```
var str = 'a1b2c3d';
var resStr = str.replace(/\d/g, 'X');
console.log(resStr); //  aXbXcXd
```
###  String.prototype.replace(regexp, function)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

function会在每次匹配替换的时候调用,包含四个可选参数

1. 匹配到的字符串
2. 正则表达式分组内容,没有分组就没有该参数
3. 匹配项在字符串中的index
4. 原字符串

**例子:**

比如要把'a1b2c3'替换后的结果为'a2b3c4'

```
var str = 'a1b2c3';
var resStr = str.replace(/\d/g, function(matchStr, index, originStr) {
    // 此时正则表达式中无捕获,function中则无分组参数
    return parseInt(matchStr) + 1;
});
console.log(resStr); // a2b3c4
```

当正则表达式中有捕获时,再看一下另外一个例子:
```
var str = 'a1b2c3d4e';
var resStr = str.replace(/(\d)(\w)(\d)/g, function(matchStr, group1, group2, group3, index, originStr) {
    // 会执行两次回调,打印结果分别如下
    console.log(matchStr)  // 1b2   3d4
    console.log(group1);   // 1     3
    console.log(group2);   // b     d
    console.log(group3);   // 2     4
    return group1 + group3; //把匹配到的文本替换成group1 + group3字符串拼接后的值
});
// 把匹配到的1b2替换成group1 + group3(12), 3d4替换成(34)
console.log(resStr); // a12c34e  把匹配到的1b2替换成group1 + group3(12)
```

以上就是我总结的正则表达式相关知识, 感觉把正则搞清楚还是很爽滴, 如发现有问题请多多指教。
