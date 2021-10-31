#  http协议

- 无连接、无状态、简单快速、灵活

## Request

### 请求行



### 请求报头

### 请求正文



## Response

### 状态行

### 消息报头

### 响应正文



```bash
# curl 以http的方式请求百度, -v 获取详情
curl -v http://www.baidu.com	
```



- 埋点

```js
  const img = new Image();
  img.src = "/api/users?abc=123";
```



## 跨域

- 跨域:浏览器器同源策略略引起的接⼝口调⽤用问题
  - 协议、域名、端口 不同，即跨域
  - ip地址和网址也跨域



常⽤用解决⽅方案:

1. JSONP(JSON with Padding)，前端+后端⽅方案，绕过跨域

前端构造script标签请求指定URL(由script标签发出的GET请求不不受同源策略略限制)，服务器器返 回⼀一个函数执⾏行行语句句，该函数名称通常由查询参callback的值决定，函数的参数为服务器器返回的 json数据。该函数在前端执⾏行行后即可获取数据。

2. 代理理服务器器 请求同源服务器器，通过该服务器器转发请求⾄至⽬目标服务器器，得到结果再转发给前端。

前端开发中测试服务器器的代理理功能就是采⽤用的该解决⽅方案，但是最终发布上线时如果web应⽤用和 接⼝口服务器器不不在⼀一起仍会跨域。

3. CORS(Cross Origin Resource Share) - 跨域资源共享，后端⽅方案，解决跨域 预检请求
    https://www.jianshu.com/p/b55086cbd9af
   原理理:cors是w3c规范，真正意义上解决跨域问题。它需要服务器器对请求进⾏行行检查并对响应头做相应处理理， 从⽽而允许跨域请求。





### Access-Control-Allow-Origin



### credential请求

如果要携带cookie信息，则请求变为credential请求:

cookies

```js

// 预检options中和/users接⼝口中均需添加
res.setHeader('Access-Control-Allow-Credentials', 'true'); 
// 设置cookie
res.setHeader('Set-Cookie', 'cookie1=va222;')
// index.html
// 观察cookie存在
console.log('cookie',req.headers.cookie) 

// axios 设置此项才能带cookies
axios.defaults.withCredentials = true
```



### **Proxy**代理理模式

反向代理：指代理发生在服务器端；反之，正向代理发生在客户端

- 使用中间件 http-proxy-middleware，webpack也用的是这款

```js
const express = require('express')
const app = express()
const { createProxyMiddleware } = require('http-proxy-middleware')

//express内置了 静态资源的中间件
app.use(express.static(__dirname + '/'))

app.use('/api', createProxyMiddleware({ target: 'http://localhost:4000', changeOrigin: false }));
app.listen(3000, () => {
  console.log('proxy on 3000');
})

module.exports = app
```

