# DOM 

Document Object Model   -   文档对象模型


## 1、childNodes

- 获取元素节点的所有子节点（空格也算一个字符）
- IE 低版本获取到的只能获取到 元素节点，无法获取到文本节点

```html
<body>
    <div class="box">
        <p>1</p>
        <p>2</p>
        <p>3</p>
    </div>

    <script>
        let oBox = document.querySelector('.box')
        let oPs = oBox.childNodes
        
        // 空格会被解析为一个空字符串 的数组项
        console.info(oPs)   // NodeList(7) [text, p, text, p, text, p, text]
    </script>
</body>
```

###### 节点类型

![](D:\Windows 10 Documents\Desktop\节点各属性参数.png)

- 
  getAttributeNode() 方法从当前元素中通过名称获取属性节点。


提示：如果你仅想返回属性值请使用 getAttribute 方法。


```html
<body>
    <div id="box">
        <p>1</p>
        <p>2</p>
        <p>3</p>
    </div>

    <script>
        var element = document.getElementById("box");
        var attr = document.getElementById("box").getAttributeNode("id");
        var text = document.getElementById("box").firstChild;  // 即childNodes[0]

        console.log(element.nodeType);     //1
        console.log(attr.nodeType);        //2
        console.log(text.nodeType);        //3
    </script>
</body>
```

###### childNodes优化

去除childNodes获取到子节点中的空项

```js
let myChildNodes = el => {
    let child = document.querySelect(el).childNodes
    let childArr = []
    child.forEach( (item,index) => {
        if( item.nodeType === 1 ){
            childArr.push( item )
        }
    } )
    return childArr
}
```

## 2、children

- 获取 元素节点的所有 子元素节点

```html
<body>
    <div class="box">
        <p>1</p>
        <p>2</p>
        <p>3</p>
    </div>

    <script>
        let oBox = document.querySelector('.box')
        let oPs = oBox.children
        
        // 获取到的为 HTMLCollection，不能进行forEach操作
        console.info(oPs)   // HTMLCollection(3) [p, p, p]
    </script>
</body>
```

## 3、parentNode

- 获取元素节点的父元素节点


```html
<body>
    <div class="box">
        <p id="ppp">111</p>
    </div>

    <script>
        let oP = document.getElementById('ppp')
        console.info(oP.parentNode);  // <div class="box">...</div>
        
        // 获取“爷爷”节点
        console.info(oP.parentNode.parentNode)
    </script>
</body>
```

## 4、offsetParent

- 获取元素节点的最近的带有定位的父元素节点（ 若父节点都没有定位，则返回 body 节点 ）

  （包括 absolute、relative、fixed 、sticky 定位）

```html
<body>
    <div id="app" style="position: absolute;">
        <div class="box">
            <p id="ppp">111</p>
        </div>
    </div>

    <script>
        let oP = document.getElementById('ppp')
        console.info(oP.offsetParent);   // 返回 #app 节点
    </script>
</body>
```

## 5、有兼容性问题的DOM操作

### firstElementChild    

- 返回元素的第一个子元素节点

  在 IE8- 下的写法 ：  firstChild    

  **兼容写法**：

  ```js
  first = xxx.firstElementChild || xxx.firstChild
  ```

### lastElementChild    

- 返回元素的最后一个子元素节点

  在 IE8- 下的写法 ：   lastChild   

  **兼容写法**：

  ```js
  last = xxx.lastElementChild || xxx.lastChild
  ```

### previousElementSibling   

- 返回元素的上一个同级节点

  在 IE8- 下的写法 ：previousSibling    

  **兼容写法**：

  ```js
  previous = xxx.previousElementSibling || xxx.previousSibling
  ```

### nextElementSibling

- 返回元素的下一个同级节点

  在 IE8- 下的写法 ： nextSibling    

  **兼容写法**：

  ```js
  next = xxx.nextElementSibling || xxx.nextSibling    
  ```

## 6、+= 拼接会出现的问题

```html
<body>
    <div id="app">
        <p id="ppp">1111111</p>
    </div>

    <script>
        let app = document.getElementById('app')
        document.querySelector("#ppp").onclick = () => alert(1)

        // += 操作只会保留之前的html结构，不会保留之前节点上绑定的事件，导致点击p时点击弹窗事件不会触发
        app.innerHTML += `<a href="http://www.tanzhouedu.com">潭州教育</a>`
        
    </script>
</body>
```

## 7、创建和添加节点

- document.createElement( <节点> ) ：创建节点
- <父节点>.appendChild( <子节点> )   : 将子节点添加到父节点的最后
-   document.createTextNode( <文本内容> )   ：创建文本节点，等同于 innerText
-   <父节点>.insertBefore( <新创建节点> , <子节点> )   ：将新创建的节点插入指定子节点的前面（ 第二个参数使用 null，会将节点添加到父节点的最后 ）
-   <父节点>.replaceChild( <新创建节点> , <子节点> )   ：使用新创建的节点 替换指定子节点
-   < 父节点>.removeChild( <子节点> )   ：删除一个节点中的指定子节点
-   <元素>.remove( )   ：删除某个节点

```html
<body>
    <div id="app">
        <p id="text">我是一段话</p>
    </div>
    
    <script>
        let app = document.getElementById('app')
        let text = document.getElementById('text')
        
        // 创建一个新的节点
        let oDiv = document.createElement('div')
        // 创建一个文本节点
        let oText = document.createTextNode('一段文本哈哈哈')
        // 将新创建的文件节点添加到新创建的div中
        oDiv.appendChild(oText)
        // 将 oDiv 添加到 #app 的最后
        app.appendChild(oDiv)
        // 删除 oDiv 节点
        oDiv.remove()
    </script>
</body>
```

#### 封装insertAfter方法

```js
let insertAfter = (parentNode,newNode,refNode) => {
    var oNext = refNode.nextElementSibling || refNode.nextSibling;
    // insertBefore 方法的第二个参数为null时，则将新节点添加到父节点的最后
    parentNode.insertBefore(newNode, oNext)
}
```

## 8、cloneNode   

- 克隆一个节点

- 浅克隆 ：不克隆该元素的子元素 ( 该元素内的文本也不保留 )   

- 深克隆 ：  克隆该元素以及元素中的所有子元素（包含文本内容）

  ```js
  var text = document.getElementById('text')
  
  // 浅克隆 - 参数不写默认为false
  let newNode1 = text.cloneNode(false)
  
  // 深克隆 
  let newNode2 = text.cloneNode(true)
  ```


## 9、文档碎片 - createDocumentFragment

解决使用 appendChild 多次添加节点时，页面多次进行渲染的问题

**使用 DocumentFragment 处理节点，速度和性能远远优于直接操作 DOM。**

```html
<!-- 普通写法 - 页面多次渲染 -->
<body>
    <div class="box">

    </div>

    <script>
        let oBox = document.querySelector('.box')
        let p1 = document.createElement('p')
        let p2 = document.createElement('p')

        oBox.appendChild(p1)
        oBox.appendChild(p2)
    </script>
</body>


<!-- 使用文档碎片 - 页面只进行一次渲染 -->
<body>
    <div class="box">

    </div>

    <script>
        let oBox = document.querySelector('.box')
        let p1 = document.createElement('p')
        let p2 = document.createElement('p')

        // 创建文档碎片
        let fragment = document.createDocumentFragment()
        // 先将所有节点添加到文档碎片中 - 不进行页面渲染
        fragment.appendChild(p1)
        fragment.appendChild(p2)
        // 将文档碎片再添加到文档中
        oBox.appendChild(fragment)
    </script>
</body>

```



# BOM

Browser Object Model  -  浏览器对象莫模型 

## 1、重要事件

### onresize

- 窗口大小改变时触发

```js
window.onresize = () => console.info(1)
```

### onscroll

- 滚动条滚动时触发

```js
window.onscroll = () => console.info(1)
```

### onfocus/onblur

- onfocus - 进入当前标签页时触发（包括切换标签页，从任务栏最大化浏览器时）

- onblur - 离开当前标签页时触发 （包括切换标签页，将浏览器最小化到任务栏时）

  常用来配合 title 做一些操作

```js
window.onfocus = () => {
    document.title = "处于当前页面"
}
window.onblur = () => {
	document.title = "快回来"
}
```

## 2、重要对象

###  location 

获取/设置 URL相关的属性。 

#### 四种跳转方式

```js
location="http://www.tanzhouedu.com"
location.assign("http://www.tanzhouedu.com")
location.replace("http://www.tanzhouedu.com")
location.href = "http://www.tanzhouedu.com"
```

#### location 中的常用属性

```js
location.pathname  // url中端口号后的部分

location.search  // url中的参数部分

location.host   // 带端口号的服务器地址
```

### history

控制页面的前进、后退、刷新

```js
history.forward() // 前进一个页面

history.back() // 后退一个页面

history.go( 0 )  // 刷新页面
history.go( n )  // 前进/后退n个页面 
```

###  navigator 

 获取浏览器相关的信息

```js
navigator.userAgent   // 浏览器信息
```





