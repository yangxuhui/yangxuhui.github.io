---
layout: post
title:  Aha! CGI, WSGI
date:   2018-08-04 12:00:00
categories: web开发
---

* 什么是CGI？
* CGI用来解决什么问题？

首先，我们来想一个问题，一个最简单的Web Server在做什么事情？我们考虑最简单的情况，我们在浏览器输入一个网址，比如：http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html, Web Server返回给浏览器一个网页，浏览器渲染展示。这种情况下，一种最简单的可能就是在“服务器根目录/blog/2012/08/”下面有个文件，文件名是“blogging_with_jekylly.html”，Web Server直接按照HTTP协议把这个文件发给浏览器。在这种情况下，我们不需要CGI，也不需要WSGI。现在考虑稍微复杂一些的情况，无论是通过在URL中添加参数的方式还是使用表单的方式，我们向服务器发送请求，服务器需要根据不同的请求参数生成不同的页面返回给浏览器，比如一个天气查询服务，服务器会根据请求的地区的不同返回不同的页面。这种情况下，一种简单可能就是服务器解析请求，进而在数据库中查询请求地区的天气情况，然后生成页面返回给浏览器。我们稍微抽象一下的话，服务器服务动态内容需要解析用户请求，发送给一个进程，然后这个进程处理用户请求，最终生成页面返回给浏览器。  
  
这样，为了提供动态内容和服务，我们需要回答几个问题：  
* 客户端如何向服务器发起不同的请求参数？
* 服务器如何将这些不同的参数传给子进程或者服务进程或者应用程序或者一段脚本？
* 子进程或者服务进程或者一段脚本的输出要返回到哪里？
  
这里第一个问题其实HTTP协议已经回答了，GET请求通过URL中的参数发送请求参数，POST请求通过表单发送请求参数。我们仔细想下其他几个问题。  
  
对于第二个，一个简单的想法就是通过全局变量，子进程从全局变量解析用户请求参数和其他信息；对于第三个问题，最简单的办法，服务进程将结果输出到一个文件里面，并将一个全局变量的名字设置成文件名，这样服务器直接将这个文件发送给浏览器...  
  
我们能想出很多方法来回答这三个问题，这样同样的功能可能在不同的服务器就不能用了。**CGI就是一种用来回答这两个问题并被业界广泛接受和认可的解决方案**。  

## CGI
CGI全称Common Gateway Interface，定义了服务器与服务端服务进程或应用程序或服务脚本的接口。具体来说，CGI描述了请求信息如何通过环境变量传给应用程序，请求包体如何通过标准输入传给应用程序，应用程序如何通过标准输出发送回应。

* **CGI程序从标准输入获取HTML表单内容，通过环境变量获取URL参数和HTTP包头字段**。
* **CGI程序将结果发送到标准输出**。

> Client request -----> Web Server -----> [CGI] -----> Server side Program -----> Database 

CGI的实现细节可以参考csapp中实现的一个简单的HTTP Server [tiny.c](http://csapp.cs.cmu.edu/2e/ics2/code/netp/tiny/tiny.c)

## 参考资料
* [Common Gateway Interface](https://en.wikipedia.org/wiki/Common_Gateway_Interface)
* [tiny.c](http://csapp.cs.cmu.edu/2e/ics2/code/netp/tiny/tiny.c)
* [What is Common Gateway Interface (CGI)](https://stackoverflow.com/questions/2089271/what-is-common-gateway-interface-cgi)