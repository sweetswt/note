## 一、垃圾回收机制、闭包

### 1、垃圾回收机制

-   JavaScript自动回收不再使用的变量，释放其所占用的内存，开发者不需要手动做垃圾回收的处理。 
- 垃圾回收机制只会自动回收局部变量， 全局变量不会被回收（全局变量只有关闭浏览器后会进行回收）， **所以当我们定义了一个全局的对象时，使用完毕之后，最好给它重新赋值为null，以便释放它所占的内存（这个变量没有被回收，只是改变了指向，减少内存占用）。** 
-  目前浏览器基本都使用*标记清除(介绍……)*的方式，还有另外一种不常见的*引用计数(介绍……)*方式。 

###### 标记清楚：

当某个变量不再被使用时，该变量就会被回收。

###### 引用计数：

- 极少数浏览器（如 IE ）上针对引用类型的回收机制

当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1，如果这个变量的值又被赋值给了另外一个变量（即两个变量的地址都指向同一个引用类型），则该值的引用次数+1。相反，如果包含这个引用类型的变量又取了另外一个值，则引用次数 - 1。当这个引用类型的引用次数变为 0 时，则说明无法再访问这个变量。当垃圾收集器下次再运行时，它就会释放引用次数为 0 的值所占用的内存。

### 2、闭包

###### 概念

一个函数中嵌套另一个函数，内部函数使用了外部函数的参数或变量，就构成了闭包（这个内部函数就叫做闭包）。被内部函数使用的外部函数的参数或变量 ，不会被js的垃圾回收机制所回收。

例：

```js
(function () {
    var a = 1;
    document.onclick = function () {
        var b = 10;
        console.info(++a);  //  点击页面后依次输出 2 3 4 5 ...    
        console.info(++b);  //  点击页面始终都输出 10
    }
})()
```

以上代码，局部变量在被使用后会被回收，但由于内部函数使用外部函数的了变量a，所以变量a不会被回收。而 变量b 是一个局部变量且没有构成闭包，所以内部函数中 变量b 使用完后会被回收。

###### 经典例子

点击p打印相应的序号：

```html
<body>
    <p>000</p>
    <p>111</p>
    <p>222</p>
    <p>333</p>

    <script>
        var oP = document.querySelectorAll('p')
        for (var i = 0; i < oP.length; i++) {
            a(i);
        }
        function a(index) {
            oP[index].onclick = function () {
                console.info(index);
            }
        }
    </script>
</body>
```

使用 ES6 - let 替换 var ，则不需要进行函数嵌套，如下：

```html
<body>
    <p>000</p>
    <p>111</p>
    <p>222</p>
    <p>333</p>

    <script>
        var oP = document.querySelectorAll('p')
        for (let i = 0; i < oP.length; i++) {
            oP[i].onclick = function () {
                console.info(i);
            }
        }
    </script>
</body>
```

原理：let 是块级作用域，即每次for循环的 大括号内 都是一个独立的作用域，而内部函数则是一个子作用域，也相当于作用域内嵌套子作用域（也类似形成了闭包），所以每次循环的 i 不会被回收。 

###### return 作用域

函数的作用域定义时在哪，就始终不变，和函数执行的位置无关。

```js
function a(){
    var x = 1;
    return function(){
        alert(x)
    }
}

var b = a()
b()    // 1
```

###### 手动回收 闭包中使用的变量

- 将接收内部函数的变量设为null

  ```js
  function a(){
      var x = 1
      return function(){
          x++    // x始终不会被回收
      }
  }
  
  var fn = a()
  fn = null   // 手动回收
  ```

  

## 二、this指向

### 1、介绍

- 函数在定义时，是不知道 this 的指向的
- 只有在函数执行时，才有 this 这个概念
- **谁调用这个函数，this就指向谁，如果没有被调用，在全局自执行，则this指向window**

### 2、改变this指向

#### call

语法：

```js
fn.call( 规定this指向 , 参数1 , 参数2 , ... )
```

可通过 call 来改变this指向，call 会改变 this 指向并**立即执行一次函数**

例：

```js
function fn( n , m ){
    console.info(this)
}

fn()    // window
fn.call(document,2,4)  // document
```

#### apply

语法：

```js
fn.apply( 规定this指向 , [ 参数1 , 参数2 , ...] )
```

参数传递为数组形式，其他使用方式与 call 相同，也会**立即调用一次函数**

#### bind 

兼容性 ： IE8+

语法：

```js
fn.bind( 规定this指向 , 参数1 , 参数2 , ... )
```

改变this的指向，返回值为一个改变过this指向的原函数，**不会立即调用一次函数**

例：

```js
function fn( n , m ){
    console.info(this)
}

var x = fn.bind(document,2,4)  
console.info(x)    // function fn(n,m){ console.info(this) }
x()  // document
x()  // document
```

#### 注意

```js
错误写法 ： fn().call(obj)   // 函数不能加(),会执行
正确写法 ： fn.call(obj)
```

#### 手写bind函数（了解）

```js
Function.prototype.mybind = function () {
    let self = this
    let that = arguments[0]
    let arg = Array.from(arguments).slice(1, arguments.length)
    return function () {
        self.call(that, ...arg, ...arguments)
    }
}

function a(n, m, z ) {
    console.info(this)  // document
    console.info(n);    // 1
    console.info(m);    // 2
    console.info(z);    // 55
}

// 柯里化 
let x = a.mybind(document, 1, 2)
x(55)
```

###### 函数柯里化

 柯里化就是利用了函数闭包的特性 ,将接受多个参数的函数,将变为接受单一参数的函数,在函数内部return一个函数,内部函数用来接受剩余参数,并返回结果   

作用：固定部分参数，在调用时传入剩余参数

#### 事件触发函数改变this指向

```js
var obj = { name:"铁柱" }

function fn(){
    console.info(this)
}

// 方式1 - bind
document.onclick = fn.bind(obj)

// 方式2 - call （或apply）
document.onclick = function(){
    fn.call(obj)
}
```





## 三、延长作用域链

### 1、with

**了解即可，禁止使用**    ->  with 在严格模式下会报错

```js
var obj = { a:"10" }

function fn(){
    var a = 100;
    var b = 200;
    with( obj ){
        console.info(a)   // 10
        console.info(b)   // 200
    }
}
```

以上代码，with 延长了作用域链，with中的代码块，会先从 obj 中去查找，找不到再从自身作用域一级一级向上查找，直至 window

### 2、try...catch...

我们知道，js是顺序执行的，遇到错误就会停止执行，那么如何避免代码停止执行，这时就需要使用 try...catch...。

###### 执行方式

**先执行 try 中的代码，如果执行过程中没有报错的代码，则不执行 catch 中的代码。如果在执行 try 中代码时遇到了报错的代码，不会终止代码的执行，跳到 catch 中开始执行代码**，

例：

```js
try{
    console.info(1)
    conasdf.info(2)    // 报错代码，停止执行try中之后的代码，开始执行catch中的代码
    console.info(3)
}catch(e){
    console.info(4)
}

// 执行结果为 1 4
```

注 ：catch 的参数 e 为错误日志。（throw 手动抛出错误）





## 四、严格模式

在全局代码最前面或者函数最前面可以加上`“use strict”`字符，以表明当前作用域使用严格模式。

严格模式与非严格模式的不同：

1. 禁用with
2. 不允许给未声明的变量赋值
3. arguments和形参不冲突
4. 禁止删除声明的变量或参数
5. 八进制必须 0o 开头
6. eval作用域独立（防止获取到其他作用域的数据）
7. 禁止给基础数据类型添加属性 （非严格模式下给基础数据类型添加属性不会报错，但属性不会被报存下来）
8. 函数在全局作用域执行，this不再指向window而是undefined
9. 禁止使用arguments.callee（指向原函数）
10. 禁止对只读/只写属性进行读/写操作（如 getComputedStyle 获取的样式属性为只读，在严格模式下添加属性会报错）

使用严格模式的目的：

> - 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
> - 消除代码运行的一些不安全之处，保证代码运行的安全；（如eval）
> - 提高编译器效率，增加运行速度；（如浏览器不需要再对一些未定义的变量进行定义）
> - 为未来新版本的Javascript做好铺垫。

在写代码时，按照严格模式下的要求进行书写，防止 ESMAScript 版本更新后，非严格模式的代码被修正。