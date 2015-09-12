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

- 不会使用自定义请求头（类似于X-Modified这种）


### 预请求





































