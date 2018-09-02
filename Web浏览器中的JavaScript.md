# Web浏览器中的JavaScript

考虑Web浏览器中市如何呈现Web页面的。一些呈现的静态信息的页面，叫做**文档**(document)(由于加入了JavaScript，静态页面的信息看上去会动来动去，但信息本身是静态的)，相对于文档来说，其他Web页面则感觉更像是应用。

## 1.客户端的JavaScript

Window对象是所有客户端JavaScript特性和API的主要接入点。它表示Web浏览器的一个窗口或者窗体，并且可以用标识符window来引用它。

属性：

指代Location对象的location属性，Location对象指定当前显示在窗口中的URL，并允许脚本往窗口中载入新的URL:

```js
//设置location属性，从而跳转到新的web页面
window.location = "http://www.4399.com"
```

Window对象方法

alert()可以弹出一个对话框用来显示一些信息

setTimeout()，可以注册一个函数，在给定的一段时间之后触发一个回调

```js
//等待两秒，然后说hello
setTimeout(function() { alert("hello world");}, 2000);
```

Window的一个重要属性document，它引用Document对象

```js
//查找id为timestamp的元素
var timestamp = document.getElementById("timestamp");

//如果元素为空，往里面插入当前时间
if(timestamp.firstChild == null)
    timestamp.appendChild(document.createTextNode(new Data().toString()));

//修改样式和类名
timestamp.style.backgroundColor = "yellow";
timestamp.className = "hightlight";

//注册事件
timestamp.onclick = function() { this.innerHTML = new Date().toString(); }
```

显示内容的简单客户端JavaScript

```html
<!DOCTYPE html>
<html>
<head>
	<title>Test</title>
	<style>
		.reveal *{display: none;}
		.reveal *.handle{display: block;}
	</style>
	<script>
		window.onload = function(){
			var elements = document.getElementsByClassName("reveal");
			for(var i = 0; i < elements.length; i++){
				var elt = elements[i];
				var title = elt.getElementsByClassName("handle")[0];
				addHandler(title,elt);
			}
			function addHandler(title,elt){
				title.onclick = function(){
					if(elt.className == "reveal")
						elt.className = "revealed";
					else if(elt.className == "revealed")
						elt.className = "reveal";
				}
			}
		};
	</script>
</head>
<body>
	<div class="reveal">
		<h1 class="handle">Click Here to Reveal Hidden Text</h1>
		<p>This paragragh is hidden. It appears when you click on title.</p>
	</div>
</body>
</html>
```

### 1.1Web文档里的JavaScript

JavaScript程序可以通过Document对象和它包含的Element对象遍历和管理文档内容。它可以操纵CSS样式和类，修改文档内容的呈现。内容、呈现和行为的组合，叫动态HTML或DHTML。

Web文档中就应当尽量少使用JavaScript。用户的体验不应依赖于JavaScript，但JavaScript可以增强体验，比如

- 创建动画和其他视觉效果，巧妙地引导和帮助用户进行页面导航
- 对表格的列警醒分组，让用户更容易找到所需要的
- 隐藏某些内容，当用户“深入”到内容里时，再逐渐展示详细信息

### 1.2Web应用里的JavaScript

要真正理解Web应用，需要认识到Web浏览器已经有了好的发展，现在已经不仅仅是作为显示文档的工具的角色了，而渐渐变成了一个简易的操作系统。

谨记Web浏览器是简单操作系统的概念，这样就可以把Web应用定义为用JavaScript访问更多浏览器提供的高级服务（比如网络、图像和数据存储）的Web页面。

## 2.在HTML里嵌入JavaScript

4种方法

- 内联，放置在`<script>`和`</script>`标签之间
- 放置在`</script>`的src属性指定的外部文件中
- 放置在HTML事件处理程序中，该事件处理程序有onclick或onmouseover这样的HTML属性指定
- 放置一个URL里，这个URL使用特殊的“javascript:”协议

### 2.1`<script>`元素

```html
<script>
	//你的JavaScript代码
</script>

//如果JavaScript代码中包括"<","&"字符
<script>
    <![CDATA[
    //你的JavaScript代码
    ]]>
</script>
```

### 2.2外部文件中脚本

```html
<scripr src="../../scripts/utils.js"></scripr>
```

结束的`</script>`的标签不能丢的。在XHTML可以使用`<script/>`标签

src属性优点

- 可以把大块的JavaScript代码从HTML文件中删除，这有助于保持内容和行为分离，从而简化HTML文件
- 如果多个Web页面共用相同的JavaScript代码，用src属性可以让你只管理一份代码，而不用再代码改变是编辑每个HTML文件
- 如果一个JavaScript代码文件有多个页面共享，就只需下载它一次，通过使用它的第一个页面——随后的页面可以从浏览器缓存检索它
- 由于src属性的值可以是任意的URL，因此来自一个Web服务器的JavaScript程序或Web页面可以使用由另一个Web服务器输出的代码。很多互联网广告依赖于此
- 从其他网站载入脚本的能力，可以让我们更好地利用缓存。Google正在为通用客户端类库推广标准且好标记的URL，可以让浏览器缓存一份副本，并且网络上的任意站点都可以使用

从文档服务器之外的服务器载入脚本有重要的安全隐患，使用同源安全策略来阻止交互

### 2.3脚本类型

type属性默认为text/javascript,老浏览器使用language属性代替type，值为javascript，但被废弃了

当Web服务器遇到`<script>`元素，并且这个`<script>`元素包含其值不被浏览器识别的type属性时，它会解析这个元素但不会尝试显示或执行它的内容。

### 2.4HTML中的事件处理程序

```html
<input type="checkbox" name="options" value="giftwrap" 
       onchange="order.options.giftwrap = this.checked;">
```

HTML中定义的事件处理程序的属性可以包括任意条JavaScript语句，相互之间用逗号分隔。

但这样是一个不好的习惯，开发者习惯用内容与行为分离

### 2.5URL中的JavaScript

```html
<a href="javascript:new Date().toLocaleTimeString();">What time is it?</a>

<a href="javascript:alert(new Date().toLocaleTimeString());">检查时间，而不必覆盖整个文档</a>

<!--void操作符强制函数抵用或给表达式赋予undefined值，如果URL里没有void操作符，调用
window.open()方法返回的值被转化为字符串表示，而当前文档也会被覆盖[object Window]-->
<a href="javascript:void window.open('about:blank');">打开一个窗口</a>
```

在URL后面跟一个javascript:协议限定符。

它被单做单独的一行代码对待，这意味着语句之间必须用分号隔开，而//注释/* */注释代替。

**书签bookmarklet**

只要书签不返回值，它就可以操作当前显示的任何文档，而不把文档替换成新的内容

```html
<a href='javascript:
   var e = "",r = "";/*  需要计算的表达式和结果 */
         do{
         /* 输出表达式和结果，并且求输入新的表达式*/
         e = prompt("Expression: " + e + "\n" + r + "\n" ,e);
         try( r = "Result: ) +eval(e); }/* 尝试计算这个表达式 */
         catch(ex){r = ex; }/* 否则记住这个错误*/
         }while(e);
         void 0;/* 这句到吗用以防止当前文档的覆盖*/
         '></a>
```

## 3.JavaScript程序执行

两个阶段：

1.载入文档内容，并执行`<script>`元素里的代码（包括内联和外部脚本）

2.异步，是由事件驱动的。

### 3.1同步、异步和延迟的脚本

JavaScript第一次添加到Web浏览器时，还没有API可以用来遍历和操作文档的结构和内容。当文档还在载入时，JavaScript影响文档内容的唯一方法是快速生成内容。它使用document.write()方法完成上述任务。

载入时生成文档内容

```html
<h1>test</h1>
<script>
    function a(n){
        if(n <= 1) return n;
        else return n*a(n-1);
    }
    document.write("<table>");
    document.write("<tr><th>n<\th><th>n!</th></tr>");
    for(var i = 0; i <=10; i++){
        document.write("<tr><td>" + i + "</td><td>" + a(n) + "</td></tr>");
    }
    document.write("</table>");
    document.write("Generated at " + new Date());
</script>
```

脚本的执行只在默认情况下是同步和阻塞的。`<script>`有defer和async属性，HTML5这些属性只在联合src属性使用才有用

```html
<script defer src="defeered.js"></script>
<script async src="async.js"></script>
```

defer属性是的浏览器延迟脚本的执行，直到文档的载入和解析完成，并可以操作

async属性是的浏览器可以尽快地执行脚本，而不用在下载脚本是阻塞文档解析

异步载入并执行脚本

```js
function loadasync(url){
    var head = document.getElementsByTagName("head")[0];
    var s= documents.createElement("script");
    s.src = url;
    head.appendChild(s);
}
```

### 3.2事件驱动的JavaScript

注册事件程序来写程序

```js
window.onload = function(){};
document.getElementById("button1").onclick = function(){};
function handleResponse(){};
request.onreadystatechange = handleResponse;


//第三个参数false为事件冒泡，true为事件捕获
window.addEventListner("load",function(){},false);
request.addEventListner("onreadystatechange",function(){},false);

//IE8及以前采用attachEvent
window.attachEvent("onload",function(){});
```

onLoad()，当文档载入完成是调用一个函数

```js
function onLoad(f){
    if(onLoad.loaded)
        window.setTime(f,0);
    else if(window.addEventListner)
        window.addEventListner("load",f,false);
    else if(window.attachEvent)
        window.attachEvent("onload",f);
}
onLoad.loaded = false;
OnLoad(function() { onLoad.loaded = true; });
```

### 3.3客户端JavaScript的线程模型

JavaScript语言核心并不包含任何线程机制。HTML5定义了一种作为后台形成的“WebWorker”，用来执行计算密集任务而不冻结用户界面的后台线程

### 3.4客户端JavaScript时间线

1.Web浏览器创建Document对象，解析Web页面，解析Element对象和Text节点，此时readystate属性值为“loading”

2.当没有遇到async和defer时，把这些script元素加入文档，然后执行行内和外部脚本，这些脚本会同步执行，并且在脚本下载和执行时解析器会暂停。

3.遇到async时，下载脚本，并继续解析文档

4.文档完成解析，readystate属性值为“interactive”

5.defer属性脚本

6.Document对象触发DOMContentLoaded时间

7.readystate属性值为“complete”

8.调用异步事件

## 4.兼容性和互用性

3类

1.演化，浏览器版本提升

2.未实现 不同浏览器规范不同

3.bug 每种浏览器都存在bug

处理方法

### 4.1处理兼容性问题的类库

使用其他js处理

### 4.2分级浏览器支持

A通过所有测试用例

C不需要所有用例都通过

X不是A和C

### 4.3功能测试

通过几个if/else检测当前代码是否邮箱

### 4.4怪异模式和标准模式

CSS显示模式的不同，顶部声明了DOCTYPE按标准模式渲染，否则按怪异模式

document.compatMode属性可以检测渲染模式，CSS1Compat是标准模式，BackCompat或者undefined为怪异模式

1、**标准模式**：浏览器按W3C标准解析执行代码。 
① 如果XHTML文档包含完整的DOCTYPE，那么它一般以标准模式呈现； 
② 包含严格dtd的doctype和包含过渡dtd和URI的doctype常常导致页面以标准模式呈现；

2、**怪异模式**：兼容老页面。使用浏览器自己的方式解析执行代码，因为不同浏览器解析执行的方式不一样，所以我们称之为怪异模式。 
① 不存在doctype或形式不正确会导致怪异模式； 
② 有过渡/框架dtd没有URI会导致页面以怪异模式呈现； 
③ IE中，如果doctype声明在xml之后，会导致怪异模式。

3、**近标准模式**：与标准模式一致，除了在处理下面这种情况时： 
如果一个块级元素除了空白文本（空格，tab等字符）外再无其它内容，则它的高度按0处理；如果有子元素，则它的高度不能比子元素大，无论它的font-size多大。

**标准模式与怪异模式的区别** 
1） 盒模型： IE下标准模式为标准w3c盒模型【content+padding+border+margin】，怪异模式为IE盒模型【content+margin：padding与border包含在content宽高中】 
2） 行内元素的垂直对齐：基于 Gecko 的浏览器【Mozilla Firefox、HotBrowser、Mozilla Suite、Camino】标准模式对齐至基线，怪异模式对齐至底部 
3） 怪异模式中，IE6/7/8都不认识!important声明 
4） 设置行内元素的高宽： 在Standards模式下，给等行内元素设置wdith和height都不会生效，而在quirks模式下，则会生效。 
5） 使用margin:0 auto在standards模式下可以使元素水平居中，但在quirks模式下却会失效。

### 4.5浏览器测试

使用Navigator对象

### 4.6IE条件注释

```html
<!--[if ID]><script src = "excanvas.js"></script><![endif]-->

//只在IE中执行的代码
/*@cc_on
  @if(@_jscript)
	alert("In IE");
  @end
  @*
/
```

## 5.可访问性

Web是发布信息的理想工具，而JavaScript程序可以增强信息的访问。

## 6.安全性

### 6.1JavaScript不能做什么

没有权限来写入和删除客户计算机上的任意文件或列出目录

没有任何通用的网络能力

### 6.2同源策略

是对JavaScript能够操作哪些文档内容的一条安全限制

包括协议，主机，端口号

不严格同源策略

1.设置Document对象的domain值，如果一直就可以互相读取对方属性

2.跨域资源共享

3.跨文档消息，调用Window对象的postMessage()方法

### 6.3脚本化插件和ActiveX控件

### 6.4跨站脚本

就是在url的？输入script元素来导入非法脚本

措施 replace方法去掉尖括号

使用toStaticHTML()移除script标签

### 6.5拒绝服务攻击

Web恶意无限循环占用浏览器，没有通用方法来防止这种笨重攻击

## 7.客户端框架

Jquery,Prototype,Dojo,YUI,Closure,GWT