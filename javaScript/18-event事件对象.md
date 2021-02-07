# event

event事件对象是伴随着事件触发而产生的一个内置对象，它存储了关于该事件的各种信息，比如鼠标位置、按键信息等等，同时也可以它也提供了很多控制事件传递的方法。

- event 存储着与该次事件相关的一些信息 

## 1、兼容性

- 现代浏览器使用事件函数的第一个参数
- IE8以下使用全局event

兼容写法：

```js
ele.onclick = ev => {
    ev = ev || window.event;
};
```

## 2、鼠标位置

- client

  `clientX clientY `鼠标到可视区的距离。

- page

  `pageX pageY` 鼠标到文档的距离。

- offset

  `offsetX offsetY` 鼠标到触发事件的元素的距离。

- screen

  `screenX screenY` 鼠标到用户屏幕的距离。

```js
ele.onclick = ev => {
    ev = ev || window.event
    console.info(ev.clientX)
}
```

## 3、阻止冒泡和阻止默认事件

#### 事件冒泡

事件默认是冒泡的，子元素触发事件时，会沿着DOM结构一层一层向上传递，这个过程称为事件冒泡。

注意：子元素被定位到了其他位置（视觉上脱离了父元素），也会见事件一层一层向上传递

例：以下代码中，.root 加了定位，视觉上脱离了.box，但在点击时，依旧会将点击事件传递给.box

```html
<style>
    .box {
        width: 200px;
        height: 200px;
        background-color: indianred;
    }

    .root {
        height: 100px;
        width: 100px;
        background-color: #ccc;
        position: absolute;
        top: 300px;
    }
</style>

<body>
    <div class="box">
        <div class="root">

        </div>
    </div>

    <script>
        document.querySelector('.box').onclick = () => {
            console.info('box被点击了');
        }
        document.querySelector('.root').onclick = () => {
            console.info('root被点击了');
        }
    </script>
</body>
```

#### 阻止冒泡

阻止冒泡的写法

`e.cancelBubble = true`     支持 IE8 -

`e.stopPropagation()`   支持现代浏览器

兼容写法：

```js
if(ev.stopPropagation){
    ev.stopPropagation()
}else{ 
    ev.cancelBubble = true 
}
```

#### 默认事件

浏览器中很多操作都有默认的行为，比如右键打开菜单，我们称之为默认事件。

```js
// 鼠标右击时会弹出浏览器菜单栏，称为浏览器默认事件
document.oncontextmenu = ev => console.info(1)
```

#### 阻止默认事件

阻止冒泡的写法

`e.returnValue = false `  支持IE8 - 

`e.preventDefault()`   支持现代浏览器

兼容写法：

```JS
if(ev.preventDefaul){      
    ev.preventDefault() 
} else{ 
    ev.returnValue = false 
}
```

## 4、addEventListener

DOM的API也做过很多次的调整升级，在初始DOM和第二次升级DOM的时候，对绑定事件的方式做出了更新，初始DOM，也就是DOM 0级时规定事件的绑定方式为 on+事件名 的方式，也就是我们前面学习过的方式， DOM 2级时添加了一种新的绑定事件的方式：`addEventListener` ，低版本IE浏览器为：`attachEvent`。他们有对应的解除事件绑定的方式：`removeEventListener` 和 `detachEvent`。 

##### addEventListener 作用

- 可以给同一个DOM元素注册多个同类型事件   (使用DOM 0级写法，后面定义的会覆盖前面的)

```js
document.addEventListener('click', ev => {
    console.info(1);
})
document.addEventListener('click', ev => {
    console.info(2);
})

// 在页面内点击后 1 和 2 都会被打印
```

-   只需要给父元素添加事件 , 即可控制子元素 , 减少事件的添加,优化性能   

##### removeEventListener 

**移除事件时，需要第二个参数（函数）的指针相同**

```js
function a(){
    console.info(1)
}
function b(){
    console.info(2)
}

// 事件绑定
document.addEventListener('click', a)
document.addEventListener('click', b)

// 移除事件绑定 - 移除事件的函数地址必须相同（所以事件绑定时不建议将函数定义在addEventListener的参数内部）
document.removeEventListener('click',a)
```

##### 事件捕获

事件默认是冒泡的，可通过 addEventListener 的第三个参数这只为 true 将事件设置为捕获，同时，添加了捕获的事件在使用 removeEventListener 移除时也需要写第三个参数 true

例：以下代码中，先打印 root 再打印 box ，因为 box 和 root 使用了事件捕获

```html
<body>
    <div class="root">
        <div class="box">

        </div>
    </div>

    <script>
        let oBox = document.querySelector('.box')
        let oRoot = document.querySelector('.root')

        function box() {
            console.info('box被点击了');
        }
        function root() {
            console.info('root被点击了');
        }

        oBox.addEventListener('click', box, true)
        oRoot.addEventListener('click', root, true)
        
        // 移除时也需要写第三个参数
        oBox.removeEventListener('click', box, true)
        oRoot.removeEventListener('click', root, true)
    </script>
</body>
```

## 5、事件委托

作用：通过给父级元素添加事件，让所有的子元素拥有事件，解决了重复给子元素添加事件的情况

- target 兼容写法：

  ```js
  target = ev.target || ev.srcElement
  ```

例：要求点击 li 时，打印当前 li 的内容

```html
<body>
    <ul>
        <li>0000</li>
        <li>1111</li>
        <li>2222</li>
    </ul>
    
	<!-- 普通写法 -->
    <script>
        let oUl = document.querySelector('ul')
        let oLi = document.querySelectorAll('li')
        oLi.forEach(item => item.onclick = () => console.info(item.innerHTML))

        // 添加一个新的li - 需要给新的 li 新添加事件，才能打印 3333
        let newLi = document.createElement('li')
        newLi.innerHTML = "3333"
        newLi.onclick = () => console.info(newLi.innerHTML)
        oUl.appendChild(newLi)
    </script>
    
    <!-- 事件委托写法 -->
    <script>
        let oUl = document.querySelector('ul')
        oUl.onclick = ev => {
            ev = ev || window
            target = ev.target || ev.srcElement
            if (target.nodeName === "LI") {
                console.info(target.innerHTML);
            }
        }

        // 添加一个新的li - 不需要新添加事件，点击后就会在控制台打印 3333
        let newLi = document.createElement('li')
        newLi.innerHTML = "3333"
        oUl.appendChild(newLi)
    </script>
    
</body>
```



## 6、ready事件（了解）

`window.onload`事件需要等到所有元素彻底加载完之后再触发，比如css、图片等，假设网络条件不好，或者页面中各种资源较多，这时候会导致后续的js代码很久很久之后才生效，影响用户体验。我们可以使用**DOMContentLoaded  事件**(jQuery-ready事件原理)让js代码在DOM结构加载完后执行，而不必等所有资源加载完成：

-   在任何情况下，DOMContentLoaded 的触发不需要等待图片等其他资源加载完成 -> 所以会先于onload触发

```js
window.onready = function (callback) {
    ///兼容FF,Google
    if (document.addEventListener) {
        document.addEventListener('DOMContentLoaded', function () {
            //console.log(document.readyState);
            document.removeEventListener('DOMContentLoaded',arguments.callee, false);
            callback();
        }, false)
    }
    //兼容IE
    else if (document.attachEvent) {
        document.attachEvent('onreadystatechange', function () {
            //console.log(document.readyState);
            if (document.readyState == "interactive") {
                document.detachEvent("onreadystatechange", arguments.callee);
                callback();
            }
        });
    }
};

//测试   
window.onload = function(){ alert('onload') }
window.onready( function(){ alert('ok') } )https://gitrty.github.io/js-work/30-ES6%E7%9B%92%E5%AD%90%E6%8B%96%E6%8B%BD.html)
```
