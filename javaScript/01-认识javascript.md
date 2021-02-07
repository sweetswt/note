### 1、javascript简史

JavaScript 诞生于1995 年。当时，它的主要目的是处理以前由服务器端语言负责的一些输入验证操作。

ECMAScript  欧洲计算机制造商协会

1997年1月  -  ECMAScript1诞生

1998年1月  -  ECMAScript2修正

1999年12月  -  ECMAScript3修正

（ECMAscript4 由于关于语言的复杂性出现分歧,第4版本被放弃,其中的部分成为了第5版本的基础）

2009年  -  ECMAScript5修正

2015年  -  ECMAScript6修正（也称为ES2015）

从2015开始每年例行更新 

我们现在要学习的是ES5，之后会学习 ES6 。

ie等极少数浏览器不支持ES6

### 2、基础问题

- JavaScript是什么

专业一点：JavaScript一种直译式脚本语言，是一种动态类型、弱类型、基于原型的语言。

通俗一点：JS是前端代码中最重要的部分（行为层），常用来*操作HTML页面*，*响应用户的操作*，*验证传输数据*等。

- Java和JavaScript有什么关系

没有太大的关系……

 它最初叫 Mocha， 接着改名为 LiveScript，最后才确定命名为 JavaScript，  目的之一就是“看上去像Java”

- jQuery（JQ）和 JS 有什么关系

jQuery是由JS编写的……，js和jq可以同时编写

- NodeJs 和 JS 有什么关系

NodeJs是后端语言，以JS代码来驱动实现后端功能……，降低了前段人员学习后端的难度

### 3、javascript的组成

- 核心（ECMAScript）：提供核心语言功能，规定语法规范。
- 文档对象模型（DOM）：提供访问和操作网页内容的方法和接口，即由html页面中的标签组成
- 浏览器对象模型（BOM）：提供与浏览器交互的方法与接口，即浏览器中前进、后退、刷新等功能

### 4、引入javascript

1 - 内部引入

```html
<head></head>
<body>
    html标签部分
	...
    ...
    
    <script type="text/javascript">
        js代码块部分
		...
        ...
    </script>
</body>
```

2 - 外部引入

```html
<head>
	<script src=""></script>    <!-- 注意不能是单标签 -->
</head>
```

**注意**：若使用外部引入js文件的方式，script标签中的代码块不会被解析

3 - 标签内引入（不建议使用）

```html
<head>
	<div onclick="alert('div被点击了')" >
        哈哈哈
    </div>
</head>

// 没有特殊情况，不建议使用此方法
```

- script标签最好写在 head 标签 或 body 标签中，如果放在head或body外，浏览器在解析时，也会将script标签放在head或body中。
- script标签通常放在body的最后面。
- 页面的解析是按顺序解析的，所以，若在 body 前获取dom元素，则需要使用 window.onload
- 外部引入的js文件，js文件中不需要写 script 标签

------

### 5、console 控制台输出

- console.log  普通输出

- console.info   前面出现蓝色标记（ 在 chrome 上与 console.log 无区别）

- console.warn  输出背景为黄色，警告信息

- console.error  输出背景为红色，错误信息

-  console.dir  可以显示一个对象所有的属性和方法。 

  console.log()可以取代alert()或document.write()，在网页脚本中使用console.log()时，会在浏览器控制台打印出信息。 

##### 快速生成代码片段：

点击编辑器左下角设置图标，选择用户代码片段，输入js，找到javascript.json，输入一下代码，即可通过输入 log + TAB键 快速生产 console.log(' ')

```js
{
	"Print to console": {
		"prefix": "log",
		"body": [
			"console.log('$1');"
		],
		"description": "Log output to console"
	}
}
```

### 6、注释、弹窗

单引号和双引号需要配对，不能单引号配对双引号

```js
// 单行注释 （常用）    -> 注意不是返斜杠 \\

/*
 多行注释
*/

// 弹窗
alert("这是弹出的内容");
ALERT("错误写法，严格区分大小写")

// 输入弹窗
prompt("请输入内容")

// 确认弹窗
confirm("是否确认")
```

### 7、js注意事项

- 严格区分大小写 ( x=1;X=1; x和X不是同一个变量)
- 语法字符使用英文字符，半角字符  
- 缩进对齐
- 分号结尾 （可以不加，看个人或团队开发习惯而定）

