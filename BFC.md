# BFC(块级格式化上下文)

它是页面中的一块渲染区域，有一套渲染规则，决定了其子元素如何布局，以及和其他元素之间的关系和作用。 

- BFC定义

  BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。 

- BFC布局规则

  1. 内部的Box会在垂直方向，一个接一个地放置。
  2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
  3. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
  4. BFC的区域不会与float box重叠。
  5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
  6. 计算BFC的高度时，浮动元素也参与计算

- 触发

满足下列条件之一就可触发BFC

　　【1】根元素，即HTML元素 
　　【2】float的值不为none 
　　【3】overflow的值不为visible 
　　【4】display的值为inline-block、table-cell、table-caption 
　　【5】position的值为absolute或fixed

【1】阻止垂直外边距（margin-top、margin-bottom）折叠 

属于同一个BFC的两个相邻块级子元素（元素都要在文档流中）的上下margin会发生重叠—— 分为两个BFC就可以消除这种margin 重叠 

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<style>
    .dv {
        width: 400px;
        height: 400px;
        border: 1px solid slateblue;
    }

    .dv2 {
        width: 200px;
        height: 100px;
        background: salmon;
        margin-bottom: 30px;
    }


    .dv3 {
        width: 200px;
        height: 100px;
        margin-top: 60px;
        background: seagreen;
    }
    </style>

    <div class="dv">
        <div class="dv2">
        </div>       
        <div class="dv3">             
        </div>
        <!--dv2和dv3的margin会重叠-->
  	</div>
	</body>
</html>

```

触发其中一个div的BFC，就可以阻止两个div的重叠

```css
 .dv4{
        /* overflow: hidden; */
        /* display: inline-block; */
        position: absolute;  
    }
```

overflow:hidden这个可能不行

【2】包含浮动元素  

可以包含它内部的所有元素，包括浮动元素——因此一清除内部浮动 

元素内容比较多的时候，内容会环绕着浮动的元素 

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<style>
 .second{
        width: 400px;
        padding: 10px;
        border: 1px solid slateblue;

    }
    .float{
        float: left;
        width: 200px;
        height: 50px;
        background: salmon;
    }
    .s1{
		
        background: slategray;

    }

</style>  
<div class="second">
        <div class="float">
        </div>
        <div class="s1"> 
            IE6、IE7中， // function foo(){ // console.log(a); // } // function bar(){ // var a =3 ;
            IE6、IE7中， // function foo(){ // console.log(a); // } // function bar(){ // var a =3 ;
        </div>
    </div>   
	</body>
</html>

```

![1532938507563](C:\Users\李科\AppData\Local\Temp\1532938507563.png)

`清除环绕`，为其本身创建一个BFC 

```css
  .s1{
        overflow: hidden;
        background: slategray;
    }
```

![1532938595959](C:\Users\李科\AppData\Local\Temp\1532938595959.png)

元素内容比较少的时候，会出现高度坍塌， 足够的文本去一个浮动的元素 ， 浮动元素脱离文档流，父元素（class=second）高度就会随着文本的减少而降低 ，父元素未被浮动元素撑开，父元素的高度坍塌。  

![1532938721269](C:\Users\李科\AppData\Local\Temp\1532938721269.png)

`改变坍塌`：为父元素创建一个BFC 

```css
.second{
        width: 400px;
        padding: 10px;
        border: 1px solid slateblue;
        overflow: hidden;
       //或者 overflow: auto;
    }
```

![1532938814191](C:\Users\李科\AppData\Local\Temp\1532938814191.png)

