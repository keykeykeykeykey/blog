this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定，this最终指向调用它的对象。

1.函数调用模式

当一个函数并非一个对象的属性时，那么它就是被当做函数来调用的。在此种模式下，this被绑定为全局对象，在浏览器环境下就是window对象

```js
function a(){
    var a = 'hello';
    console.log(this.a);		//undefined
    console.log(this);			//window
}
a();
```



2.方法调用模式

当函数被保存为一个对象的属性时，它就可称为这个对象的方法。当一个方法被调用时，this被绑定到这个对象上。如果调用表达式包含一个提取属性的动作（. 或 []），那么它被称为方法调用

```js
var o = {
    name:'hello',
    sayName:function(){
        console.log(this.name);//hello
    }
}
o.sayName();
```



这里的this指向的对象是o，因为调用这个sayName()函数是通过o.sayName()执行的。

```js
var o = {
    name:'hello',
    b:{
        name:'world',
        fn:function(){
            console.log(this.name);//world
            console.log(this)//b
        }
    }
}
o.b.fn();
```



因为是o.b调用的这个函数，所以指向b这个对象

```js
var o = {
    name:'hello',
    b:{
        fn:function(){
            console.log(this.name);//undefined
            console.log(this)//b
        }
    }
}
o.b.fn();
```



同理，因为是o.b调用的这个函数，所以指向b这个对象

```js
var name = "我是最外面的name"
var o = {
    name:'hello',
    b:{
        name:'world',
        fn:function(){
            console.log(this.name);//我是最外面的name
            console.log(this)//window
        }
    }
}
var t = o.b.fn;
t();
```



t是全局变量，在全局环境下执行，this指向window

3.构造函数调用模式

如果在一个函数前面加上new关键字来调用，那么就会创建一个连接到该函数的prototype成员的新对象，同时，this会被绑定到这个新对象上。这种情况下，这个函数就可以成为此对象的构造函数。

```js
function Fn(){
    console.log(this);//Fn{}
}
var a = new Fn();
```

在构造函数，new出一个对象时，this指向这个构造函数，new关键字会改变this的指向

```js
function Fn(){
    this.name = 'hello';
}
var a = new Fn();
console.log(f.name);//hello
```



当用new关键字，返回的是一个对象，this指向的就是那个返回的对象；

如果返回的不是对象，this还是指向函数的实例，虽然null属于对象，但是返回null依然指向函数实例

```js
function fn(){
    this.name = 'hello';
    return {};
}
var f = new Fn();
console.log(f.name);//undefined
```

```js
function fn(){
    this.name = 'hello';
    return function(){};
}
var f = new Fn();
console.log(f.name);//undefined
```

```js
function fn(){
    this.name = 'hello';
    return 1;
}
var f = new Fn();
console.log(f.name);//hello
```

```js
function fn(){
    this.name = 'hello';
    return undefined;
}
var f = new Fn();
console.log(f.name);//hello
```

```js
function fn(){
    this.name = 'hello';
    return null;
}
var f = new Fn();
console.log(f.name);//hello
```

```js
function fn(){
    this.name = 'hello';
    return [];
}
var f = new Fn();
console.log(f.name);//undefined
```

4.apply和call调用模式

JS中，函数也是对象，所有函数对象都有两个方法：apply和call，这两个方法可以让我们构建一个参数数组传递给调用函数，也允许我们改变this的值

```js
var name = 'window';
var o ={
    name:'obj'
};
function sayName(){
    console.log(this.name);
}
sayName();//window
sayName.apply(o);//obj
sayName.call(o);//obj
sayName.apply();//window
sayName.call();//window
```



在全局范围内，this指向全局对象（浏览器下指window对象）

对象函数调用时，this指向当前对象

全局函数调用时，应该是指向调用全局函数的对象。

使用new关键字实例化对象时，this指向新创建的对象

当用apply和call上下文调用的时候指向传入的第一个参数



例子

```js
var name = 'window';
function showName(){
    console.log(this.name);
}
var person1 = {
    name:"hello",
    sayName:showName
}
var person2 = {
    name:"world",
    sayName:function(){
        var fun = person1.sayName;
        fun();
    }
}
person1.sayName();//hello
person2.sayName();//window
```

在执行person1.sayName()时，时方法调用模式，this代表person1这个对象

在执行person2.sayName()时，时方法调用，但是sayName，并没有执行，而是将sayName()这个函数赋值给fun这个变量，fun是函数调用模式，this指向window，故输出全局的name

```js
var A ={n:4399};
var B = function(){
    this.n = 9999;
}
var C = function(){
    var n = 8888;
}
B.prototype = A;
C.prototype = A;
var b = new B();
var c = new C();
A.n++;
console.log(b.n);//9999
console.log(c.n);//4400
```

执行console.log(b.n)时，b对象有自己的属性n值

执行console.log(c.n)时，c对象没有自己的属性n值，会向上查找，找的A对象中的属性n值

```js
var color = "green";
var test = {
    color:'blue',
    getColor:function(){
        var color = 'red';
        console.log(this.color);
    }
};
var getColor = test.getColor;
getColor();//green
test.getColor();//blue
```

var getColor=test.getColor相当于把方法函数赋值给全局变量，

故getColor()中的this指向window

test.getColor()是方法调用