# Interview Template
## 一、数据结构、算法、设计模式
1. 二分查找的js递归与非递归实现  
```Javascript
// 递归
function bsearch(array, low, high, target) {
  if (low > high) return -1;
  var mid = Math.floor((low + high) / 2);
  if (array[mid] > target) {
    return bsearch(array, low, mid - 1, target);
  } else if (array[mid] < target) {
    return bsearch(array, mid + 1, high, target);
  } else { 
    return mid; 
  }
}
```
```Javascript
// 非递归实现
function bsearchWithoutRecursion(array, low, high, target) {
  while (low <= high) {
    var mid = Math.floor((low + high) / 2);
    if (array[mid] > target) {
      high = mid - 1;
    } else if (array[mid] < target) {
      low = mid + 1;
    } else {
      return mid;
    }
  }
  return -1;
}
```

2. js中深拷贝的实现  
```Javascript
// 写法1
function deepCopy(source, target) {
  var target = target || {};
  for (var i in source) {
    if (typeof source[i] === 'object') {
      target[i] = (source[i].constructor === Array) ? [] : {};
      deepCopy(source[i], target[i]);
    } else {
      target[i] = source[i];
    }
  }
  return target;
}
var a = { name: 'Lily', Hobbies: ['Music', 'Sport'] };
var b = deepCopy(a);//b={name:'Lily',Hobbies:['Music','Sport']}
a.Hobbies.push('Read');//b={name:'Lily',Hobbies:['Music','Sport','Read']},b={name:'Lily',Hobbies:['Music','Sport']}
```

```Javascript
// 写法2
function deepCopy(source, target) {
  var target = target || {};
  for (var i in source) {
    //防止自引用
    if (source[i] === source)
      continue;
    if (typeof source[i] === 'object') {
      target[i] = (source[i].constructor === Array) ? [] : {};
      deepCopy(source[i], target[i]);
    } else {
      target[i] = source[i];
    }
  }
  return target;
}

var a = { name: 'lily' };
a.obj = a;
var b = deepCopy(a);//b={name:'lily'}
```

3. pub / sub（发布订阅）对比观察者模式？  
https://juejin.cn/post/7036560142548074509 (另一篇)  
观察者模式和发布订阅模式最大的区别就是发布订阅模式有个事件调度中心。
![alt](https://s9.51cto.com/images/blog/202106/25/088686380f55bda1cee3616bc49c8f8b.webp?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)  

    从图中可以看出，观察者模式中观察者和目标直接进行交互，而发布订阅模式中统一由调度中心进行处理，订阅者和发布者互不干扰。这样一方面实现了解耦，还有就是可以实现更细粒度的一些控制。比如发布者发布了很多消息，但是不想所有的订阅者都接收到，就可以在调度中心做一些处理，类似于权限控制之类的。还可以做一些节流操作。

    接下来看一下代码实现  
    https://blog.51cto.com/u_15283585/2958257  
    从上面代码可以看出，观察者模式由具体目标调度，每个被订阅的目标里面都需要有对观察者的处理，会造成代码的冗余。而发布订阅模式则统一由调度中心处理，消除了发布者和订阅者之间的依赖。


4. Mixin的优劣？对比Interface或Inheritance？
  - 什么时候使用Mixin(优点)  
    - 当多个组件/页面有相同的选项,可以通过mixin抽离公共代码,达到复用效果,类似封装组件.
    - Mixin的优点是不需要传递状态
  - Mixin的缺点
    - 命名冲突
    - 隐含的依赖关系(mixin和使用它的组件之间没有层次关系。组件里的变量名称修改后，mixin里没改) 
    - vue3.0中已经将mixin作为备选方案,优先选择使用hook,也是复用的.因为mixin覆盖性会导致不稳定性.类似全局变量,如果mixin被修改,会造成全局污染.所以使用的时候需要小心
  
 - 与Inheritance的区别：

   -  mixin是对Vue类的options进行混入。所有Vue的实例对象都会具备混入进来的配置行为。

   - extend是产生一个继承自Vue类的子类，只会影响这个子类的实例对象，不会对Vue类本身以及Vue类的实例对象产生影响。
 - 与Interface的区别
   - 传统的「接口」概念中并不包含实现，而 Mixin 包含实现



## 二、网络基础  
1. cors - options的作用   
https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS  
OPTIONS请求旨在发送一种“探测”请求以确定针对某个目标地址的请求必须具有怎样的约束（比如应该采用怎样的HTTP方法以及自定义的请求报头），然后根据其约束发送真正的请求。比如针对“跨域资源”的预检（Preflight）请求采用的HTTP方法就是OPTIONS。

    简而言之，OPTIONS请求方法的主要用途有两个：

    (1) 获取服务器支持的HTTP请求方法；

    (2) 用来检查服务器的性能。
  
    规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。

    “需预检的请求”要求必须首先使用 OPTIONS   方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。

    当请求满足下述任一条件时，即应首先发送预检请求（使用OPTIONS）：  
      (1) 使用了下面任一 HTTP 方法：PUT、DELETE、CONNECT、OPTIONS、TRACE、PATCH

      (2) 人为设置了对 CORS 安全的首部字段集合之外的其他首部字段。该集合为：  
      Accept  
      Accept-Language  
      Content-Language  
      Content-Type (but note the additional requirements below)  
      DPR  
      Downlink  
      Save-Data  
      Viewport-Width  
      Width

      (3) Content-Type 的值不属于下列之一:  
      application/x-www-form-urlencoded  
      multipart/form-data


2. http 2.0 相对 1.x 的区别  
https://www.zhihu.com/question/306768582    
- http 1.x版本问题  
在传输数据过程中，所有内容都是明文，客户端和服务器端都无法验证对方的身份，无法保证数据的安全性。  
HTTP/1.1 版本默认允许复用TCP连接，但是在同一个TCP连接里，所有数据通信是按次序进行的，服务器通常在处理完一个回应后，才会继续去处理下一个，这样子就会造成队头阻塞。  
http/1.x 版本支持Keep-alive，用此方案来弥补创建多次连接产生的延迟，但是同样会给服务器带来压力，并且的话，对于单文件被不断请求的服务，Keep-alive会极大影响性能，因为它在文件被请求之后还保持了不必要的连接很长时间。

- http 2.0  
**二进制分帧** 这是一次彻底的二进制协议，头信息和数据体都是二进制，并且统称为"帧"：头信息帧和数据帧。  
**头部压缩** HTTP 1.1版本会出现 User-Agent、Cookie、Accept、Server、Range 等字段可能会占用几百甚至几千字节，而 Body 却经常只有几十字节，所以导致头部偏重。HTTP 2.0 使用 HPACK 算法进行压缩。  
**多路复用** 复用TCP连接，在一个连接里，客户端和浏览器都可以同时发送多个请求或回应，且不用按顺序一一对应，这样子解决了队头阻塞的问题。  
**服务器推送** 允许服务器未经请求，主动向客户端发送资源，即服务器推送。  
**请求优先级** 可以设置数据帧的优先级，让服务端先处理重要资源，优化用户体验  

3. tcp请求3次握手,除了ACK和SYN外还有什么  
**SYN：同步标志**   
    同步序列编号(Synchronize Sequence Numbers)栏有效。该标志仅在三次握手建立TCP连接时有效。它提示TCP连接的服务端检查序列编号，该序列编号为TCP连接初始端(一般是客户端)的初始序列编号。在这里，可以把TCP序列编号看作是一个范围从0到4，294，967，295的32位计数器。通过TCP连接交换的数据中每一个字节都经过序列编号。在TCP报头中的序列编号栏包括了TCP分段中第一个字节的序列编号。也可以简单理解成，建立连接。

    **ACK：确认标志​** 

    确认编号(Acknowledgement Number)栏有效。大多数情况下该标志位是置位的。TCP报头内的确认编号栏内包含的确认编号(w+1，Figure-1)为下一个预期的序列编号，同时提示远端系统已经成功接收所有数据。

    **RST：复位标志​**

    复位标志有效。用于复位相应的TCP连接，即连接重置。

    **URG：紧急标志​**

    紧急(The urgent pointer) 标志有效。紧急标志置位。

    **PSH：推标志​**

    该标志置位时，接收端不将该数据进行队列处理，而是尽可能快将数据转由应用处理。在处理 telnet 或 rlogin 等交互模式的连接时，该标志总是置位的。即表示有data传输。

    **FIN：结束标志​**

    带有该标志置位的数据包用来结束一个TCP回话，但对应端口仍处于开放状态，准备接收后续数据。

4. 对websocket的理解  
    - WebSocket 是一个真正“全双工”的通信协议，与 TCP 一样，客户端和服务器都可以随时向对方发送数据
    - WebSocket 采用了二进制帧结构，语法、语义与 HTTP 完全不兼容，但因为它的主要运行环境是浏览器，为了便于推广和应用，就不得不“搭便车”，在使用习惯上尽量向 HTTP 靠拢，这就是它名字里“Web”的含义。
    - 服务发现方面，WebSocket 没有使用 TCP 的“IP 地址 + 端口号”，而是延用了 HTTP 的 URI 格式，但开头的协议名不是“http”，引入的是两个新的名字：“ws”和“wss”，分别表示明文和加密的 WebSocket 协议。
    - WebSocket 的默认端口也选择了 80 和 443，因为现在互联网上的防火墙屏蔽了绝大多数的端口，只对 HTTP 的 80、443 端口“放行”，所以 WebSocket 就可以“伪装”成 HTTP 协议，比较容易地“穿透”防火墙，与服务器建立连接

5. Server - Sent Events

6. 对http缓存的理解

7. localhost，127.0.0.1和0.0.0.0差别  
https://cloud.tencent.com/developer/article/1562123  
- 0.0.0.0和127.0.0.1  
 **共同点：**  

  都属于特殊地址。  
  都属于A类地址。  
  都是IPV4地址。 

  **区别：**

  在服务器中，0.0.0.0指的是本机上的所有IPV4地址，，如果我绑定的端口指定了0.0.0.0，那么通过内网地址或外网地址都可以访问我的应用。但是如果我只绑定了内网地址，那么通过外网地址就不能访问，所以如果绑定0.0.0.0,也有一定安全隐患，对于只需要内网访问的服务，可以只绑定内网地址。
  在路由中，0.0.0.0表示的是默认路由，即当路由表中完全没有找到完全匹配的路由的时候所对应的路由。  

  **用途**  

  回环测试,通过使用ping 127.0.0.1 测试某台机器上的网络设备，操作系统或者TCP/IP实现是否工作正常。
  DDos攻击防御：网站收到DDos攻击之后，将域名A记录到127.0.0.1，即让攻击者自己攻击自己。
  大部分Web容器测试的时候绑定的本机地址。

- localhost   
  localhost其实是个域名，而不是一个ip地址。之所以我们经常把localhost与127.0.0.1认为是同一个是因为我们使用的大多数系统上都默认localhost指向了127.0.0.1这个地址。 
  在linux系统中，/ets/hosts文件中都会有如下内容：

  **总结**  
  127.0.0.1是一个环回地址或仅本地接口的ip地址（从该接口发送出去，双从该接口接收回来）。  

  > 127.0.0.1 是一个环回地址。并不表示“本机”。0.0.0.0才是真正表示“本网络中的本机”。 
  在实际应用中，一般我们在服务端绑定端口的时候可以选择绑定到0.0.0.0，这样我的服务访问方就可以通过我的多个ip地址访问我的服务。 
  比如我有一台服务器，一个外放地址A,一个内网地址B，如果我绑定的端口指定了0.0.0.0，那么通过内网地址或外网地址都可以访问我的应用。但是如果我之绑定了内网地址，那么通过外网地址就不能访问。 所以如果绑定0.0.0.0,也有一定安全隐患，对于只需要内网访问的服务，可以只绑定内网地址。



## 三、BOM、DOM、浏览器相关
1. localStorage、sessionStorage使用场景
 - 1.sessionStorage的数据缓存是保存在网页窗口的进程内存或线程内存中的，当网页窗口关闭的时候sessionStorage的数据缓存也自动清除了
- 2.localStorage的数据缓存是保存在物理硬盘中的，数据不会随着网页窗口关闭或者关闭浏览器而丢失。当需要清除的时候需要到浏览器的设置里手动清除

- localStorage应用场景  
  > localStorage可以用于存储该浏览器对该页面的访问次数，当然，如果换个浏览器，这个次数就重新开始计数了。还可以用来存储一些固定不变的页面信息，这样就不需要每次都重新加载了，这个值也可以进行覆盖。

  访问这个页面的时候，script 脚本会自动运行，localStorage.pagecount就会 ++ 了，从而达到统计页面访问次数的目的。
- sessionStorage
  1. SessionStorage本地存储，仅在当前会话有效，关闭页面或浏览器后被清除。
  2. 有三个方法：setItem、getItem和removeItem。SessionStorage.setItem("key", "value")，SessionStorage.getItem("key")，SessionStorage.removeItem("key")。
  3. 存储数据类型：字符串类型的键值对。
  4. 清除所有的key/value：sessionStorage.clear()。


2. 跨域请求如何携带cookie  
https://www.teqng.com/2022/03/09/%E9%9D%A2%E8%AF%95%E5%AE%98-%E8%B7%A8%E5%9F%9F%E8%AF%B7%E6%B1%82%E5%A6%82%E4%BD%95%E6%90%BA%E5%B8%A6cookie/  
   **跨域携带 Cookie**  
   - 这时候需要后端添加另外一个请求头：  
   Access-Control-Allow-Credentials: true
   - 前端在发送请求时也需要设置:  
   xhr = new XMLHttpRequest();  
   xhr.withCredentials= true;  //关键句  
   xhr.open("GET", url);  
   xhr.send(); 


3. cookie httpOnly 意义 
https://www.wangan.com/articles/864  
生成cookie时使用HttpOnly标志有助于降低客户端脚本访问受保护cookie的风险（如果浏览器支持它）。 如果HTTP响应头中包含HttpOnly标志，只要浏览器支持HttpOnly标志，客户端脚本就无法访问cookie。    
HttpOnly 标志–保护 Cookies 免受 XSS 攻击

4. 如何监听html外链资源加载失败，并设计一套重试方案  
http://www.alloyteam.com/2021/01/15358/  
 - 方案一：script onerror  
我们可以给 script 标签添加上 onerror 属性，这样在加载失败时触发事件回调，从而捕捉到异常。     
    ```Javascript
    <script onerror="onError(this)"></script> 
    ```
   
- 方案二：window.addEventListener  
答案否定的，因为 onerror 的事件并不会向上冒泡，window.onerror 接收不到加载失败的错误。冒泡虽不行，但捕获可以！我们可以通过捕获的方式全局监控加载失败的错误，虽然这也监控到了脚本错误，但通过 !(event instanceof ErrorEvent) 判断便可以筛选出加载失败的错误。

5. 了解performance api  
https://javascript.ruanyifeng.com/bom/performance.html   
https://kuro-p.github.io/2019/07/11/%E4%BD%BF%E7%94%A8%20Performance%20APi%20%E8%BF%9B%E8%A1%8C%E5%89%8D%E7%AB%AF%E6%80%A7%E8%83%BD%E7%9B%91%E6%8E%A7/  
  performance是html5的新特性之一，该接口会返回当前页面性能相关的信息。共提供4个属性：  
   - navigation: 包含页面加载、刷新、重定向情况
   - timing: 包含了各种与浏览器有关的时间数据
   - memory: 返回JavaScript对内存的占用
   - timeOrigin: 返回性能测量开始时的时间的高精度时间戳


6. Mutation Observer、Intersection Observer使用场景  
https://juejin.cn/post/6999950594207121444  

## 四、JavaScript基础
### 语法规范
1. let与var的区别，暂时性死区是否了解  
    ```Javascript
    console.log(x)
    let x = 2

    console.log(x)
    var x = 3
    ```

2. 考察var变量声明和变量提升  
    ```Javascript
    (function () {
      var x = y = 1;
    })();
    console.log(y);
    console.log(x);

    //输出
    1
    Uncaught ReferenceError: x is not defined

    ```

3. 考察js的对象字面量中，key只能为字符串的知识点，同时考察对js隐式类型转换的了解  
    ```Javascript
    let foo = {}
    let obj = {}
    foo[obj] = 'hello'

    foo: {
    ?:’hello’
    }
    ```



4. 考察对for of /for in迭代的了解，for in中不输出length属性的原因，for of以及对es6迭代器模式的认识  
    ```Javascript
    let list = [4, 5, 6];

    for (let i in list) {
      console.log(i);
    }

    for (let i of list) {
      console.log(i);
    }

    let obj = { age: 1, sex:’male’ }//改造obj，符合迭代器模式，可以被for of迭代
    ```

5. Arrow function与普通function中this指向的区别
    ```Javascript
    var a = 10
    var obj = {
      a: 20,
      say: () => {
        console.log(this.a)
      }
    }
    obj.say()
    var anotherobj = { a: 30 }
    obj.say.apply(anotherobj)
    ```




6. 对typescript的使用经验，下面函数声明的意义
    ```Javascript
    function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
    }
    ```

7. es6 modules 循环引用  
    ```Javascript
    //a.mjs
    import { bar } from './b';
    console.log('a.mjs');
    console.log(bar);
    export let foo = 'foo';
    //b.mjs
    import { foo } from './a';
    console.log('b.mjs');
    console.log(foo);
    export let bar = 'bar';
    ```

    执行node--experimental - modules a.mjs 结果
    ReferenceError: foo is not defined

    //export var foo = 'foo';如果换成var呢
    b.mjs
    undefined
    a.mjs
    bar
***
### 方法实现

1. js 数据类型判断，例如实现isArray函数

    ```Javascript
    function isArray(value) {
    }
    function isArray(value) {
      return Object.prototype.toString.call(value) == '[object Array]';
    }
    ```

2. 使用promise js sleep函数实现
    ```Javascript
    function sleep(seconds) { }

    function sleep(seconds) { return new Promise(resolve => setTimeout(resolve, seconds * 1000)) }
    ```


3. 对原型链的理解，实现instanceof方法
    ```Javascript
    function instanceOf(leftVaule, rightVaule) {
      let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值
      leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值 
      while (true) {
        if (leftVaule === null) { return false; }
        if (leftVaule === rightProto) { return true; }
        leftVaule = leftVaule.__proto__
      }
    }

    function instanceof (instance, constructor){ }


    function A() { }
    var a = new A()
    ```

4. js中Call方法的polyfill
    ```Javascript
    Function.prototype.call = function (context) {

    }

    //测试代码
    var foo = {
      name: 'Selina'
    }
    var name = 'Chirs';
    function bar(job, age) {
      console.log(this.name);
      console.log(job, age);
    }
    bar.call(foo, 'programmer', 20);
    // Selina programmer 20
    bar.call(null, 'teacher', 25);
    // 浏览器环境: Chirs teacher 25
    ```

5. 对js中bind函数的实现
    ```Javascript
    if (!Function.prototype.bind) (function () {
      var ArrayPrototypeSlice = Array.prototype.slice;
      Function.prototype.bind = function () {
        var thatFunc = this, thatArg = arguments[0];
        var args = ArrayPrototypeSlice.call(arguments, 1);
        if (typeof thatFunc !== 'function') {
          // closest thing possible to the ECMAScript 5
          // internal IsCallable function
          throw new TypeError('Function.prototype.bind - ' +
            'what is trying to be bound is not callable');
        }
        return function () {
          args.push.apply(args, arguments);
          return thatFunc.apply(thatArg, args);
        };
      };
    })();
    ```

6. 高阶函数实现函数缓存
    ```Javascript
    const memorize = function (fn) {

    }
    const add = function (a) {
      return a + 1
    }
    const adder = memorize(add)
    adder(1)    // 2，经过计算得出结果    
    adder(1)    // 2，不经过计算，从内存缓存中获得结果
    adder(2)    // 3，经过计算得出结果
    ```

7. 限频函数的实现debounce

  ```Javascript
  function debounce(func, wait, immediate = true) {
    let timeout, context, args;
    // 延迟执行函数
    const later = () => setTimeout(() => {
      // 延迟函数执行完毕，清空定时器
      timeout = null
      // 延迟执行的情况下，函数会在延迟函数中执行
      // 使用到之前缓存的参数和上下文
      if (!immediate) {
        func.apply(context, args);
        context = args = null;
      }
    }, wait);
    let debounced = function (...params) {
      if (!timeout) {
        timeout = later();
        if (immediate) {
          //立即执行
          func.apply(this, params);
        } else {
          //闭包
          context = this;
          args = params;
        }
      } else {
        clearTimeout(timeout);
        timeout = later();
      }
    }
    debounced.cancel = function () {
      clearTimeout(timeout);
      timeout = null;
    };
    return debounced;
  };
  ```

### 原理泛谈

1. 对promise的了解  
首先是否了解promise调用链形成的关键点，然后是否可以写出一个基本的promise结构  
https://juejin.cn/post/7002008722629197832  
https://mengera88.github.io/2017/05/18/Promise%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/

2. 对Event loop, micro task, task的认识    
https://juejin.cn/post/6844903651526311949   
https://www.shuzhiduo.com/R/kvJ30lL95g/      
https://segmentfault.com/a/1190000038985579  

3. 对fetch的认识  
是否对其body / headers / request / response结构有了解  
https://juejin.cn/post/6844903783491698701

4. 模块化理解  
CommonJS, AMD, CMD, ES6 Module的区别  
https://kuro-p.github.io/2019/01/03/%E5%89%8D%E7%AB%AF%E6%A8%A1%E5%9D%97%E5%8C%96/



## 五、css与动画
### css基础

1. position absolute与relative的关联关系是什么  
https://www.cnblogs.com/yuer20180726/p/11006972.html   
https://www.zhangxinxu.com/wordpress/2010/12/css-%E7%9B%B8%E5%AF%B9%E7%BB%9D%E5%AF%B9relativeabsolute%E5%AE%9A%E4%BD%8D%E7%B3%BB%E5%88%97%EF%BC%88%E4%BA%8C%EF%BC%89/ 

2. flex布局模型  
https://segmentfault.com/a/1190000023801961

3. css modules  
https://juejin.cn/post/6952665769209495566    
https://chinese.freecodecamp.org/news/introduction-to-css-modules-part-one/#:~:text=%E6%A0%B9%E6%8D%AECSS%20Modules%20%E5%9C%A8GitHub,%EF%BC%88%E7%B1%BB%E4%BC%BC%E4%BA%8E%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4%EF%BC%89%E3%80%82

4. meta viewport是什么  
https://juejin.cn/post/6844904110873919502

### 动画
1. css3 动画  
页面垂直水平居中已有shopee六个字母，实现这六个字母从页面上方依次掉落到起始位置，掉落完成后，六个字母依次沿着起始水平线持续做弹性跳动，这个动画过程的关键点

### css技巧
1. CSS绘制三角形箭头  
  ```CSS
  .box{
    width: 0;
    height: 0;
    border: 50px solid;
    border - color: red transparent transparent transparent;
  }
  ```

2. 利用vw / vh做设备兼容性适配  
https://juejin.cn/post/6844903939196846094

3. 1px border实现  
https://juejin.cn/post/6844904000484016136


## 六、前端框架
### React

1. react domdiff，平级比较key的作用？  
https://juejin.cn/post/6844903905629831176

2. Controlled Component 与 Uncontrolled Component。  
https://zh-hans.reactjs.org/docs/uncontrolled-components.html  
https://stackoverflow.com/questions/42522515/what-are-react-controlled-components-and-uncontrolled-components

3. React如何处理events？synthetic event？top level single listener ? Event pooling ?  
https://juejin.cn/post/6844904066397503502

4. 怎么防止某个component error导致页面空白, Errorboundry意义  
https://blog.csdn.net/qq_41499782/article/details/114798578  
https://segmentfault.com/a/1190000039824075

5. React Fiber ? 设计的意义何在？解决了哪些问题？  
https://juejin.cn/post/6844904167836745735

6. import React from “react”  
  为什么在组件中需要引入React
    ```Javascript
    //comp.js
    import React from “react”
    const Comp = () => {
      return <div>comp</div>
    }
    ```


7. setState(function () { })的多种调用形式的使用场景

8. react 16后，新的生命周期  
https://juejin.cn/post/6844903655372488712

9. react suspense / lazy 实现原理
https://juejin.cn/post/6844904191853494280

10. react hooks是否了解，有无引用  
https://zh-hans.reactjs.org/docs/hooks-effect.html 

11. 对redux / mobx是否了解  
https://segmentfault.com/a/1190000011148981

12. redux store如何设计  
https://segmentfault.com/a/1190000019152612

13. redux异步请求中间件了解哪些，关注点有什么区别  
https://juejin.cn/post/6844903504427892750

14. 是否了解dva这种业界方案 ?
https://juejin.cn/post/6844903955802112007  
https://blog.csdn.net/weixin_42224055/article/details/107951987  

15. react hooks了解  
  下面代码的问题
    ```Javascript
    function Counter() {
      const [count, setCount] = useState(0);

      useEffect(() => {
        const id = setInterval(() => {
          setCount(count + 1);
        }, 1000);
        return () => clearInterval(id);
      }, []);

      return <h1>{count}</h1>;
    }

    function Counter() {
      const [count, setCount] = useState(0);

      useEffect(() => {
        const id = setInterval(() => {
          setCount(c => c + 1); // ✅ 在这不依赖于外部的 `count` 变量
        }, 1000);
        return () => clearInterval(id);
      }, []); // ✅ 我们的 effect 不适用组件作用域中的任何变量

      return <h1>{count}</h1>;
    }
    ```


## Vue

1. Vue slot是什么  
https://juejin.cn/post/6844903920037281805

2. vm.$nextTick(function () { }) 组件中用来做什么的？Vue是如何实现的？

3. Vue的生命周期分别都会发生什么？比如mounted vs.created 的区别？

4. Vue render方法使用

5. Keep - alive特性的原理

6. 全局api形式调用Toast.show()的实现

7. Vue的defineProperty与proxy区别？


## AngularJS
1. scope意义
2. digest循环
3. apply方法
4. directive的使用

## Angular
1. zone是否了解
2. 对rxjs的了解


## 七、多端能力
### Hybrid h5开发能力
1. webview是什么
2. 如何实现js / webview通信
3. 离线包方案是否了解

### React Native
1. 横向对比react - router, react - navigation

2. RN的native modules如何实现？如何和JS交流？为什么JS性能差？
RCTBridgeModule
Bridge is async callback / events based, 并且没有JIT

3. RN的ListView优化？


4. RN的线程模型？哪些工作是在哪些线程上？
JS线程卡顿怎么办
UI线程卡顿怎么办
Native Modules线程卡顿怎么办
Render线程卡顿怎么办

5. rn 打包、多模块、热更新等基础设施建设有无了解

6. rn业务首屏优化有无了解



### PWA
1. pwa包括的内容
2. Service worker使用经验，生命周期，更新过程
3. 缓存机制


## Node.js
1. node master / cluster的了解

2. nodejs异步异常处理

3. nodejs不停服更新方法

4. 对pm2的了解

5. 对业界流行web框架的使用经验

## 八、工程应用
1. Webpack的构建过程？Tree shaking / dead code elimination / live code import?
加速构建过程的方法
热更新机制

2. Webpack的插件是如何制作的？比如，replace text, uglifyJS等。
对Webpack compiler / compilation的了解


3. babel按需引入polyfill怎么用
```Javascript
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        modules: false,
        useBuiltIns: 'usage',
        debug: true,
        targets: {
          browsers: [
            "chrome >= 43",
            "and_chr >= 43",
            "ios >= 10",
            "android >= 4.4"
          ]
        }
      }
    ],
  ]

```

4. h5应用首屏优化方案(前端及服务端角度)

 


5. nodejs服务端经验，基于node做过什么类型应用，是否涉及db，如何运维

6. 主导过的典型技术项目是什么

7. 有无对前端工程化的实践总结

## 九、上机题


1. js大数相加
    ```Javascript
    function add(a, b){ }
    console.log(add('111111122222222111111122222222', '1'))
    //'111111122222222111111122222223'
    ```

2. stringify  
    ``` Javascript
    var schema = {
      "title": "Example Schema",
      "type": "object",
      "properties": {
        "firstName": {
          "type": "string",
          "required": true
        },
        "lastName": {
          "type": "string"
        },
        "age": {
          "type": "integer"
        }
      },
    }

    var obj = {
      firstName: 'Matteo',
      lastName: 'Collina',
      age: 32
    }

    function build(schema) {
      let code = `
        'use strict'
        let json = '{'
      `
      Object.keys(schema.properties).forEach((key, i, a) => {
        const { type, required } = schema.properties[key];
        code += `
          json += '"${key}":'
        `
        if (required) {
          code += `
            if(!obj.hasOwnProperty('${key}')){
                throw new Error('${key} is required!');
            }
            `
        }

        switch (type) {
          case 'string':

            code += `
              json += obj.hasOwnProperty('${key}') ? '"' + obj.${key} + '"' : undefined
            `
            break;
          case 'integer':
            code += `
              json += obj.hasOwnProperty('${key}') ? '' + obj.${key} : undefined
            `
            break;
          default:
            throw new Error(`${type} unsupported`)
        }
        if (i < a.length - 1) {
          code += 'json += \',\''
        }
      })
      code += `
        json += '}'
        return json
      `
      return new Function('obj', code)
    }

    console.log(build(schema)(obj))
    //{"firstName":"Matteo","lastName":"Collina","age":32}
  ```



3. 打印red，停1s，打印yellow，停2s，然后循环5次   
    ```Javascript
      function sleep(seconds) { return new Promise(resolve => setTimeout(resolve, seconds * 1000)) }

      async function func() {
        for (let i = 0; i < 5; i++) {
          console.log('red')
          await sleep(1)
          console.log('yellow')
          await sleep(2)
          console.log('green')
          await sleep(4)
        }
      }

      func()
    ```
