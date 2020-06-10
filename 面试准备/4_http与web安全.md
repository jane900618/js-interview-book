# 面试准备4 - http与web安全

## 1、http/https 协议
### http2.0
* 多路复用
* 首部压缩
* 服务端推送
* 二进制分帧层

### https

## 2、缓存策略
### 强缓存
> 不需要经过服务器，浏览器根据响应头字段返回的时间判断是否命中，命中返回200，存储于内存/硬盘中

* 查看响应头字段：cache-control
    * max-age:
    * public/private
    * no-store: 不缓存 
    * no-cache: 跳过走协商缓存
* 查看响应头字段：expires(GMT)，不准

### 协商缓存
> 由浏览器提交的响应字段给到服务器，由服务器判断是否命中缓存，命中返回304，否则200

* 查看响应头字段：Etag
    * 提交字段：if-None-Match
* 查看响应头字段：Last-Modified
    * 提交字段：if-Modified-Since
    * 缺点：
        * 最小粒度到S，1S内修改的无法检测
        * 内容不变，只是修改文件，不希望被检测到

## 3、常见状态码
##### 1XX
##### 2XX
* 200: 成功
* 204: 成功，返回无内容
* 206: 成功，返回部分内容

##### 3XX
* 301: 永久重定向
* 302: 临时重定向
* 304: 内容未修改，可用缓存

##### 4XX
* 401: 未授权
* 403: 禁止请求
* 404: 资源不存在

##### 5XX
* 500: 服务器异常

## 4、get/post
* get请求参数在URL上，post请求参数在请求体内
* get请求链接有上限，post请求无上限
* get请求可缓存，post请求不可以

## 5、websocket
> 持久化的协议，服务端可主动push

## 6、TCP三次握手
* C --> syn请求，进入syn_send状态
* S --> syn + ack包，进入syn_recv状态
* C --> ack包，双方进入established状态

## 7、TCP四次挥手
* C --> FIN包，进入FIN_wait状态
* S --> ACK包，进入close_wait状态
* S --> FIN包/ACK包，进入last_ack状态
* C --> ACK包，最大等待2SML，进入close状态

## 8、NodeJS 事件循环
* **timers**：定时器setTimeout和seInterval的执行，将callback加入队列中
* pending callbacks：一些I/O的callback，推迟到下一次循环中执行
* idle, prepare：内部的一些事件
* **poll**：轮循，i/o，回调，fs.readFile()
* **check**：setImmediate的callback执行
* close callbacks：一些关闭的回调函数，如socket

## 9、跨域
### JSONP
> 利用script标签

### CROS
> 服务器端设置`Access-Control-Allow-Origin：*`

## 10、安全
### XSS攻击：跨站脚本攻击
表现：
1. 盗取cookie
2. 监听用户行为，比如输入操作直接到黑客服务器
3. 修改DOM伪造登录表单
4. 在页面生成浮窗广告

实现方式：
1. 存储型：提交到服务器，页面渲染直接执行
2. 发射型：恶意脚本作为网络请求提交到服务器返回，页面直接执行（不存储于服务器）
3. 文档型：不经过服务器，数据传输劫持数据包，修改页面HTML文档

防范： ==> 避免脚本在览器中直接执行
* 对用户输入进行转码 / 过滤
* 设置cookie 的httponly属性

### CSRF攻击：跨站请求伪造 ==> 诱导用户点击链接，打开黑客网站，基于当前页面的登录态发起跨站请求
表现：
1. 自动get请求
2. 自动post请求
3. 诱导点击get请求

防范：
* 利用cookie 的 samesite
* 验证来源站点：origin / referer