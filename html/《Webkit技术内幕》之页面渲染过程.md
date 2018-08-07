> 最近拜读了传说中的《Webkit技术内幕》一书，有很大收获，尤其是对页面渲染有了较深的认识。由于功力有限，而且书中设计到较多的底层也无法理解，所以本文主要整理和分享一下自己理解的基于Webkit内核浏览器的页面渲染的整体过程，不做深入介绍(也做不了深入介绍)。

# 浏览器内核

> 浏览器之所以能呈现出更重页面，正是由于浏览器内核的存在，也被称为渲染引擎，主要作用就是，也就是能把HTML/CSS/JavaScript文本其相关的资源转化转化成可视化可交互的图像页面，这个过程就是渲染。

# 四大主流内核

 内核| 浏览器 | 内核识别码
--------- | -------------| -------------
Trident | IE、360        | -ms
--------- | -------------| -------------
Gecko | Firefox(火狐)     | -moz
--------- | -------------| -------------
presto | Opera(欧朋)     | -o
--------- | -------------| -------------
Webkit | Chrome(谷歌) safari(苹果)| -webkit


# Webkit架构

本文主要是基于Webkit内核，所以先放一张整理Webkit架构图(并非全部模块, 主要包含主要模块),大致介绍一下Webkit的架构

![webkit架构](http://ovqwwz784.bkt.clouddn.com/Webkit%E6%9E%B6%E6%9E%84.png)

自下而上主要分为三层:

* 1.操作系统层
* 2.Webkit渲染过程中依赖的很多第三方模块
* 3.Webkit层，主要包含webCore、渲染引擎层、嵌入式接口层，绑定层

### 1.操作系统层

由Webkit可以运行在不同的操作系统上，浏览器也可以运行在不同的操作系统上，另外与对磁盘和内存的操作都需要操作系统来操作，所以需要操作系统支持。说白了就是一切需要对硬件操作的程序都需要操作系统的支持。

### 2.第三方模块层

资源的获取、页面的渲染等需要依赖第三方库来完成，网络库、图形库、视频库等都是Webkit之所以能工作的基础，Webkit来根据需要来使用相应的库。

### 3. WebKitCore

WebKitCore部分是所有浏览器共享的部分，是渲染网页的基础，包括HTML解释器、CSS解释器、SVG、DOM、渲染树等

* 1.HTML解释器

> 解释HTML文本的解释器，把html文档解析成DOM(文档对象模型)树，表示整个html文档

* 2.CSS解释器
 
> 问DOM树中各个元素对象计算出样式信息，后计算后面的网页布局做基础

* 3.布局

> 把DOM树和解析后的CSS树的信息结合起来，形成一个包含页面所有元素和样式的信息的一个内部表示模型

* 4.绘图

>使用第三方依赖的库如2D/3D图形库等将布局计算后的节点绘制成图像 

### 4. JavaScript引擎

对于JavaScript引擎有必要说一下，JavaScript引擎就是将JavaScript代码解析处理并运行的环境，负责整个JavaScript程序的编译及执行过程。

1.webkit默认的引擎是JavaScriptCore引擎，对于不同浏览器对于引擎的实现JavaScript引擎的实现也不一样，比如Chrome浏览器是基于V8引擎等。

2.另外在解析/语法分析，构建出"抽象语法树"之后，会将"抽象语法树"进行编译，转化为一组机器指令，拿一个例子来说

```
var a = 1
```
JavaScript会创建一个变量a，并分配内存，将1这个值存储在a的变量中。


3.JavaScript可以修改网页内容，也就是修改DOM和和CSS样式，事实上，也正是javaScript代码通过DOM和CSSOM暴露出的接口来进行修改，从而改变页面渲染的效果

### 5. 绑定层和嵌入式接口层

绑定层和嵌入式接口层最主要与Webkit项目的移植有关，就比如基于linux内核的CentOS、Ubuntu等系统一样。嵌入式接口层主要提供给浏览器调用，不同浏览器厂商会基Webkit以及对外暴露的接口实现自己的功能。

# 页面渲染过程

**从输入url开始整体介绍一下页面的渲染流程，然后具体步骤再详细加以说明**

* 1.用户输入url按下回车后，浏览器开启一个进程对url进行分析，如果是http协议，按照web方式处理
* 2.根据域名进行DNS解析，解析之后发送第二次get请求，进行HTTP协议会话，以获取资源
* 3.此时进入到web服务器上的 Web Server，如 Apache、Tomcat、Node.JS 等web服务器;
* 4.继续进入后端应用程序，如PHP、Java等找到对应请求处理，最后由web服务器传送给浏览器资源
* 5.因为资源的位置以URL(统一资源定位符)来区分，发送请求时，如果本地有缓存，发送请求的同时会带上缓存的相关信息，与服务器资源作比较，比如更新时间等，服务器如果没有更新则返回304，直接使用缓存，否则向服务器请求资源。
* 5.浏览器开始下载HTML文档
* 6.浏览器内核开始解析文档(整个html文档就是一大串字符串)，构建DOM树，解析成DOM树的过程中，如果遇到JavaScript代码，则交给JavaScript引擎来执行，等到DOM树构建完成后触发DOMContentLoaded事件
* 7.解析CSS，构建CSS树，构建CSSOM(在浏览器控制台输入document.styleSheets按下回车就可以看到styleSheetList的集合了)
* 8.CSS解析完成之后，在DOM树的基础上附加解释后的样式信息，形成RenderObject树，在RenderObject节点创建的同时，webkit会根据网页层次构建出RenderLayer树，同时构建出一个虚拟的绘图上下文(与平台无关的抽象类)，最后根据绘图上下文(需要依赖2D/3D库)进行绘制，最终也就是用户看到的可交互的页面。

# 页面渲染细节

上面介绍了页面从输入url发送请求后到页面渲染的整体流程，下面补充一下在这整个过程中的一些细节知识点，方面更好的理解页面的渲染过程

**1. 首先理解页面是分层次的，比如说如下代码片段代码**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div>
        <p>aaa</p>
        <video src="xxx.mp4"></video>
    </div>
</body>
</html>
```

当网页构建层次的时候，html元素为根层，此时创建一个层，html元素的所有子节点、子节点的子节点，依次类推，如dody，div，p为普通元素，并不会创建新的层次接口，video元素需要进行创建新层来进行视频处理和渲染。

如下情况，都需要建立新的RenderLayer节点

![建立新的RenderLayer节点](http://ovqwwz784.bkt.clouddn.com/%E9%9C%80%E8%A6%81%E5%BB%BA%E7%AB%8BReanderLayer%E8%8A%82%E7%82%B9%E7%9A%84%E6%83%85%E5%86%B5.png)

**备注**: 图片直接截取于《Webkit技术内幕》

**2.从资源字节流到构建成DOM树**

![资源字节流到构建成DOM树](http://ovqwwz784.bkt.clouddn.com/%E8%B5%84%E6%BA%90%E5%AD%97%E8%8A%82%E6%B5%81%E5%88%B0DOM%E6%A0%91.png)

**备注:** 图片直接截取于《Webkit技术内幕》

* 1.分词/词法分析，从字节流到字符流流(串)，是分段的词法解释器会将字符串解释成标记(相当于字典中的词语)

如 var a = 1; var、a、=、1 、;。空格是否会被当作词法单元，取决于空格在
这门语言中是否具有意义。

* 2.解析/语法分析，基于词法解释器生成的新标记，构建成“抽象语法树”，解析器尝试将其与某条语法规则进行匹配。如果发现了匹配规则，解析器会将一个对应于该标记的节点添加到解析树中，然后继续请求下一个标记

**3.HTML文档对应的DOM树的对应关系**

![HTML文档对应的DOM树的对应关系](http://ovqwwz784.bkt.clouddn.com/HTML%E7%BD%91%E9%A1%B5%E5%88%B0DOM%E6%A0%91.png)


**4.DOM树和RenderObject树之间的对应关系**

![DOM树和RenderObject树](http://ovqwwz784.bkt.clouddn.com/DOM%E6%A0%91%E8%8A%82%E7%82%B9%E5%92%8CRenderObject%E6%A0%91%E8%8A%82%E7%82%B9%E7%9A%84%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB.png)

**备注:** 图片直接截取于《Webkit技术内幕》

**5. RenderObject树与RenderLayer树之间的对应关系**

多对一的关系，RenderObject多个节点可以对应RenderLayer的一个层次结构

![RenderObject树和RenderLayer](http://ovqwwz784.bkt.clouddn.com/RenderObject%E6%A0%91%E8%8A%82%E7%82%B9%E5%92%8CRenderLayer%E6%A0%91%E8%8A%82%E7%82%B9%E7%9A%84%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB.png)

**备注:** 图片直接截取于《Webkit技术内幕》

以上文章就是整理的页面渲染整体流程，很多细节没有具体讲，比如解析html文档时，利用栈来处理、绘制3D图形的GPU加速等、由于能力有限不敢多讲，也难讲清楚，这些还是去看大神们具体的书比较，感兴趣的小伙伴，《WebKit技术内幕》了解一下

本文主要参考资料:

[WebKit技术内幕](https://book.douban.com/subject/25910556/)
[Chrome 渲染优化 - 层模型](http://blog.jobbole.com/42967/)
[前端文摘：深入解析浏览器的幕后工作原理](http://www.cnblogs.com/lhb25/p/how-browsers-work.html)