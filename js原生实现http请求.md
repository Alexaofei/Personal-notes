### js原生实现http请求
##### 分享者：敖飞

---

#### 什么是http
 >HTTP(HyperText Transfer Protocol)是一套计算机通过网络进行通信的规则。计算机专家设计出HTTP，使HTTP客户（如Web浏览器）能够从HTTP服务器(Web服务器)请求信息和服务，HTTP目前协议的版本是1.1.HTTP是一种无状态的协议，无状态是指Web浏览器和Web服务器之间不需要建立持久的连接，这意味着当一个客户端向服务器端发出请求，然后Web服务器返回响应(response)，连接就被关闭了，在服务器端不保留连接的有关信息.HTTP遵循请求(Request)/应答(Response)模型。Web浏览器向Web服务器发送请求，Web服务器处理请求并返回适当的应答。所有HTTP连接都被构造成一套请求和应答。
 
####  http格式
- 请求方法URI协议/版本
- 请求头(Request Header)
- 请求正文
```
				GET /feProject/js.html HTTP/1.1
Host			project.4399ued.com
Pragma			no-cache
Cache-Control	no-cache
User-Agent		Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36
Content-Type	application/x-www-form-urlencoded
Accept			*/*
Referer			http://project.4399ued.com/feProject/js.html
Accept-Encoding	gzip, deflate, sdch
Accept-Language	zh-CN,zh;q=0.8
```

#### XMLHttpRequest对象
XMLHttpRequest 对象用于在后台与服务器交换数据。
   XMLHttpRequest 对象是开发者的梦想，因为您能够：
   - 在不重新加载页面的情况下更新网页
   - 在页面已加载后从服务器请求数据
   - 在页面已加载后从服务器接收数据
   - 在后台向服务器发送数据
   - 所有现代的浏览器都支持 XMLHttpRequest 对象(除ie6以外)。

#### XMLHttpRequest对象属性
##### readyState
表示XMLHttpRequest对象的状态：0：未初始化。对象已创建，未调用open；
1. open方法成功调用，但sendd方法未调用；
2. send方法已经调用，尚未开始接受数据；
3. 正在接受数据。Http响应头信息已经接受，但尚未接收完成；
4. 完成，即响应数据接受完成。

##### Onreadystatechange
请求状态改变的事件触发器（readyState变化时会调用这个属性上注册的javascript函数）。

##### responseText
服务器响应的文本内容

##### responseXML
服务器响应的XML内容对应的DOM对象

##### status
服务器返回的http状态码。200表示“成功”，404表示“未找到”，500表示“服务器内部错误”等。

##### statusText
服务器返回状态的文本信息。

#### XMLHttpRequest对象方法

| 方法|     说明 |
| :-------- | :--------|
| Open(string method,string url,boolean asynch,String username,string password)   |   指定和服务器端交的HTTP方法，URL地址，即其他请求信息；Method:表示http请求方法，一般使用"GET","POST".url：表示请求的服务器的地址；asynch：表示是否采用异步方法，true为异步，false为同步；后边两个可以不指定，username和password分别表示用户名和密码，提供http认证机制需要的用户名和密码。 | 
| Send(content)   |   向服务器发出请求，如果采用异步方式，该方法会立即返回。content可以指定为null表示不发送数据，其内容可以是DOM对象，输入流或字符串。 | 
| SetRequestHeader(String header,String Value)   |   设置HTTP请求中的指定头部header的值为value。此方法需在open方法以后调用，一般在post方式中使用。 | 
| getAllResponseHeaders()   |   返回包含Http的所有响应头信息，其中相应头包括Content-length,date,uri等内容。返回值是一个字符串，包含所有头信息，其中每个键名和键值用冒号分开，每一组键之间用CR和LF（回车加换行符）来分隔！ | 
| getResponseHeader(String header)   |   返回HTTP响应头中指定的键名header对应的值 | 
| Abort()   |   停止当前http请求。对应的XMLHttpRequest对象会复位到未初始化的状态。 | 

#### 实例

**get方式：**
```
var http = new XMLHttpRequest();
http.open("get","http://project.4399ued.com/feProject/js.html?id=123&name=aofei");
http.onreadystatechange = function(){
	if(http.readyState == 4){
		if(http.status == 200){
			console.log(http.responseText);
			//成功回调
		}else{
			//失败回调
		}
	}
	
};
http.send();
```
**post方式：**
```
var http = new XMLHttpRequest();
http.open("post","http://project.4399ued.com/feProject/js.html");
http.setRequestHeader("Content-Type","application/x-www-form-urlencoded"); 
http.onreadystatechange = function(){
	if(http.readyState == 4){
		if(http.status == 200){
			console.log(http.responseText);
			//成功回调
		}else{
			//失败回调
		}
	}
};
http.send("id=123&name=aofei");
```

---
#### ie6的处理方式
```
if (window.XMLHttpRequest) {
	// code for IE7+, Firefox, Chrome, Opera, Safari
    xmlHttpRequest = new XMLHttpRequest();
} else if (window.ActiveXObject) {
    // code for IE6, IE5
    xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
} else {
    //very rare browsers, can be ignored.
}
```
---
## jsonp

#### 为何而生

>**同源策略**
>定义：*同源策略（Same origin policy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。*
>作用：*基于安全的原因，浏览器是存在同源策略这个机制的，同源策略阻止从一个源加载的文档或脚本获取或设置另一个源加载的文档的属性。*

如果强行向请求非本域的地址发送请求，将会出现`（not allowed by Access-Control-Allow-Origin）`的错误，这样的请求是不被允许的，并不能收到所期望的数据，就是所谓的跨域。

#### 原理
>javascript标签具有跨域能力，所以很自然的就有人想到用js标签来解决跨域的问题。

#### 实例
**链接：**
```
http://www.4399youpai.com/tv-hot.html?r=m&_callback_=jQuery19000736121055438217_1487918665247&_=1487918665248
```
**内容：**
```
jQuery19000736121055438217_1487918665247({"code":100,"result":{"data":[{"push_id":114601,"uid":2165278937,"author":"4399\u5154\u53fd","authorImg":"http:\/\/a.img4399.com\/2165278937\/middle","room_id":10217,"game_id":99099,"game_name":"\u602a\u517d\u5927\u4f5c\u6218","title":"\u5b9e\u529b\u7cbe\u5206\uff1a\u4f60\u6c38\u8fdc\u4e0d\u8ba4\u8bc6\u4e0b\u4e00\u79d2\u7684\u5154\u5b50","game_logo":"http:\/\/f1.img4399.com\/box~cp\/2732016\/08\/30\/15_R_MMab.124x124.jpg~250x250","logo":"http:\/\/f1.img4399.com\/youpai~tv\/2017\/02\/24\/14_A5e84e.1280x720.jpg~480x480","play_nums":824,"online_nums":345,"url":"http:\/\/www.4399youpai.com\/mobile\/tv\/10217.html","status":1},{"push_id":114404,"uid":2164858251,"author":"4399\u5927\u9ed1\u864e","authorImg":"http:\/\/a.img4399.com\/2164858251\/middle","room_id":10225,"game_id":85014,"game_name":"\u738b\u8005\u8363\u8000","title":"\u5f00\u95e8\uff01\u9ed1\u864e\u961f~","game_logo":"http:\/\/f1.img4399.com\/box~cp\/273\/2015\/10\/30\/10\/5945_CHR0u4.jpg~250x250","logo":"http:\/\/f1.img4399.com\/youpai~tv\/2017\/02\/24\/14_BpwFE5.1280x720.jpg~480x480","play_nums":4182,"online_nums":1315,"url":"http:\/\/www.4399youpai.com\/mobile\/tv\/10225.html","status":1}]},"message":"message"});
```
**代码：**
```
/*成功回调*/
function successCallback(data){
	console.log(data);
}

/*失败回调*/
function errorCallback(){
	
}

var script = document.createElement("script");
script.type = "text/javascript";
document.body.appendChild(script);

script.onerror = function(){
	errorCallback();
};

script.src = "http://www.4399youpai.com/tv-hot.html?r=m&_callback_=successCallback";
```

这样就实现了一次跨域的数据访问，就是这么神奇\(^o^)/~


## 结语
现在网络上有很多开源库和组件供大家下载使用，非常便利，固然大家可以借由它们的帮助完成自己的工作，但这样就满足了吗，知其然而不知其所以然，对自己能力是一个很大的束缚，去探究其根本，清楚每一个脉络，了然于心，从而体会到其中的乐趣，相信这会是一个良性循环，未知事物并不可怕，可怕的是不敢接受，共勉。



