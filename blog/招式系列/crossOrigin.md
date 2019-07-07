# 浅谈跨域

## 何为跨域

### 含义

广义的跨域，指的是从一个域名去请求另外一个域名的资源。资源跳转： A 链接、重定向、表单提交。
资源嵌入： <link>、<script>、<img>、<frame>等 dom 标签，还有样式中 background:url()、@font-face()等文件外链。
脚本请求： js 发起的 ajax 请求、dom 和 js 对象的跨域操作等。

### 作用

## 同源策略

## 跨域解决方案

- [CORS](#CORS)
- [jsonp](#jsonp)
- [document.domain + iframe](#document.domain-+-iframe)
- [location.hash + iframe](#location.hash-+-iframe)
- [window.name + iframe](#window.name-+-iframe)
- [postMessage](#postMessage)
- [nginx 代理](#nginx-代理)
- [nodejs 中间件代理](#nodejs-中间件代理)
- [WebSocket 协议](#WebSocket-协议)

### CORS

### jsonp

### document.domain + iframe

### location.hash + iframe

### window.name + iframe

### postMessage

### nginx 代理

### nodejs 中间件代理

### WebSocket 协议

## 总结

### 参考文章

1. [浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

2. [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)

3. [前端常见跨域解决方案（全）](https://www.cnblogs.com/roam/p/7520433.html)
