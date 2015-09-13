# http跨域

## 跨域概述

> 跨域资源共享标准通过新增一系列HTTP头，让服务器能声明那些来源可以通过浏览器访问该服务器上的资源。另外，对那些会对服务器数据造成破坏性影响的HTTP请求方法
> (特别是GET以外的HTTP方法，或者搭配某些MIME类型的POST请求)，标准强烈要求浏览器必须先以OPTIONS请求方式发送一个预请求（preflight request），
> 从而获知服务器端对跨源请求所支持HTTP方法。在确认服务器允许该跨源请求的情况下，以实际的HTTP请求方法发送那个真正的请求。服务器端也可以通知客户端，
> 是不是需要随同请求一起发送信用信息（包括Cookies和HTTP认证相关数据）。

## 名词解释

### HTTP头概述

> HTTP协议采用请求/响应模型。包括客户端向服务器的请求头和服务端向客户端的响应头。HTTP头包括通用头、请求头、响应头、实体头、扩展头。

- 通用头：客户端和服务端都可以使用，如Date头，两端都可以用它来说明构建报文的时间和日期：

```shell
Date:Sat, 12 Sep 2015 08:46:49 GMT
```

- 请求头：为服务器提供一些额外信息，比如希望接收什么类型的数据

```shell
Accept: */*
```

- 响应头：为客户端提供信息，客户端在与哪种类型的服务器进行交互

```shell
Server:openresty
```

- 实体头：应对实体主体部分的头，比如，可以用实体头来说明实体主体部分的数据类型。

```shell
Content-Type:text/html; charset=utf-8
```
- 扩展头：非标准头，由应用程序开发者创建

## 跨域HTTP请求场景

  - XMLHttpRequest跨域HTTP请求
  - Web字体（CSS中通过@font-face使用跨站字体资源）
  - WebGL贴图
  - 使用drawImage API在canvas上画图

## 访问控制场景

### 简单请求

- 只使用GET,HEAD或者POST请求方法。POST方式，请求头Content-Type只能是`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

  - GET：通常用于请求服务器发送某个资源，传统IE URL最大可用长度2048字符，提交数据大小，一般在2k-8k之间
  ![Alt text](https://raw.githubusercontent.com/zqjflash/http-options/master/http-options.png)

  - HEAD：类似于GET，但只返回头部（比如，判断其类型）
  ![Alt text](https://raw.githubusercontent.com/zqjflash/http-options/master/http-HEAD.png)

  - POST：向服务器输入数据，通常用HTML表单
  ![Alt text](https://raw.githubusercontent.com/zqjflash/http-options/master/http-POST.png)

- 不会使用自定义请求头（类似于X-Modified这种）

- 示例分析

  - topics.html5.qq.com的网页想要访问sc.qq.com资源

  ```js
  Zepto.ajax({
    url: "http://sc.qq.com/fx",
    data: { ... },
    success: function(data) {}
  });
  ```

  - 请求头

  ```html
  Accept:*/*
  Accept-Encoding:gzip, deflate, sdch
  Accept-Language:zh-CN,zh;q=0.8
  Connection:keep-alive
  Host:sc.qq.com
  Origin:http://topcis.html5.qq.com
  Referer:http://topcis.html5.qq.com/topics?action=getHtml&topId=833&from=singlemessage&isappinstalled=0
  User-Agent:Mozilla/5.0 (Linux; Android 4.2.1; en-us; Nexus 4 Build/JOP40D) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166 Mobile Safari/535.19
  ```

  - 响应头

  ```html
  `Access-Control-Allow-Origin:*`
  Cache-Control:no-cache
  Content-Encoding:gzip
  Content-Length:568
  Content-Type:text/html;charset=utf-8
  Pragma:no-cache
  Set-Cookie:sc_id=6193889328175799217; domain=sc.qq.com; path=/; expires=Tue, 10 Jul 2508 06:53:59 GMT
  ```

### 预请求OPTIONS
  OPTIONS方法请求Web服务器告知其支持的各种功能。不用实际访问那些资源就能判定访问各种资源的最优方式。
  - OPTIONS示例
  ![Alt text](https://raw.githubusercontent.com/zqjflash/http-options/master/http-options-new1.png)

  - 请求产生的条件
    - 请求方式非GET、HEAD、POST方法
    - POST方法请求头，数据类型非application/x-www-form-urlencoded，multipart/form-data，text/plain以外的数据类型
    - 使用自定义请求头，比如X-PINGOTHER

  - 示例代码：
  ```js
  var body = '{C}{C}Arun';
  var invocation = new XMLHttpRequest();
  invocation.open('POST', 'http://sc.qq.com/fx/share2nd', true);
  invocation.setRequestHeader('X-PINGOTHER', 'pingpong');
  invocation.setRequestHeader('Content-Type', 'application/xml');
  invocation.send(body);
  ```
  - General
  ```js
  Remote Address:14.17.18.180:80
  Request URL:http://sc.qq.com/fx/share2nd?%22{name:\%22aprilxue\%22,%20count:%203}%22
  Request Method:OPTIONS
  Status Code:200 OK
  ```

  - Request Headers
  ```js
  Accept:*/*
  Accept-Encoding:gzip, deflate, sdch
  Accept-Language:zh-CN,zh;q=0.8
  `Access-Control-Request-Headers:X-PINGOTHER`
  `Access-Control-Request-Method:POST`
  Cache-Control:max-age=0
  Connection:keep-alive
  Host:sc.qq.com
  Origin:http://127.0.0.1:8081
  Referer:http://127.0.0.1:8081/
  User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.132 Safari/537.36
  ```
  > Access-Control-Request-Method: POST 提醒服务器跨站请求将使用POST
  > Access-Control-Request-Headers: X-PINGOTHER 自定义请求头


  - Response Headers
  ```js
  Access-Control-Allow-Origin:*
  Access-Control-Allow-Methods: POST, GET, OPTIONS
  Access-Control-Allow-Headers: X-PINGOTHER
  Access-Control-Max-Age: 1728000
  Cache-Control:no-cache
  Content-Length:29
  Content-Type:text/vnd.wap.wml; charset=utf-8
  Pragma:no-cache
  ```
  > Access-Control-Allow-Methods: POST, GET, OPTIONS 服务器可以接受这三种方法
  > Access-Control-Allow-Headers: X-PINGOTHER 服务器接受自定义请求头
  > Access-Control-Max-Age: 1728000 表示浏览器在20天内针对跨域请求，无需再发送"预请求"

### 附带凭证信息的请求

  - 发送凭证请求（HTTP Cookies和验证信息）的功能。
    - withCredentials: true，使得Cookies可以随请求发送。

  - 请求头
    ```js
    Accept:*/*
    Accept-Encoding:gzip, deflate, sdch
    Accept-Language:zh-CN,zh;q=0.8
    `Access-Control-Request-Headers:X-PINGOTHER`
    `Access-Control-Request-Method:POST`
    Cache-Control:max-age=0
    Connection:keep-alive
    Host:sc.qq.com
    Origin:http://127.0.0.1:8081
    Referer:http://127.0.0.1:8081/
    User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.132 Safari/537.36
    Cookie: pageAccess=2
    ```

  - 响应头
    ```js
    Access-Control-Allow-Origin:http://127.0.0.1:8081
    `Access-Control-Allow-Credentials: true`
    Access-Control-Allow-Methods: POST, GET, OPTIONS
    Access-Control-Allow-Headers: X-PINGOTHER
    Access-Control-Max-Age: 1728000
    Cache-Control:no-cache
    Content-Length:29
    Content-Type:text/vnd.wap.wml; charset=utf-8
    Pragma:no-cache
    ```
    > 带有withCredentials请求发送响应，服务器必须指定允许请求的域名，否则响应会失败

### 附注：
  > Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header
  - 这样, X-My-Custom-Header 和 X-Another-Custom-Header这两个头信息,都可以被浏览器得到.