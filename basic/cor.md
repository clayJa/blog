## 同源策略&跨域
#### 什么是浏览器同源策略？
同源策略(Same Origin Policy,SOP)也叫单源策略(Single Origin Policy),
是一种用于Web浏览器编程语言（如JavaScript和Ajax）的安全措施，用于保护信息的机密性和完整性。同源策略可防止网站的脚本访问其他网站上使用的脚本并与之交互。

假设您已登录Facebook并在另一个浏览器选项卡中访问恶意网站。如果没有同源策略，该网站上的JavaScript可以对您允许的Facebook帐户执行任何操作。例如，在提交表单之前输入密码后，阅读私人消息，发布状态更新，分析HTML DOM树。
Facebook当然希望使用JavaScript来增强用户体验。因此，浏览器可以检测到此JavaScript是否可以访问Facebook资源，这一点非常重要。这就是相同的原始策略发挥作用的地方：如果JavaScript包含在facebook.com上的HTML页面中，它可能会访问facebook.com资源。

现在如果将例子中的Facebook换为网上银行，这就很可怕了。

#### 什么是同源？
如果两个页面的协议，端口（如果有指定）和域名三者都相同，则两个页面具有相同的源。
例如：

页面A | 页面B | 是否同源 | 原因
------------ | ------------- | ------------ | ------------
http://a.clayja.com | https://a.clayja.com  | 不同源 | 协议不同
http://a.clayja.com | http://b.clayja.com  | 不同源 | 域名不同
http://a.clayja.com | http://a.clayja.com:8080  | 不同源 | 端口不同

#### 为什么会发生跨域？
+ 您的应用必须与不同源的REST API的第三方服务（如第三方登录、支付）集成。
+ 您的服务器端服务托管在不同的（子）域上。
+ 客户端逻辑的资源来源与服务器端服务端点不同。

and so on...
#### 如何解决跨域问题？
##### *CORS*
CORS 全称是跨域资源共享（Cross-Origin Resource Sharing），是一种 ajax 跨域请求资源的方式，支持现代浏览器，IE支持10以上。

实现方式：当你使用 XMLHttpRequest 发送请求时，浏览器发现该请求不符合同源策略，会给该请求加一个请求头：Origin。后台进行一系列处理，如果确定接受请求则在返回结果中加入一个响应头：Access-Control-Allow-Origin; 浏览器判断该相应头中是否包含 Origin 的值，如果有则浏览器会处理响应，我们就可以拿到响应数据，如果不包含浏览器直接驳回，这时我们无法拿到响应数据。所以 CORS 的表象是让你觉得它与同源的 ajax 请求没啥区别，代码完全一样。

*server.js*
```javascript
var http = require('http')
var fs = require('fs')
var path = require('path')
var url = require('url')

http.createServer(function(req, res){
  var pathObj = url.parse(req.url, true)

  switch (pathObj.pathname) {
    case '/getData':
      //  handle request
      // 返回结果中加入一个响应头Access-Control-Allow-Origin,允许跨域
      res.setHeader('Access-Control-Allow-Origin','*')
      res.end('hello world')
      break;
    default:
      fs.readFile(path.join(__dirname, pathObj.pathname), function(e, data){
        if(e){
          res.writeHead(404, 'not found')
          res.end('<h1>404 Not Found</h1>')
        }else{
          res.end(data)
        }
      }) 
  }
}).listen(8080)
```
*index.js*
```javascript
  function getData(){
    var xhr = new XMLHttpRequest()
    xhr.open('GET', 'http://127.0.0.1:8080/getData', true)
    xhr.send()
    xhr.onload = function(){
      appendHtml(JSON.parse(xhr.responseText))
    }
  }
```
##### *JSONP*
当html页面包含javascript文件时，html代码看起来像这样。
```javascript
<script src="http://otherdomain.com/javascripts/.js" type="text/javascript"></script>
```
通过HTML 中 script 标签可以加载其他域下的js，比如我们经常引入一个其他域下线上cdn的静态js资源，仿佛浏览器并没有对script做同源策略限制，那么我们能不能通过script标签来实现跨域呢？我们尝试一下。
```javascript
<script src="http://127.0.0.1:8080/getData" type="text/javascript"></script>
```
但是需要注意的是，获取数据后浏览器将其作为js代码来执行，但是我们的数据一般为json格式，直接运行肯定行不通。

想想，如果是运行一个函数就好了，如果我得到是一段可以运行的js代码字符串就好了。如果这样试试呢？
```javascript
<script src="http://127.0.0.1:8080/getData?callback=showData"></script>
```
我们预先声明一个全局的数据处理函数，并将函数名传递给服务器端，服务器端直接将数据拼接为函数参数的格式，（例如：showData({data:'hello world'})）返回给浏览器执行，那不就成功完成数据读取了吗？

##### *降域*
脚本可以将 document.domain 的值设置为其当前域或其当前域的父域。如果将其设置为其当前域的父域，则较短的域将用于后续源检查。

所以，对于类似a.example.com与b.example.com之间的跨域操作，可以通过设置document.domain来规避跨域问题。
```javascript
document.domain = "example.com";
```
使用 document.domain 来允许子域安全访问其父域时，您需要在父域和子域中设置 document.domain 为相同的值。

浏览器单独保存端口号。任何的赋值操作，包括 document.domain = document.domain 都会导致端口号被重写为 null 。因此 example.com:8080 不能仅通过设置 document.domain = "example.com" 来与example.com 通信。必须在他们双方中都进行赋值，以确保端口号都为 null 。
##### *postMessage*
window.postMessage() 方法可以安全地实现跨源通信。

window.postMessage() 方法被调用时，会在所有页面脚本执行完毕之后（e.g., 在该方法之后设置的事件、之前设置的timeout 事件,etc.）向目标窗口派发一个  MessageEvent 消息。 

语法:
```javascript
otherWindow.postMessage(message, targetOrigin, [transfer]);
```
otherWindow为其他窗口的一个引用，比如iframe的contentWindow属性、执行window.open返回的窗口对象、或者是命名过或数值索引的window.frames。

例如：在 a.example.com/a.html 发送message，在 b.example.com/b.html 接收message。

*a.html*
```html
<script>
  window.frames[0].postMessage('hello','*')
</script>
```
*b.html*
```html
<script>
  window.addEventListener('message',function(e){
    console.log(e.data);
  })
</script>
```
##### 参考资料
[浏览器的同源策略-MDN](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

[CORS-MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

[postMessage-MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)