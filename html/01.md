# HTML文档
>HTML指的是超文本标记语言 (Hyper Text Markup Language)，HTML文档就是我们常说的网页，一个标准的HTML文档由**文档元素**和**元数据元素**组成，二者用来创建HTML文档以及其内容。

顺便说一下什么是元数据元素: 用来构建HTML文档的基本结构，以及就如何处理文档向浏览器提供信息和指示，它们本身不是文档内容，但提供了关于后面文档内容的信息。包含在head内，如title、base、meta等都是元数据元素。本文不做重点介绍。

## 文档元素
文档元素一共有四个**DOCTYPE**、**html**、**head**、**body**

### DOCTYPE

每一个HTML文档都必须由DOCTYPE元素开头，告诉浏览器要处理的是HTML文档，在HTML5中DOCTYPE 声明变得非常简单而且唯一，不用写版本号浏览器也能识别这是HTML5文档，因为和之前的HTML版本有所差异
    <!DOCTYPE html> //HTML5声明

在 HTML 4.01 中，<!DOCTYPE> 声明引用 DTD，因为 HTML 4.01 基于 SGML。DTD 规定了标记语言的规则，这样浏览器才能正确地呈现内容。HTML5 不基于 SGML，所以不需要引用 DTD。--[W3school](http://www.w3school.com.cn/tags/tag_doctype.asp)

**HTML4中声明如下:**

HTML 4.01 Strict(不允许使用框架集):
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

HTML 4.01 Frameset(允许使用框架集):
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">

### html
html表示根元素，表示HTML文档的开始，必须需要的元素，html具有如下属性:

|属性|值|功能|
| -- | -- | -- |
|manifest|url|定义一个url，描述文档的缓存信息|
| xmlns | "http://www.w3.org/1999/xhtml" |定义 XML namespace 属性(其实这个我也没用过不懂啊)，感兴趣的大神自己研究下吧|

### head
>包含文档的元数据，向浏览器提供文档内容和标记的信息，还包括脚本和对外资源的引用，如引入外联.css文件、js文件等。

|包含元素|是否必须|功能|
| -- | -- | -- |
|title|是|必须有一个title元素，定义网站的标题|
|base |否|设置一个基准URL，让HTML文档中的相对链接在此基础上解析|
|meta |否|定义各种文档元数据，可见我的上一篇文章[<meta>常用属性总结](https://segmentfault.com/a/1190000010342600)|
|style |否|1. 指定样式类型; 2. 指定样式作用范围; 3. 指定样式使用的媒体类型|
|link |否|1. 载入外部样式表; 2. 为页面定义网站标志; 3. 预先获取关联资源 |
|script |否|1. 定义文档内嵌脚本; 2. 载入外部脚本|
|noscript |否|可以向禁用或不知JavaScript的浏览器展示一些内容|

- **<base>介绍:**
如果在浏览器地址为"https://segmentfault.com/demo/index.html"中载入一个文档，代码如下:
```
<DOCTYPE html>
<html>
    <head>
        <base href="https://segmentfault.com/">
    </head>
    <body>
        <a href="page2.html">跳转</a>
    </body>
</html>
```
正常点击a链接，浏览器将从"https://segmentfault.com/page2.html"中加载文档
**如果不包含<base>元素**，浏览器将从"https://segmentfault.com/demo/index.html"中加载文档地址

- **style**

style元素拥有局部属性:type、scoped、media，其对应作用如下:

**1.指定内嵌样式，type属性是高速浏览器文档所定义的类型、这个值只有一种text/css**
```
<style>
    .box {
        width: 200px;
        height: 200px;
        background: red;
    }
</style>
```
**2.指定样式范围，scoped属性的作用为style元素内定义的样式只作用于该元素的父级及其所有兄弟元素**


**3.media属性表明文档在指定的设备下显示其定义的样式**
```
<style media="screen" type="text/css">
    .box {
        width: 200px;
        height: 200px;
        background: red;
    }
</style>
```
media属性所有的设备值如下表(--HTML5权威指南)，很多在工作中几乎都用不上，列出来了解一下及以备查阅:

|设备(media的值)|说明|
|--|--|
|all|将样式用于所有设备(默认值)|
|aural|将样式用于语音合成器|
|braille|将样式用于盲文设备|
|handheld|将样式用于手持设备|
|projection|讲样式用于投影机|
|print|将样式用于打印预览和打印页面时|
|screen|将样式用于计算机显示屏幕|
|tty|将样式用于电传打印机之类的等宽设备|
|tv|将样式用于电视机|

media不只能规定设备，还能定义更具体的使用条件，举例如下: 
```
<style media="screen AND (max-width:500px)" type="text/css">
//只在浏览器宽度小于500px的情况下生效
    .box {
        width: 200px;
        height: 200px;
        background: red;
    }
</style>
```
```
<style media="screen AND (mix-width:500px)" type="text/css">
//只在浏览器宽度大于500px的情况下生效
    .box {
        width: 200px;
        height: 200px;
        background: blue;
    }
</style>
```
除了用AND来定义具体条件，还可以使用NOT和OR(','表示)，另外还有其他供media使用的特性就不列举了，大家可以自己去百度了，再补充一个吧，device-width，这些特性都可以结合max-/mix-来使用

- **link**

拥有的局部属性如下表:

|属性|说明|
|--|--|
|href|指定引入资源的URL|
|hreflang|说明关联资源使用的语言|
|media|说明关联内容用于哪种设备|
|rel|说明文档与关联资源的关系类型，属性值决定了浏览器如何解析link元素|
|sizes|HTML5中新增，指定网站图标大小|
|type|指定所关联资源的[MIME](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types)类型，如text/css|

rel属性常用值[rel值列表](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Link_types)及其表示的link元素功能

|属性|说明|
|--|--|
|stylesheet|载入外部样式表|
|icon|指定图标资源|
|prefetch|预先获取一个资源|

**1.引入外联样式表**
    <link rel="stylesheet" type="text/css" href="index.css">
    
**2.添加网站图标**
    <link rel="shortcut icon" type="image/x-icon" href="favicon.ico">
     //href属性值为图片路径
**3.预先获取关联的资源**
    <link rel="prefetch"  href="demo.html">
rel属性值设置为prefetch，可以预先加载demo.html，如果页面存在链接如下，为需要demo.html页面的操作做好准备
```
   <a href="demo.html">demo</a>
```
- **script**

>script元素可以定义页面内嵌脚本、引入外部文件脚本并通过script本身局部属性值设定加载脚本的各种方式

script的常用局部属性及其说明如下表:

|属性|说明|
|--|--|
|type|表示引用或定义的脚本类型，如果是javascript脚本，此属性可以省略，省略type属性时浏览器默认为使用JavaScript脚本
|src|指定加载外部脚本资源的URL|
|defer|设定脚本执行的方式为延迟执行，告诉浏览器等页面载入和解析完毕才能执行此脚本，只能与src属性一起使用|
|async|设定脚本执行的方式为异步执行，只能与src属性一起使用|
|charset|说明外部脚本资源的字符编码，只能与src属性一起使用|

**1.定义文档内嵌脚本** 
```
<script>
    window.onload = function() {
        alert('页面加载完成');
    } 
</script>
```
一般情况下script元素应该放在文档最后，等页面全部加载完成后才去执行，保证脚本文件内可以获取到当前页面的全部内容。

如果script元素在head内，如果是当前内嵌脚本，则可以添加window.onload来告诉浏览器当所有页面全部加载完成才去执行。

**2.载入外部脚本**
    <script src="index.js"></script>
    
**3.使用defer属性延迟加载外部脚本**
    <script defer src="index.js"></script>
    
如果在head中使用script元素，defer属性将会在HTML文档所有元素都解析完毕之后才加载和执行。

*注意*:defer属性只能处理外部脚本，对内嵌脚本无效。

**4.使用async属性异步执行脚本**
    <script async src="index.js"></script>

浏览器在解析script元素时的默认行为是加载和执行脚本的时候暂停处理页面，各script元素按顺序同步执行。

使用async属性可以使外部脚本在加载HTML时异步执行，如何使用需结合具体产品功能需求，不过带来的影响是，页面中的脚本不能再按次序同步执行，所以如果当前脚本中与其他脚本有关联，则不适合使用async属性

### body

HTML文档的元数据和文档信息都包含在head内，文档内容包含在body内，body紧跟在head后面，具体不在赘述！

本文很多概念和属性表格结合《HTML5权威指南》、W3school、MDN，由于博主能力有限，很多概念直接拿过来引用，并附上相关链接，以保证其正确性，对HTML文档的相关知识作一个简单的总结，希望能帮助到需要的人，同时也方便自己后续查阅。