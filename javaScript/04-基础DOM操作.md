### 1、什么是DOM?

DOM（文档对象模型 document object model）是针对HTML 文档的一个API
DOM 描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面元素(元素的文字也是DOM的一个节点)

### 2、获取元素的方式

#### 通过id获取
```
document.getElmentById( )   
```

#### 通过class获取
	document.getElementsByClassName( ) 
*获取到的为一个类数组 HTMLCollection，不兼容IE8及以下*

#### 通过标签名获取
	document.getElementsByTagName( )
*获取到的为一个类数组 HTMLCollection*

#### 通过name获取
```js
document.getElementsByName( )
```
*获取到的为一个类数组 NodeList*

#### 通过选择器获取
```js
document.querySelector( )   //获取一个
document.querySelectorAll( )	// 获取所有的（NodeList）
```

*不支持 IE7 及以下*

```js
document.querySelectorAll("[class^='text-']") // 获取所有class 以 text开头的元素
```

#### 其他

```js
document.documentElement   // 获取html标签
document.body   // 获取body标签
document.head   // 获取head标签
document.title  // 获取title标签
...
```

#### 注意点

`getElementsByClassName` 和`getElementsByTagName`和 `document.getElementsByName` 获取的元素为动态获取，而`querySelectorAll`为静态获取。

动态获取：当DOM元素有改变时，获取到的类数组也会随之变化。

静态获取：当获取到后，DOM的改变不会改变已获取到的类数组。

例：

```js
let divs = document.getElementsByTagName('div')
let divs2 = document.querySelectorAll('div')
document.body.onclick = () => {
    document.body.appendChild(document.createElement('div'))
    console.info(divs);   //  添加新的div后，类数组 divs 的数量也增加
    console.info(divs2);  //  添加新的div后，类数组 divs2 的数量始终不变
}
```

### 3、操作元素属性

##### 1 - 改变标签合法属性

```js
var oBox = document.getElementById("box")
oBox.href = "#"
oBox.className = "box1"
oBox.style.display = "none" 
```

特殊：class 和 style 属性

class ：使用 className （推荐使用H5中新增classList操作class）

style ：使用 style.xxx

##### 2 - 自定义标签属性的操作（也可操作合法标签属性）

.getAttribute()

.setAttribute()

.removeAttribute()

```js
var oBox = document.getElementById("box")
// 获取
oBox.getAttribute("data-title")   
// 修改、添加
oBox.setAttribute("data-name","mydata")
// 删除
oBox.removeAttribute("data-name")
```

自定义属性不建议添加（团队开发时可能添加相同的自定义属性）

##### 3 - innerHTML/innerText

改变元素的内容

```js
var oBox = document.getElementById("box")
oBox.innerHTML = "ABC"
oBox.innerText = "123"
```

innerHTML 与 innerText 的区别 ：

- innerHTML 可以解析html标签
- innerText 不会解析hmtl标签，原样替换所设置的内容

### 4、事件

所谓的事件就是用户在一个网页上所做出的某些操作动作, 操作动作就是事件，如鼠标点击、鼠标拖拽，键盘输入等

注：所有元素的默认事件都为null （可通过console.dir在控制台打印查看）

|      鼠标事件       |                                                              |
| :-----------------: | :----------------------------------------------------------: |
|       onclick       |                           鼠标单击                           |
|     ondblclick      |                           鼠标双击                           |
|    oncontextmenu    | 鼠标右击 ( 通常需要在触发函数中添加 ev.preventDefault()来阻止浏览器默认行为 ) |
| onmouseenter(常用)  |                   鼠标移入 ( 只触发一次 )                    |
| onmouseleave(常用)  |                           鼠标移出                           |
| onmouseover(不常用) |          鼠标移入( 进入触发,进入 子元素 继续触发 )           |
| onmouseout(不常用)  |           鼠标移出 ( 移出时触发,遇到子元素也触发 )           |
|     onmousedown     |                          鼠标按下时                          |
|      onmouseup      |                          鼠标松开时                          |
|     onmousemove     |                      鼠标在区域内移动时                      |
|       onwheel       |                      滚轮在区域内滚动时                      |

|  键盘事件  |                                                            |
| :--------: | :--------------------------------------------------------: |
| onkeydown  |                         键盘按下时                         |
|  onkeyup   |                         键盘弹起时                         |
| onkeypress | 键盘按下并弹起后触发,但系统按钮无法识别( 箭头键/功能键等 ) |
|  oninput   |         当input框内输入时触发(有兼容问题,但是好用)         |

|   表单元素事件   |                            |
| :--------------: | :------------------------: |
| <input>.onchange | 内容改变后且焦点离开后触发 |
| <input>.onfocus  |       获得焦点时触发       |
|  <input>.onblur  |       失去焦点时触发       |
| <input>.onselect |  当select下拉框选择后触发  |
| <form>.onsubmit  |        当表单提交时        |
|  <form>.onreset  |        当表单重置时        |

| 系统事件 |                      |
| :------: | :------------------: |
|  onload  |    页面加载完成后    |
| onerror  |    页面加载出错后    |
| onresize | 浏览器窗口大小改变时 |
| onscroll |   页面滚动条滚动时   |

##### DOM事件的使用方式：

```js
document.getElementById("box").onclick = function(){
    // box容器被点击时执行的内容
	...
}
```

##### BOM事件的使用方式：

```js
window.onresize = function(){
	// 当浏览器窗口大小改变时触发的事件
	...
}
```

##### 获取滚动条滚动的距离：（先了解）

```js
var t = document.documentElement.scrollTop   =》（不兼容ie写法）
```

 http://www.360totalsecurity.com/  的导航栏（学了if判断之后讲）

### 5、改变元素的css样式

```js
var oBox = document.getElementById("box")
oBox.style.color = "red"   //  设置单个属性
oBox.style.cssText = "font-size:14px;backgroundColor:green;"
```

设置后的css为内联样式 ， 不会覆盖内部或外部样式表

#### 代替样式操作的方式

需要先提前写好需要切换的多种样式，并以class选择器命名css样式，通过切换class的方式 切换样式（推荐使用）

```js
var oBox = document.getElementById("box")

// 给box元素 添加一个 class active
oBox.classList.add("active")
// 给box元素 移除一个 class active
oBox.classList.remove("active")
// 切换 （有则删除，没有就添加）
oBox.classList.toggle("active")
// 判断是否存在 class （返回true或false）
oBox.classList.contains("active")
```

注：classList 方法仅支持 IE10 +

### 6、获取元素的css样式

getComputedStyle( el )[attr]     获取到的样式属性为一个字符串  

->   只能进行获取，不能赋值**（赋值会报错）**

```js
var oBox = document.getElementById('box')
console.info(getComputedStyle(oBox)["font-size"])   // 16px
console.info(getComputedStyle(oBox).fontSize);		// 16px
```

### 7、获取元素的注意事项

若js文件在body前引入，需要使用window.onload在页面加载完成后 再获取元素。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script>
        // 此处 box 还未创建，所以需要在页面加载完成后触发
        window.onload = function(){
            var oBox = document.getElementById('box')
            console.info(oBox);
        }
    </script>
</head>
<body>
    <div id="box">
        123
    </div>
</body>
</html>
```

### 8、disabled/checked 操作

**disabled 和 checked 操作通过 true / false 控制**

```html
<body style="height: 800px;">
    <input type="button" value="按钮" disabled id="ipt1">
    <input type="radio" checked id="ipt2">
    <script>
        var ipt1 = document.getElementById('ipt1')
        var ipt2 = document.getElementById('ipt2')
        document.body.onclick = function () {
             ipt1.disabled = '';     
             ipt2.checked = false;
            
            // ipt1.disabled = !ipt1.disabled;     
            // ipt2.checked = !ipt2.checked;
        }
    </script>
</body>
```

**获取input框中的内容**

```html
<body>
    <input type="text" value="" id="ipt1">
    <script>
        var ipt1 = document.getElementById('ipt1')
        ipt1.value
    </script>
</body>
```


