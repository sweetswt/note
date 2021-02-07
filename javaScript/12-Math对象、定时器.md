## Math 

（math 是 javascript 中内置的对象）

- Math.PI    数学中的 π （圆周率）
- Math.random( )  随机生成 0 到 1 之间的随机数 （包括0，不包括1）
- Math.abs( )   取绝对值
- Math.ceil( ) 向上取整（天花板）   /    math.floor( ) 向下取整（地板）
- Math.round( )  四舍五入后取整  （负数为5舍6入）
- Math.max( num1 , num2 , ... )  返回最大的值
- Math.min( num1, num2 , ... )  返回最小的值 
- Math.pow( x , y )   幂运算  （ES7 -  x**y  ）
- Math.sqrt(  )  开方  





## 定时器

- setTimeout( ) 
- setInterval( ) 

#### setTimeout  -  延时执行

例：

```js
// 写法1
setTimeout( function(){
    alert(1)
} , 1000 )

// 写法2
function a(){
    alert(1)
}
setTimeout( a , 1000 )

// 写法3 - 了解即可，不推荐此写法
function b(){  
    alert(1)
}
setTimeout( "b()" , 1000 )    //  写法3的 函数b 必须为一个全局变量
```

**写法3 原理**：如果参数1是一个字符串，那么在延迟时间到之后，会将这段字符串放在 eval( ) 中进行执行，由于 eval( ) 会开辟一片新的作用域，所以 函数b 需要时全局变量才能被访问到。

**定时器函数参数传递**：定时器 第三个参数开始，即给函数传递的参数。（适用于写法1和写法2）

```js
function a( n , m ){
    alert( n + m )
}
setTimeout( a , 1000 , 15 , 20 )
// 1s 后弹窗 35
```

#### setInterval -  循环执行

```js
setInterval( function(){
    alert(1)
} , 1000 )
// 延时1s后执行，之后每隔1s便执行一次，不会自动终止。
```

注意：setInterval 的执行间隔有下限，根据浏览器性能的不同，下限也不同，通常为 1000/60 毫秒，所以可以用来做动画

```js
var oBox = document.getElementById("box")
var n = 0
setInterval( function(){
    n+=2
    oBox.style.left = n + "px";
} , 1000/60 )
```

#### 清除定时器

clearTimeout / clearInterval

```js
var timer = setInterval( function(){
    console.info(1)
} , 1000 )
document.onclick = function(){
    clearInterval(timer)    // 清除定时器
}
```



##  requestAnimationFrame() 

requestAnimationFrame() 浏览器专门为动画提供的API 浏览器会自动优化方法的调用 页面不是激活的状态下 动画暂停 有效节省CPU开销 用法与setTimeout相似 只是不需要设置时间间隔

- cancelAnimationFrame()   -> 清除动画

```js
// 复合动画帧的计时器,使得动画更流畅,也只是执行一次
let timer = 0
let a = 0
function fun(){
    a++
    console.log(a)
    timer = requestAnimationFrame(fun)
}
fun()
document.onclick=()=>{
    console.log("定时器停止了")
    cancelAnimationFrame(timer)
}
```



