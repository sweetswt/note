# ajax

*ajax* 即“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术。

ajax可以在不刷新页面的前提下向后端 发送/请求 数据，在开发中是必然会用的技术。



## 1、JSON 格式

json格式中的键必须要有双引号, 使用 JSON.stringify 与 JSON.parse 可在 JSON 格式与字符串格式直接进行转换


```json
{ 
    "name":"tom",
    "age":18
}
```



## 2、封装原生ajax

```js
// 创建XHR核心对象
let createXHR = () => {
    if (window.XMLHttpRequest) {
        // 现代浏览器兼容
        return new XMLHttpRequest();
    }
    // IE兼容
    return new ActiveXObject('Microsoft.XMLHttp');
}

// 传递参数拼接 封装
let getparams = json => {
    let str = '';
    for (let k of Object.keys(json)) {
        str += k + '=' + json[k] + '&';
    }
    str = str.slice(0, str.length - 1);
    return str;
}

// 封装ajax
let ajax = opts => {
    opts.type = opts.type == undefined ? 'get' : opts.type;   //默认get方式
    opts.async = opts.async == undefined ? 'true' : opts.async;   //默认异步
    opts.dataType = opts.dataType == undefined ? 'json' : opts.dataType;  // 默认使用json格式
    opts.data = opts.data == undefined ? {} : opts.data;

    let xhr = createXHR();
    // 判断请求方式 get/post
    if (opts.type == 'get') {
        xhr.open('get', opts.url + '?' + getparams(opts.data), opts.async);
        xhr.send(null);
    } else {
        xhr.open('post', opts.url, opts.async);
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        xhr.send(getparams(opts.data));
    }
    // 判断同步/异步方式
    if (opts.async) {
        // 监听请求状态（当 readyState 改变时，就会触发 onreadystatechange 事件）
        xhr.onreadystatechange = function () {
            /*
            xhr.readyState：
                0: 请求未初始化
                1: 服务器连接已建立
                2: 请求已接收
                3: 请求处理中
                4: 请求已完成，且响应已就绪
             */
            if (xhr.readyState == 4) {
                // 请求处理到了最后一步
                getResult()
            }
        }
    } else {
        // 请求处理到了最后一步
        getResult();
    }
    // 处理回调数据
    let getResult = () => {
        /*
        xhr.status 状态码
            100+  请求已被接受，需要继续处理
            200+  请求已成功被接受和处理
            300+  通常代表重定向
            400+  客户端请求发生了错误
            500+  服务端发生了错误
         */
        if (xhr.status >= 200 && xhr.status < 300) {
            let res;
            // 若回调参数为 json 类型,则将其进行JSON.parse转换
            if (opts.dataType == "json") {
                res = JSON.parse(xhr.responseText); //返回json数据
            } else {
                res = xhr.responseText; //返回纯text
            }
            opts.success(res);
        } else {
            opts.error();
        }
    }
}


// 调用方法 : 
// ajax({
//     url:'xxx'  // 必须
//     data: { },  // 参数  默认为空  非必须
//     type: "post",  // 请求方式  默认get  非必须
//     async: true,  // 同步/异步  默认异步  非必须 
//     dataType: 'json', // 回调参数类型  默认json  非必须 
//     success(cbVal) { // 成功后的回调函数  非必须 
//         console.log(cbVal);
//     },
//     error() {  // 状态不为200时的回调函数  非必须 
//         console.info('请求失败');
//     }
// })
```



## 3、jQuery的ajax

 使用jQuery的ajax之前需要先引入jQuery库。我们可以下载jQ源码来引入，或者直接从各种CDN库引入，比如引入jq3.3.1版本：`  <script src="https://cdn.jsdelivr.net/npm/jquery@3.3.1/dist/jquery.min.js"></script>  `。 

```js
$.ajax({
    method : "POST" //请求方式，默认get(1.9版本前只能使用 type ，1.9版本后可使用 method 或 type)
    ,url : "/url" //请求地址
    ,data : {} //需要发送的数据
    ,dataType : "json" //对请求返回的数据预处理（默认 json 格式）
    ,success : function(data){} //请求成功的回调函数
    ,error : function(err){} //请求失败的回调函数
});
```

注意：jQuery 封装 ajax 时使用了Promise，所以也可以直接使用 then 来接收参数

**\*\*jquery1.5.1+开始支持Promise\*\***

## 4、Promise

#### 同步和异步

 JavaScript是一门单线程的语言，因此，JavaScript在同一个时间只能做一件事，单线程意味着，如果在同个时间有多个任务的话，这些任务就需要进行排队，前一个任务执行完，才会执行下一个任务 。

```js
function fun1(){
    console.info(1)
}
function fun2(){
	console.info(2)
}
fun1()
fun2()

// 依次打印 1 2 
```

因为代码是从上到下依次执行，执行完fun1()，才继续执行fun2()，但是如果fun1()中的代码执行的是读取文件或者ajax操作，文件的读取和数据的获取都需要一定时间，难道我们需要完全等到fun1()执行完才能继续执行fun2()么？为了解决这个问题，后面我们会介绍同步和异步的概念 。

- 同步：  

  同步任务是指在主线程上排队执行的任务，只有前一个任务执行完毕，才能继续执行下一个任务 。

- 异步：

  JavaScript的异步机制包括以下几个步骤

  ```
  （1）所有同步任务都在主线程上执行，行成一个执行栈
  （2）主线程之外，还存在一个任务队列，只要异步任务有了结果，就会在任务队列中放置一个事件
  （3）一旦执行栈中的所有同步任务执行完毕，系统就会读取任务队列，看看里面还有哪些事件，那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行
  （4）主线程不断的重复上面的第三步
  ```

  ```js
  setTimeout(() => {
      
      console.info('2')
      
      setTimeout(() => { 
          console.info('3'); 
      }, 0)
      
  }, 0)
  
  console.info('1');
  
  // 依次输出 1 2 3 
  
  // 先执行主线程上的 1 ，此时外层定时器处于队列中，当 1 执行完成后，外层定时器进入主线程，内层定时器进入队列，当 2 执行完成后，内层定时器进入主线程，开始执行 3
  ```

#### 回调地狱

```js
setTimeout(() => {
    console.info('2');
    setTimeout(() => {
        console.info('3');
        setTimeout(() => {
            console.info('4');
            setTimeout(() => {
                console.info('5');
            }, 50)
        }, 200)
    }, 50)
}, 100)

console.info('1');

// 依次打印 1 2 3 4 5
```

使用 Promise 解决 回调地狱：

```js
new Promise((res, rej) => {
    res(2)
}).then(data => {
    console.info(data)
    return new Promise((res, rej) => {
        res(3)
    })
}).then(data => {
    console.info(data)
    return new Promise((res, rej) => {
        res(4)
    })
}).then(data => {
    console.info(data)
    return new Promise((res, rej) => {
        res(5)
    })
}).then(data => {
    console.info(data)
}).catch(()=>{
    // catch 可以捕捉到每个 Promise 中的 rej ，所以写在最后即可(因为 then 中只捕捉 res，所以当某个Promise 中执行 rej 时，后面的 then 不会再执行，所以 rej 只会出现一次，所以只需要捕捉一次)
    console.info('rej之后出发的函数')
})

console.info(1);
```

使用函数：

```js
let fn2 = () => new Promise((res, rej) => {
    res(2)
})
let fn3 = () => new Promise((res, rej) => {
    res(3)
})
let fn4 = () => new Promise((res, rej) => {
    res(4)
})
let fn5 = () => new Promise((res, rej) => {
    res(5)
})

fn2()
  .then(data => {
    console.info(data)
    return fn3()
}).then(data => {
    console.info(data)
    return fn4()
}).then(data => {
    console.info(data)
    return fn5()
}).then(data => {
    console.info(data)
}).catch(err=>{
    // catch 可以捕捉到每个 Promise 中的 rej ，所以写在最后即可
    console.info('rej之后出发的函数')
})

console.info(1);
```

#### async/await 配合 Promise

ES8中出现了 async/await，通常我们用来配合 Promise 来解决回调地狱，因为 async/await 使得异步代码看起来像同步代码 ，便于理解和维护。

```js
async function fn() {
    let data2 = await new Promise((res, rej) => {
        res(2)
    })
    let data3 = await new Promise((res, rej) => {
        res(3)
    })
    let data4 = await new Promise((res, rej) => {
        res(4)
    })
    let data5 = await new Promise((res, rej) => {
        res(5)
    })
    console.info(data2)
    console.info(data3)
    console.info(data4)
    console.info(data5)
}

fn()

console.info(1);
```

注意： promise 返回的 resolve 数据可以用 await 去接，但是 reject 无法用 await 接收到，所以要用 try catch 去处理 

###### try/catch 处理 reject 

```js
async function fn() {
    try {
        let data2 = await new Promise((res, rej) => {
            res(2)
            // rej(100)
        })
        let data3 = await new Promise((res, rej) => {
            res(3)
        })
        let data4 = await new Promise((res, rej) => {
            res(4)
        })
        let data5 = await new Promise((res, rej) => {
            res(5)
        })
        console.info(data2)
        console.info(data3)
        console.info(data4)
        console.info(data5)
    } catch (e) {
        console.info(e)
    }
}

fn()

console.info(1);
```

#### Promise.all().then()

作用：当所有的 Promise 全部执行完成后，将所有的数据一起返回（返回格式为一个数组）

```js
let fn1 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口1获取完毕')
        res('接口1数据')
    }, 1000)
})

let fn2 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口2获取完毕')
        res('接口2数据')
    }, 2000)
})

let fn3 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口3获取完毕')
        res('接口3数据')
    }, 3000)
})

Promise.all([fn1(), fn2(), fn3()]).then(data => {
    console.info(data)   // ["接口1数据", "接口2数据", "接口3数据"]
})
```

#### promise.race().then()

作用：获取返回值最快的 Promise 数据 ，其他 Promise 数据不再返回

```js
let fn1 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口1获取完毕')
        res('接口1数据')
    }, 1000)
})

let fn2 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口2获取完毕')
        res('接口2数据')
    }, 2000)
})

let fn3 = () => new Promise((res, rej) => {
    setTimeout(() => {
        console.info('接口3获取完毕')
        res('接口3数据')
    }, 3000)
})

Promise.race([fn1(), fn2(), fn3()]).then(data => {
    console.info(data)   // 接口1数据    -因为接口1的数据获取的最快
})
```

## 5、axios

 axios是一个基于Promise的HTTP库，可以用在浏览器和node.js中。原生js并不支持axios，浏览器环境需要引入axios `  <script src="https://cdn.bootcss.com/axios/0.19.0-beta.1/axios.min.js"></script>  ` ，node环境需要安装对应的包。 

#### get方式

```js
axios.get('/url',{
    params:{            //params 中的参数为 随url一起传递的参数 ( url?id='xxx')
        id:'接口配置参数（相当于url?id=xxxx）'，
    },
}).then(function(res){
    console.log(res.data);//处理成功的函数 相当于success
}).catch(function(error){
    console.log(error)//错误处理 相当于error
})
```

#### post方式

```js
axios.post('url',
    {id:xxx}
).then(function(res){
    console.log(res.data);//处理成功的函数 相当于success
}).catch(function(error){
    console.log(error)//错误处理 相当于error
})
```

#### 通用方式

```js
axios({
    method:'get',
    url:'xxx',
    params:{id:123}, //params 表示 随url 一起传参
})
//------------------------------------------//
axios({
    method: ’post’,
    url: ’/user/12345’,
    data: { //data 用于post 传参
        firstName: ’Fred’,
        lastName: ’Flintstone’
    }
});
```

#### axios.all()

使用方式类似于 Promise.all()

```js
function reqA(){
    return axios.get("url1");
}
function reqB(){
    return axios.get("url2");
}

axios
    .all( [ reqA(),reqB() ] )
    .then(res=>{
        console.log(res);
    });
```

#### axios拦截器

- 请求拦截器（请求发送前）

  ```js
  axios.interceptors.request.use(
      config => {
          console.info(config)
  
          // return 出的即向后台发送的配置
          return config
      },
      error => Promise.reject(error)
  )
  ```

- 响应拦截器（请求成功，回调执行前）

  ```js
  axios.interceptors.response.use(
      response => {
          console.info(response)
  
          // return 出的即请求执行成功时获得的数据
          return response
      },
      error => Promise.reject(error)
  )
  ```

- 移除拦截器

  ```js
  // 移除请求拦截器
  let interRequest = axios.interceptors.request.use(
      config => {
          console.info(config)
          return config
      },
      error => Promise.reject(error)
  )
  
  axios.interceptors.request.eject(interRequest)
  
  // 移除响应拦截器
  let interResponse = axios.interceptors.response.use(
      response => {
          console.info(response)
  
          // return 出的即请求执行成功时获得的数据
          return response
      },
      error => Promise.reject(error)
  )
  
  axios.interceptors.response.eject(interResponse)
  ```

#### 配置全局请求域名

```js
 axios.defaults.baseURL  = "http://127.0.0.1:8080/"
```



## 6、jsonp

#### 跨域

当前 协议、ip、端口 三项任意一项不一致时，则出现跨域

- 协议：http、https
- ip：域名、由4组数字组成的ip（000.000.000.000）
- 端口：http默认端口为 80 ，https默认端口为 443

#### JSONP 实现跨域

- 我们通常可以通过 cdn 的形式引入外部的 js 文件，这么做不会被限制， jsonp 方式即引入一个外部 js 文件，这个文件中有一个函数，函数的参数为要获得的数据，所以我们可以提前定义好该函数，就可以获得后台返回的数据。

通过 jsonp 获得百度搜索框提示功能数据

```js
// 创建 script 标签
let script = document.createElement('script')

// 输入需要联系提示的词
let val = "hello"

// 通过 script 标签引入 js 文件的形式，引入以下地址的指定名函数（函数参数即要获取的数据）
script.src = 'https://www.baidu.com/su?wd='+val+'&cb=toyo'

// 将 script 标签添加到页面中
document.body.appendChild(script)

// 在页面中提前定义好接受 jsonp 数据的函数
function toyo(opts) {
    console.info(opts)   // 要获取的数据
}
```

#### 总结

需要注意的是，callback参数定义的方法是需要前后端定义好的，具体什么名字，商讨好就可以了。其实jsonp的整个过程就类似于前端声明好一个函数，后端返回执行函数。执行函数参数中携带所需的数据 。
