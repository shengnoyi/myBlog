---
title: commuication between browser and server
date: 2017-03-23 14:04:27
tags: 网络基础
---
Link:[What really happens when you navigate to a URL](http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/) 

这篇文章主要分析了当你在浏览器地址栏输入一个url，回车之后发生的事情。

### 1. 浏览器需要把你的域名解析成IP地址，会按照以下顺序
- **Browser cache** – The browser caches DNS records for some time. Interestingly, the OS does not tell the browser the time-to-live for each DNS record, and so the browser caches them for a fixed duration (varies between browsers, 2 – 30 minutes).
- **OS cache** – If the browser cache does not contain the desired record, the browser makes a system call (gethostbyname in Windows). The OS has its own cache.
Router cache – The request continues on to your router, which typically has its own DNS cache.
- **ISP DNS cache** – The next place checked is the cache ISP’s DNS server. With a cache, naturally.
- **Recursive search** – Your ISP’s DNS server begins a recursive search, from the root nameserver, through the .com top-level nameserver, to Facebook’s nameserver. Normally, the DNS server will have names of the .com nameservers in cache, and so a hit to the root nameserver will not be necessary.

### 2. 浏览器向服务器发送http请求
``` bash
GET http://facebook.com/ HTTP/1.1
Accept: application/x-ms-application, image/jpeg, application/xaml+xml, [...]//声明浏览器会接受哪种格式的response
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; [...]//浏览器信息
Accept-Encoding: gzip, deflate//声明浏览器会接受哪种格式的response
Connection: Keep-Alive //告诉服务器为之后的请求保持TCP连接
Host: facebook.com
Cookie: datr=1265876274-[...]; locale=en_US; lsd=WW[...]; c_user=2101[...]
```

### 3. 服务器告诉浏览器重定向到真正的网址
``` bash
HTTP/1.1 301 Moved Permanently
Cache-Control: private, no-store, no-cache, must-revalidate, post-check=0,
      pre-check=0
Expires: Sat, 01 Jan 2000 00:00:00 GMT
Location: http://www.facebook.com/
P3P: CP="DSP LAW"
Pragma: no-cache
Set-Cookie: made_write_conn=deleted; expires=Thu, 12-Feb-2009 05:09:50 GMT;
      path=/; domain=.facebook.com; httponly
Content-Type: text/html; charset=utf-8
X-Cnection: close
Date: Fri, 12 Feb 2010 05:09:51 GMT
Content-Length: 0
```
由于请求的是'facebook.com'而不是'www.facebook.com',所以facebook的服务器告诉浏览器重定向到'www.facebook.com'，这样对于用户来说，输入'facebook.com'也能到达'www.facebook.com',是不是方便了许多？  

那么这里有一个问题，为什么不直接在'facebook.com'返回用户想看的'www.facebook.com'的相应页面就好了呢？
##### 有两个原因：  
1. 搜索引擎排名。'facebook.com'、'www.facebook.com'对于搜索引擎来说，不是同一个网站，流量被分开了，会导致排名降低，这对于网站经营者来说是不利的。
2. cache-friendly.这个是什么意思呢？这是说如果分开这两个域名，那么它们使用的cache就不是能够互相流通的（浏览器的同源策略）,意味着同样的资源你可能要加载两次，不利于性能优化吧。


### 4. 浏览器执行重定向后的请求
``` bash
GET http://www.facebook.com/ HTTP/1.1
Accept: application/x-ms-application, image/jpeg, application/xaml+xml, [...]
Accept-Language: en-US
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; [...]
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
Cookie: lsd=XW[...]; c_user=21[...]; x-referer=[...]
Host: www.facebook.com
```
### 5. 服务器处理请求
服务器获取请求，处理请求，返回一个response。
##### 处理请求
- **Web server software**
	- The web server software (e.g., IIS or Apache) receives the HTTP request and decides which request handler should be executed to handle this request. A request handler is a program (in ASP.NET, PHP, Ruby, …) that reads the request and generates the HTML for the response.
	- In the simplest case, the request handlers can be stored in a file hierarchy whose structure mirrors the URL structure, and so for example http://example.com/folder1/page1.aspx URL will map to file /httpdocs/folder1/page1.aspx. The web server software can also be configured so that URLs are manually mapped to request handlers, and so the public URL of page1.aspx could be http://example.com/folder1/page1.
- **Request handler**
	- The request handler reads the request, its parameters, and cookies. It will read and possibly update some data stored on the server. Then, the request handler will generate a HTML response.

### 6. 服务器返回response
``` bash
HTTP/1.1 200 OK
Cache-Control: private, no-store, no-cache, must-revalidate, post-check=0,
    pre-check=0
Expires: Sat, 01 Jan 2000 00:00:00 GMT
P3P: CP="DSP LAW"
Pragma: no-cache
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
X-Cnection: close
Transfer-Encoding: chunked
Date: Fri, 12 Feb 2010 09:05:55 GMT

2b3
��������T�n�@����[...]
```

Content-Encoding 这个字段是告诉browser,response使用了gzip算法压缩的,你需要解压才行

### 7. 浏览器开始渲染HTML
> Even before the browser has received the entire HTML document, it begins rendering the website

### 8. 浏览器发送HTML中内嵌的资源请求
资源请求的资源可能有图片，css,script等，与请求一个HTML很相似，都需要解析域名、发送请求、重定向等等上面说过的操作，  
**不过**，与动态页面不同的是，浏览器可以使用静态资源的缓存

### 9. 浏览器发送AJAX请求
HTML页面被渲染完成后，客户端仍然有可能通过AJAX技术与服务器通信