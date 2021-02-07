# cookie

## 1、什么是 Cookie？

Cookie 是在 **HTTP 协议**（flie协议下不生效）下，服务器或脚本可以维护客户工作站上信息的一种方式。 

Cookie 是一些数据, 存储于你电脑上的文本文件中。

当 web 服务器向浏览器发送 web 页面时，在连接关闭后，服务端不会记录用户的信息。

Cookie 的作用就是用于解决 "如何记录客户端的用户信息":

- 当用户访问 web 页面时，他的名字可以记录在 cookie 中。
- 在用户下一次访问该页面时，可以在 cookie 中读取用户访问记录。

Cookie 以名/值对形式存储，如下所示:

```js
username='Tom'
```

当浏览器从服务器上请求 web 页面时， 属于该页面的 cookie 会被添加到该请求中。服务端通过这种方式来获取用户的信息。



## 2、创建、读取、删除cookie

#### 创建 cookie

```js
// 创建 cookie ，并设置过期时间 
document.cookie = "username=Tom; expires=Thu, 18 Dec 2043 12:00:00 GMT"

// 不设置过期时间，默认浏览器关闭时清除 cookie 
document.cookie = "username=Tom;"
```

- 如果不设置过期时间则默认关闭浏览器删除 cookie 

#### 读取全部 cookie

```
document.cookie
```

#### 删除指定 cookie 

-  设置 expires 参数为以前的时间即可 

```js
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT";
```

## 3、封装

#### 创建

```js
function setCookie(cname, cvalue, exdays) {
    var d = new Date();
    d.setTime(d.getTime() + (exdays * 24 * 60 * 60 * 1000));
    var expires = "expires=" + d.toGMTString();
    document.cookie = cname + "=" + cvalue + "; " + expires;
}
```

#### 读取

```js
 function getCookie(cname) {
     var name = cname + "=";
     var ca = document.cookie.split(';');
     for (var i = 0; i < ca.length; i++) {
         var c = ca[i].trim();
         if (c.indexOf(name) == 0) { 
             return c.substring(name.length, c.length); 
         }
     }
     return "";
 }
```

#### 删除

```js
function removeCookie(cname) {
    document.cookie = cname + "=;expires=Thu, 01 Jan 1970 00:00:00 GMT"
}
```



## 4、注意

cookie在存储时，需要转换为字符串类型，所以否则对象和数组将被强制转换为 `[object Object]`

- **cookie在创建时需要 JSON.stringify()将json转换为字符串**  

- **cookie在接收时需要 JSON.parse() 将字符串转换为json**

  

## 5、jQuery插件

#### 下载

地址 ： https://plugins.jquery.com/cookie/ 

下载之后引入：

```js
<script type="text/javascript" src="js/jquery.min.js"></script>
<script type="text/javascript" src="js/jquery.cookie.js"></script>
```

- 创建

  ```js
  // 创建一个在浏览器关闭时删除的 cookie
  $.cookie('key', 'value');
  
  // 创建一个有时间的 cookie
  $.cookie('key', 'value', { expires: 7 });
  ```

- 读取

  ```js
  $.cookie('key');
  ```

- 删除

  ```js
  $.cookie('key', null);   //通过传递null作为cookie的值即可
  ```

  

# localStorage

## 1、和cookie的区别

- localStorage 拓展了 cookie 的 4K 限制。 

- localStorage 会可以将第一次请求的数据直接存储到本地，这个相当于一个 5M 大小的针对于前端页面的数据库，相比于 cookie 可以节约带宽，但是这个却是只有在高版本的浏览器中才支持的。

- **localstorage支持flie协议**

  

## 2、局限

- 浏览器的大小不统一，并且在 IE8 以上的 IE 版本才支持 localStorage 这个属性。 
- 目前所有的浏览器中都会把localStorage的值类型限定为string类型，这个在对我们日常比较常见的JSON对象类型需要一些转换。
- localStorage在浏览器的隐私模式下面是不可读取的。 
- localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡。 
- localStorage不能被爬虫抓取到。



## 3、创建、读取、删除localStorage

 localStorage 用于长久保存整个网站的数据，保存的数据没有过期时间，直到手动去删除。 

#### 创建

```js
localStorage.setItem("key", "value");
```

#### 读取

```js
var lastname = localStorage.getItem("key");
```

#### 删除

```js
localStorage.removeItem("key")
```

