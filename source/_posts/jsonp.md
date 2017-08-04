---
title: js跨域资源共享
date: 2017-05-10 10:32:28
tags:  
     - 跨域
     - jsonp
---
# js-Cross-origin-resource-sharing
js跨域资源共享

js跨域访问的方法

<h3>1.jsonp</h3>

因为
```html
<script src='xxx.com'></script>
```
不受同源策略的影响，所以可以用它来跨域，称为jsonp

Jsonp原理： 

首先在客户端注册一个callback, 然后把callback的名字传给服务器。

此时，服务器先生成 json 数据。

然后以 javascript 语法的方式，生成一个function , function 名字就是传递上来的参数 jsonp.

最后将 json 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档，返回给客户端。

客户端浏览器，解析script标签，并执行返回的 javascript 文档，此时数据作为参数，传入到了客户端预先定义好的 callback 函数里.（动态执行回调函数）

<h3>使用方法</h3>

本文后端代码如下

```php
<?php  
  
//服务端返回JSON数据  
$arr=array('a'=>1,'b'=>2,'c'=>3,'d'=>4,'e'=>5);  
$result=json_encode($arr);  
//echo $_GET['callback'].'("Hello,World!")';  
//echo $_GET['callback']."($result)";  
//动态执行回调函数  
$callback=$_GET['callback'];  
echo $callback."($result)"; //字符串拼接  返回 jsonpCallback(a:1,b:2,etc)

```
1.原生使用
```js
<meta content="text/html; charset=utf-8" http-equiv="Content-Type" />  
<script type="text/javascript">  
    function jsonpCallback(result) {  
        alert(result.a);  
        alert(result.b);  
        alert(result.c);  
        for(var i in result) {  
            alert(i+":"+result[i]);//循环输出a:1,b:2,etc.  
        }  
    }  
</script>  
<script type="text/javascript" src="http://crossdomain.com/services.php?callback=jsonpCallback"></script>  
```
其中 jsonCallback 是客户端注册的，获取 跨域服务器 上的json数据 后，回调的函数。

http://crossdomain.com/services.php?callback=jsonpCallback

这个 url 是跨域服务 器取 json 数据的接口，参数为回调函数的名字，返回的格式为

jsonpCallback({msg:'this is json data'})  





2.用jquery
$.getJSON
```js
    $.getJSON("http://crossdomain.com/services.php?callback=?",  
    function(result) {  
        for(var i in result) {  
            alert(i+":"+result[i]);//循环输出a:1,b:2,etc.  
        }  
    }); 

```
$.ajax
```js
    $.ajax({  
        url:"http://crossdomain.com/services.php",  
        dataType:'jsonp',  
        data:'',  
        jsonp:'callback',  
        success:function(result) {  
            for(var i in result) {  
                alert(i+":"+result[i]);//循环输出a:1,b:2,etc.  
            }  
        },  
        timeout:3000  
    }); 
```
$.get
```js
$.get('http://crossdomain.com/services.php?callback=?', {name: encodeURIComponent('tester')}, function (json) { 
  for(var i in json) alert(i+":"+json[i]); 
}, 'jsonp'); 
```

<a href="http://justcoding.iteye.com/blog/1366102/">参考链接</a>

<h3>XMLHttpRequest Level 2</h3>
直接在服务点添加如下头信息
```php
//php
header('Accress-Control-Allow-Origin:*');
header('Accress-Control-Allow-Method:POST,GET');
```
<h3>CORS</h3>
<a href="http://www.ruanyifeng.com/blog/2016/04/cors.html">参考链接</a>




