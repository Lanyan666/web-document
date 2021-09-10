### 一、CSRF: Cross-Site Request Forgery  跨域请求伪造

是一种网络攻击方式，攻击者盗用了你的身份，以你的名义发送恶意请求

防御策略： 验证HTTP Referer字段、在请求地址中添加token并验证、在HTTP头中自定义属性并验证

### 二、CORS: Cross Origin Resource Sharing 跨站资源共享

定义了浏览器与服务器如何跨源通信。基本思路就是使用自定义的HTTP头部 允许浏览器和服务器互相了解，以确实请求或相应应该成功还是失败。默认情况下，跨源请求不提供凭据（cookie、HTTP认证和客户端SSL证书）

服务端设置`Access-Control-Allow-Origin`就可以开启CORS,该属性表示哪些域名可以访问资源，如果设置通配符（*）则表示所有网站都可以访问资源（

##### (1）简单请求

条件1：使用下列方式之一   HEAD   GET    POST

条件2： Content-Type的值仅限于下列三者之一：   text/plain  multipart/form-data   application/x-www-form-urlencoded

##### (2)复杂请求

不符合以上条件的就是复杂请求了。复杂请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为“预检”请求，该请求是option方法的

```js
// 允许哪个方法访问我
res.setHeader('Access-Control-Allow-Methods', 'PUT')
// 预检的存活时间
res.setHeader('Access-Control-Max-Age', 6)
// OPTIONS请求不做任何处理
if (req.method === 'OPTIONS') {
  res.end() 
}
// 定义后台返回的内容
app.put('/getData', function(req, res) {
  console.log(req.headers)
  res.end('我不爱你')
})

```



### 三、替代性跨源技术

#### 1、图片探测

利用<img>标签实现跨域通信，任何页面都可以跨域加载图片而不必担心限制，与服务器之间是一种简单、单向的跨域通信。频繁用于跟踪用户在 页面上的点击操作或动态显示广告。

#### 2、JSONP

JSONP只支持**GET和异步**请求的，不存在同步请求和其他的请求方式

JSONP看起来和JSON一样，只是会被包含在一个函数调用里，包含两个部分：回调和数据。回调是在页面接收响应之后应该调用的函数，而数据就是作为参数传给回调函数的JSON数据。例如:  `http://frrgeiop.net/json/?callback=handleResponse`

JSONP调用是通过动态创建<script>元素并为src属性指定跨域URL实现的

```js
let srcipt = document.createElement("script");
script.src = "http://www.baidu.com/?callback=handleResponse";
document.body.inserBefore(script,document.body.firstChild);
function handleResponse(response){
    console.log(IP address ${response.ip})
}
```

缺点：一是不可保证域的可靠性；二是不好确定JSONP请求是否失败，虽然HTML5规定了<script>元素的onerror事件处理程序，但还没有任何浏览器实现，因此，开发者经常使用计时器来决定是否放弃等待响应。

### 四、nginx反向代理

nginx将访问请求分配给压力小的服务器 

