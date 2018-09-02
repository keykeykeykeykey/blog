# CSS经典三栏布局

三栏布局，顾名思义就是两边固定，中间自适应。三栏布局在开发十分常见 

## 1.float

p标签虽然是一个块标签 但是不可以存放别的块标签 这样是放不进去的 你可以放一下试试 然后F12看看 存放的块标签是在p标签外部的 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>


.left {
    float: left;
    width: 100px;
    height: 200px;
    background-color: red;
}

.right {
    float: right;
    width: 100px;
    height: 200px;
    background-color: yellow;
}

.main {
    background-color: green;
    height: 200px;
    margin-left: 120px;
    margin-right: 120px;
}

.container {
    border: 1px solid black;
}
</style>

<body>
    <div class="container">
        <div class="left"></div>
        <div class="right"></div>
        <div class="main"></div>
    </div>
</body>

</html>
```

优势：简单

劣势：中间部分最后加载，内容较多时影响体验

![1534320114331](C:\Users\李科\AppData\Local\Temp\1534320114331.png)

## 2.BFC规则

BFC(块格式化上下文)规则规定：BFC不会和浮动元素重叠。所以如果将main元素设定为BFC元素即可： 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>
.left {
    float: left;
    width: 100px;
    height: 200px;
    background-color: red;
}

.right {
    float: right;
    width: 100px;
    height: 200px;
    background-color: yellow;
}

.main {
    background-color: green;
    height: 200px;
    overflow: hidden;
}

.container {
    border: 1px solid black;
}
</style>

<body>
    <div class="container">
        <div class="left"></div>
        <div class="right"></div>
        <div class="main"></div>
    </div>
</body>

</html>
```

![1534320145855](C:\Users\李科\AppData\Local\Temp\1534320145855.png)

## 3.圣杯布局

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>
.left {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100%;
    position: relative;
    left: -100px;
    background-color: red;
}

.right {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100px;
    position: relative;
    right: -100px;
    background-color: yellow;
}

.main {
    float: left;
    width: 100%;
    height: 200px;
    background-color: blue;
}

.container {
    padding-left: 100px;
    padding-right: 100px;
}
</style>

<body>
    <div class="container">
        <div class="main">点击看撒娇肯德基啊块数据啊打开</div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

![1534320818040](C:\Users\李科\AppData\Local\Temp\1534320818040.png)

圣杯布局的核心是左、中、右三栏都通过float进行浮动，然后通过负值margin进行调整。 

第一步，先来看下基本布局

```css
    .left {
        float: left;
        width: 100px;
        height: 200px;
        background-color: red;
    }
 
    .right {
        float: left;
        width: 100px;
        height: 200px;
        background-color: yellow;
    }
 
    .main {
        float: left;
        width: 100%;
        height: 200px;
        background-color: blue;
    }
```



左、右两栏被挤到第二行。这是因为main的宽度为100%。接下来我们通过调整左、右两栏的margin来将左、中、右放在一行中： 

第二步，将left的margin-left设置为-100%，此时左栏会移动到第一行的首部。然后再将right的margin-left设置为其宽度的负值：-100px，则右栏也会移动到和左、中栏一行中： 

```css
.left {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100%;
    background-color: red;
}
 
.right {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100px;
    background-color: yellow;
}
```

添加文字，发现文字覆盖

第三步，给container一个padding，该padding应该正好等于左、右栏的宽度： 

```css
.container {
    padding-left: 100px;
    padding-right: 100px;
}
```

此时看到的结果是左、中、右三栏都整体收缩了，但文字依然被压住了。 

第四步，给左、右两栏加上相对布局，然后再通过设置left和right值向外移动： 

```css
.left {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100%;
    position: relative;
    left: -100px;
    background-color: red;
}
 
.right {
    float: left;
    width: 100px;
    height: 200px;
    margin-left: -100px;
    position: relative;
    right: -100px;
    background-color: yellow;
}
```

## 4.双飞翼布局

双飞翼布局的前两步和圣杯布局一样，只是处理中间栏部分内容被遮挡问题的解决方案有所不同： 

既然main部分的内容会被遮挡，那么就在main内部再加一个content，通过设置其margin来避开遮挡，问题也就可以解决了： 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>
.main {
    float: left;
    width: 100%;
}

.content {
    height: 200px;
    margin-left: 110px;
    margin-right: 220px;
    background-color: green;
}

.main::after {
    display: block;
    content: '';
    font-size: 0;
    height: 0;
    clear: both;
    zoom: 1;
}

.left {
    float: left;
    height: 200px;
    width: 100px;
    margin-left: -100%;
    background-color: red;
}

.right {
    width: 200px;
    height: 200px;
    float: left;
    margin-left: -200px;
    background-color: blue;
}
</style>

<body>
    <div class="container">
        <div class="main">
            <div class="content"></div>
        </div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

## 5.flex布局

flex布局是趋势，利用flex实现三栏布局也很简单，不过需要注意浏览器兼容性： 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>
.container {
    display: flex;
    flex-direction: row;
}

.main {
    height: 200px;
    background-color: red;
    flex-grow: 1;
}

.left {
    height: 200px;
    order: -1;
    margin-right: 20px;
    background-color: yellow;
    flex: 0 1 200px;
}

.right {
    height: 200px;
    margin-left: 20px;
    background-color: green;
    flex: 0 1 200px;
}
</style>

<body>
    <div class="container">
        <div class="main">fdaskfjkdasjkfjdklsajfkdjas</div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

1. main要首先加载就必须写在第一位，但因为left需要显示在最左侧，所以需要设置left的order为-1
2. flex属性的完整写法是：flex: flex-grow flex-shrink flex-basis 。这也是flex实现三栏布局的核心，main设置flex-grow为1，说明多余空间全部给main，而空间不够时，仅缩小left right部分，同时因为指定了left right部分的flex-basis，所以指定了两者的宽度，保证其显示效果

## 6.绝对定位

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<style>
.container {}

.main {
    position: absolute;
    left: 200px;
    right: 200px;
    height: 300px;
    background-color: yellow;
}

.left {
    position: absolute;
    left: 0px;
    width: 200px;
    height: 300px;
    background-color: red;
}

.right {
    position: absolute;
    right: 0px;
    width: 200px;
    background-color: green;
    height: 300px;
}
</style>

<body>
    <div class="container">
        <div class="main">fdaskfjkdasjkfjdklsajfkdjas</div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

