# JS的子集和扩展

子集的定义大部分都是出于安全考虑，只有使用这门语言的一个安全的子集编写脚本，才能让代码执行得更安全，更稳定。

## 1.JS的子集

大多数语言都会定义子集，用以更安全地执行不可信的第三方代码

- 精华

  目标是简化这门语言，规避掉语言中的怪癖、缺陷部分，最终使编程更加轻松、程序更健壮。

- 子集的安全性

  目的是能在一个容器或“沙箱”中更安全地运行不可信的第三方JavaScript代码

## 2.常量和局部变量

JavaScript1.5版本及后续版本使用const关键字来定义常量。常量可以看做不可重复赋值的变量(对常量重新赋值会失败但不会报错)，对常量的重复声明会报错。和var定义行为非常相似，由于JS没有块级作用域，因此常量会被提前至函数定义的顶部。

```js
const pi = 3.14;		//定义一个常量并赋值
pi = 4;					//任何对这个常量的重新赋值都会被忽略
const pi = 4;			//重新声明常量会报错
var pi = 4;				//这里也会报错
```

为了解决JS缺少块级作用去，JavaScript1.7版本新增关键字let，需要手动加入版本号

```html
<script type="application/javascript;version=1.8"></script>
```

关键字let的4中使用方式

- 可以作为变量声明，和var一样
- 在for或for/in循环中，作为var的替代方案
- 在语句块中定义一个新变量并显示指定它的作用域
- 定义一个在表达式内部作用域的变量，这个变量只在表达式内可用

let声明的变量只属于拘谨的花括号的语句块

```js
function oddsums(n){
    let total = 0,result = [];		//在函数内都有定义的
    for(let x = 1;x<n;x++){			//x只在循环体内有定义
        let odd = 2*x-1;			//odd只在循环体内有定义
        total += odd;
        result.push(total);
    }
    //这里使用x或odd会导致一个引用报错
    return result;
}
oddsums(6);		//返回[1,4,9,16,25]
```

```js
o = {x:1y:2};
for(let p in o) console.log(p);			//输出x和y
for each(let v in o) console.log(v);	//输出1和2
console.log(p);							//引用错误，p没有定义
```

```js
let x = 1;
for(let x= x+1;x<5;x++)
    console.log(x);			//输出2~4
{							//开始一个新的语句块，创建新的变量作用域
    let x = x+1;			//x没有定义，因此x+1是NaN
    console.log(x);			//输出NaN
}
```

let表达式

```js
let x=1, y=2;
console.log(let (x=x+1,y+x+2) x+y);		//输出5
```

## 3.解构赋值

在结构赋值中，等号右侧是一个数组或对象(一个结构化的值)，指定左侧一个或多个变量的语法和右侧数组和对象直接量的语法保持一致

```js
let [x,y] = [1,2];		//等价于let x=1,y=2;
[x,y] = [x+1,y+1];		//等价于x = x+1,y=y+1;
[x,y] = [y,x];			//交换两个变量的值
console.log([x,y]);		//输出[3,2]
```

解构赋值右侧的数组所包含的元素不必和左侧变量一一对应，左侧多余的变量的赋值为undefined，而右侧多余的值则会忽略。左侧的变量列表可以包含连续的逗号用以跳过右侧对应的值

```js
let [x,y] = [1];		//x=1,y=undefined
[x,y] = [1,2,3];		//x=1,y=2
[,x,,y] = [1,2,3,4];	//x=2,y=4	
```

解构赋值右侧也可以是一个对象

## 4.迭代

### 4.1for/each循环

有E4X(ECMAScript for XML)定义的新的循环语句，遍历对象属性的值

```js
let o = {one:1,two:2,three:3};
for(let p in o) console.log(p);		//输出'one','two','three'
for each(let v in o) console.log(v);	//输出1~3
```

注意：for/each循环不仅仅针对数组本身元素警醒遍历，它也会遍历数组中左右可枚举的属性的值，包括有数组继承来的可枚举方法，因此不推荐for/each循环和数组一起使用

### 4.2迭代器

JavaScript1.7为for/in循环增加了更多功能，。

迭代器是一个对象，这个对象允许对它的值集合进行遍历，并保持任何必要的状态以便下次能够追到当前遍历的”位置“

迭代器必须包含next()方法，每次调用next()都返回几个钟的下一个值

```js
function counter(start){
    let nextValue = Math.round(start);		//表示迭代器的私有状态
    return {next:function(){return nextValue++;}}//返回迭代器对象
}
let a = counter(1000);
let sn1 = a.next(); //1000
let sn2 = a/next(); //1001
```

当迭代器用于有限的集合是，当遍历完所有值并且没有多余的值可迭代时，在调用next()方法会抛出StopIteration.

```js
function rangIter(first,last){
    let nextValue = Math.ceil(first);
    return {
        next:function(){
            if(nextValue > last) throw StopIteration;
            return nextValue++;
        };
    }
}

let r = rangeIter(1,5);
while(true){
    try{
        console.log(r.next());
    }
    catch(e){
        if(e===StopIteration) break;
        else throw e;
    }
}
```

这里的循环使用一个迭代器对象，并且显式吃力Stopiteration犯法，这种方法非常糟糕。我们经常不直接使用迭代器对象，而是使用可迭代的对象。可迭代的对象必须定义一个名叫_iterator_()的方法

```js
//返回一个可迭代的对象，用以表示该方位的数字
function range(min,max){
    return {
        get min(){return min;},
        get max(){return max;},
        includes:function(x){
            return min <= x && x<max;
        },
        toString:function(){
            return "[" + min + "," + max + "]";
        },
        _iterator_:function(){
            let val = Math.ceil(min);
            return {
                next:function(){
                    if(val>max)
                        throw StopIteration;
                    return val++;
                }
            };
        }
    };
}

for(let i in range(1,10)) console.log(i);	//输出1~10的数字
```



引入Iterator()函数(JavaScript1.7定义的全局函数)。目的是如果传入的对象或者数组没有定义_iterator_（）方法，它会返回这个对象的一个可迭代的自定义迭代器。每次调用这个迭代器的next()方法都会返回其中包含两个值的一个数组，第一个数组元素是属性名，第二个是命名属性的值

```js
for(let [k,v] in Iterator({a:1,b:2}))
    console.log(k + "=" +v);		//输出"a=1"和"b=2"
```

Iterator特性:1:只对只有属性进行遍历而忽略继承的属性2：二个参数为true是，返回的地带起只对属性名遍历，而忽略属性值

```js
o = {x:1,y:2};
Object.prototypr.z = 3;
for(p in o) console.log(p);	//x,y,z
for(p in Iterator(o,true)) console.log(p); //x,y
```

### 4.3.生成器

JavaScript1.7中特性，使用关键字yield，用法和return类似，返回函数的值。区别是使用yield的函数"产生"一个可保持函数内部状态的值，这个值时可以恢复的。

```js
function range(min,max){
    for(let i = Math.ceil(min);i<=max;i++) yield i;
}

for(let n in range(3,8)) console.log(n);	//输出数字3~8
```

生成器函数不需要返回

```js
function fibonacci(){
    let x = 0,y=1;
    while(true){
        yield y;
        [x,y] = [y+x+y];
    }
}
f = fibonacci();
//将生成器当做迭代器，输出Fibonacci数列的前10个数
for(let i = 0;i<10;i++) conslole(f.next());
f.close();//释放
```

生成器经常用来处理序列化数据，比如元素列表，多行文本，词法分析器中的单词等

一个生成器管道

```js
function eachline(s){
    let p;
    while((p = s.indexOf('\n')) !=-1){
        yield s.substring(0,p);
        s = s.substring(p+1);
    }
    if(s.length>0) yield s;
}

function map(i,f){
    for(let x in i) yield f(x);
}

function select(i,f){
    for(let x in i){
        if(f(x)) yield x;
    }
}

let text = " #comment \n \n hello \n world\n quit \n unreached \n";
//文本分隔成行
let lines = eachline(text);
//去掉行首和行尾的空格
let trimmed = map(lines,function(line) {return line.trim();});
//忽略空行和注释
let nonblank = select(trimmed,function(line){
    return line.length>0&&line[0] != "#";
});

for(let line in nonblank){
    if(line ==="quit") break;
    console.log(line);
}
```

上层器可以通过next()，send(),throw重启生成器

```js
function counter(initial){
    let nextValue = initial;
    while(true){
        try{
            let increment = yield nextValue;
            if(increment)
                nextValue += increment;
            else nextValue++;
        }
        catch(e){
            if(e==="reset")
                nextValue=initial;
            else throw e
        }
    }
}

let c= counter(10);		//用10来创建生成器
console.log(c.next());	//10
console.log(c.send());	//12
console.log(c.throw("reset")); //10
```

### 4.4.数组推导

语法

[expression for (variable in object) if(condition)]

三个部分

- 一个没有循环体的for/in或for/each玄幻
- 条件表达式，用于过滤值
- expression，可以认为是循环体



例子

```js
let a = [x*x for (x in range(0,100)) if(x%2===0)]
```

### 4.5.生成器表达式

JavaScript1.8中，将数组推导中的方括号替换成圆括号就成了一个生成器表达式。返回一个生成器对象

```js
let lines = eachline(text);
let trimmed = (l.trim() for (l in lines));
let nonblank = (l for ( l in trimmed) if (l.length>0&&l[0]!='#'))
```

## 5.函数简写

```js
let succ = function(x)x+1,yes=functiontrue,no = function(false);
data.sort(function(a,b)b-a);
```

省略花括号和return，更适用于函数传入另一个函数

6.E4M

新增XML对象，typeof运算结果为“xml”

创建XML对象

```js
var pt = <periodictable>
    <element id="1"><name>A</name></element>
    <element id="2"><name>B</name></element>
    <element id="3"><name>C</name></element>
    </periodictable>;
//给表格添加元素
pt.elemrnt += <element id="4"><name>D</name></element>
```

XML直接量语法中使用花括号作为转义字符

```js
pt = <periodictable></periodictable>;
var elements = ["A","B","C"];
for(var n = 0; n < elements.length; n++){
	pt.element += <element id={n+1}><name>{elements[n]}</name></periodictable>;
}
```

将字符串解析为XML

```js
pt.element += new XML('<element id="5"><name>B</name></element>')
```

涉及到片段时使用XMLList()

```js
pt.element += new XMLList('<element id="6"><name>C</name></element>' + 
                         '<element id="7"><name>D</name></element>');
```

访问XML文档内容

```js
var elements = pt.element;			//得到所有<element>标签组成的一个列表
var names = pt.element.name;		//得到所有的<name>标签的一个列表
var n = names[0];					//"A",name的第0个标签内容
```

后代运算符..

```js
var names2 = pt..name;
```

通配符*

```js
var names = pt.element.*;
```

E4X使用@来区分属性和标签名

```js
var number = pt.element[i].@ud;
```

可以使用通配符获取属性名@*

```js
var Nums = pt.element.@*;
```

过滤条件

```js
var light = pt.element.(@id < 3);

var bElementNames = pt.element.(name.charAt(0) == 'B').name;
```

for/each循环遍历对象属性值，for/in遍历对象的属性名

```js
for each (var e in pt.element){
    console.log(e.name);
}

foreach(var n in pt.element.@*) console.log(n);
```

修改或添加新标签或属性

```js
<element id="1" symbol="H">
    <name>H</name>
	<weight>1.00794</weight>
</element>

pt.element[0].@symbol = "H";
pt.element[0].weight = 1.00794;
```

可以通过标准的delete运算符删除属性和标签

```js
delete pt.element[0].@symbol;
delete pt..weight;
```

调用XML对象的方法

```js
pt.insertChildBefore(pt.element[1],<element id="1"><name>De</name></element>);
```

E4X完全支持命名空间，它使用XML命名空间提供了语法支持和API支持

```js
d = <html>
    <body>
    This is a small red square:
	<svg xmlns="http://www/w3/org/2000/svg" width="10" height="10">
        <rect x="0" y="0" width="10" height="10" fill="red"/>
    </svg>
	</body>
	</html>

var tagname = d.body.name();
var bodyns = tagname.uri;
var localname = tagname.localName;

var svg = new Namespace('http://www.w3.org/2000/svg');
var color = d..svg::rect.@fill  //"red"
```

