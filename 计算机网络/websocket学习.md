# Websocket基本学习及基础使用

## 1.WebSocket是什么？

WebSocket 是一种在单个TCP连接上进行全双工通信的协议。WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据，可以双方双向的平等对话，属于服务器的推送技术的一种

## 2.为什么要用WebSocket?

在HTTP协议中，我们可以通过客户端发起通信，但是**服务器不能给客户端推送信息**

单向的请求无法感知服务器的变化，如果服务器连续变化，我们只能进行**轮询**，隔一段时间发起请求，效率低且浪费资源

### （1）http短轮询

浏览器向服务器请求，看有没有数据更新，每隔一段时间请求一次，无论更没更新都响应

![image-20211214165606395](image/image-20211214165606395.png)

优点：

- 前后端编写容易

缺点：

* 不断发送请求，对服务器压力比较大，开启HTTP连接时一件比较消耗资源的事情
* 轮询时间不好控制，实时性比较高的话，就无法及时获得消息

### （2）http长轮询

页面发起一个到服务器的请求，如果服务端数据更新就返回，如果没有更新，到达时间限制就返回，客户端处理完返回信息之后再次发起请求

![image-20211214170310323](image/image-20211214170310323.png)

优点：

- 不会频繁请求，建立HTTP减少，消耗资源变少

缺点：

- 同样导致资源浪费，如果有一百个人请求，那就有一百个请求挂起

**无论是哪种轮询，都不太适用于客户端数量太多的情况，因为每个服务器所能承载的TCP连接是有限的的，很容易连满**

### （3）SSE(Server-Sent Events)

SSE是服务端给客户端主动推送消息的技术，本质是HTTP（TCP）的长连接，客户端不会关闭连接，会一直等待发送过来的数据

![image-20211221225135784](image/image-20211221225135784.png)

优点：

- 使用简单，websocket相对更复杂一点
- 默认支持断线重连
- SSE 使用 HTTP 协议，现有的服务器软件都支持

缺点：

- 一般用来传输文本，二进制数据需要编码后传送
- 单向通道，只能服务器向浏览器发送

### （3）websocket

HTTP做不到服务器向浏览器推送数据，所以有了websocket，**HTTP2.0具有服务器推送功能，但是只能推送静态资源**

当建立连接之后，服务器可以向浏览器发送信息，浏览器也可以向服务器发送信息，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。

特点：

（1）建立在 TCP 协议之上，双向通信，减少轮询时候HTTP连接数量的开销。

（2）与 HTTP 兼容性良好。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）没有同源限制，客户端可以与任意服务器通信，完全可以取代 Ajax。

（6）协议标识符是`ws`（如果加密，则为`wss`，对应 HTTPS 协议），服务器网址就是 URL

### （4）长连接和短连接

短连接：客户端服务器每进行一次HTTP操作，就建立一次连接，**HTTP/1.0中默认使用短连接**

长连接：`Connection:keep-alive`保持TCP连接,一段时间，**HTTP/1.1起默认使用长连接**

## 3.websocket API

整个流程就是建立连接—>发送数据--->接收数据--->处理错误--->断开连接

```js
var ws = new WebSocket("wss://...");//新建实例 建立连接

ws.onopen = function(evt) {// 连接成功
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");//发送数据
};

ws.onmessage = function(evt) {//收到数据
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {//关闭连接
  console.log("Connection closed.");
};
```

[查看方法和websocket兼容性](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

#### （1）readyState属性

ws.readyState返回实例对象的状态

```
0：表示正在连接
1：连接成功
2：连接正在关闭
3：连接已经关闭或者打开连接失败
```

#### （2）onopen、onmessage、onerror、onclose属性

```js
// onopen：连接成功之后的操作
ws.onpen = function () {
	ws.send()// 向服务器发送数据
}
var file = document.querySelector('input[type="file"]').files[0];
ws.send(file);
// onclose： 连接关闭之后的操作
ws.onclose = function(event) {
  console.log(event);
};
// onmessage：收到服务器数据之后的操作
ws.onmessage = function(event) { // 服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）
  var data = event.data;
  // 处理数据
};
// onerror：连接出错的操作
socket.onerror = function(event) {
  // handle error event
};
```

## 4.websocket封装

### （1）封装一：

```js
//wsDemo.js
class wsClient {
	constructor(url) {//构造函数
		this.ws = new WebSocket(url);
		this.eventList = {};
		this.ws.onmessage = (e) => {
			this._handleOnMessage(e);
		};
		this.ws.onerror = (e) => { 
			this._handleOnError(e);
		};
	}

	addEventListener(type, callback) {//添加事件监听
		if (this.eventList[type] === undefined) {
			this.eventList[type]= [callback];
		} else {
			this.eventList[type].push(callback);
		}
	}

	sendMessage(data) {//发送数据
		if (this.ws.readyState === 1) {
			this.ws.send(JSON.stringify(data));
		}else if (this.ws !== null && this.ws.readyState === 3) {
			this.ws.close();
		}
	}

	removeEventListener(type, callback) {// 移出监听
		if (this.eventList[type] && this.eventList[type].indexOf(callback)) {
			this.eventList[type].splice(this.eventList[type].indexOf(callback), 1);
		}
	}

	_handleOnMessage(messageEvent) {// 数据放到监听函数中
		if (this.eventList && this.eventList["message"]) {
			for (let i in this.eventList["message"]) {
				this.eventList["message"][i](JSON.parse(messageEvent.data));
			}
		}
	}

	_handleOnError(error) {
		if (this.eventList && this.eventList["error"]) {
			for (let i in this.eventList["error"]) {
				this.eventList["error"][i](error);
			}
		}
	}
	close() {
		this.ws.close();
	}
}

let WS_CLIENT_POOL = {};

export function Factory(url) {//工厂函数
	if (WS_CLIENT_POOL[url] === undefined) {
		let ws_client = new wsClient(url);
		WS_CLIENT_POOL[url] = ws_client;
	}
	return WS_CLIENT_POOL[url];
}

// 使用封装数据
import { Factory } from '../utils/wsDemo';
let ws_client = Factory("xxxxx");
beforeDestroy() {// 删除监听
	ws_client.removeEventListener("message",this.getSocketData);
},
ws_client.sendMessage({
    //send给后端的数据
});
ws_client.addEventListener("message", this.getSocketData);// 添加监听获得数据
getScocketData(res){
    console.log(res);//onmessage返回的数据
}
```

### （2）封装二

```js
//websocket.js
let WS = "";

// 创建连接
export const createWS = function (params,callback) {
	if (!WS) {
		WS = new WebSocket("XXXXX");
	} else {
		console.log("websocket connected ...");
	}
	WS.onopen = initWS(params);//创建连接之后发送数据
	WS.onmessage = function (msg) { //接收后端数据
		if (callback) {
			callback(msg);
		}
	};
	WS.onerror = onerrorWS;
	WS.onclose = oncloseWS;
	console.log("websocket connected ...");
};
//  单独发送数据方法
export const sendMessage = function (data) {
	if (WS !== null && WS.readyState === 3) {
		WS.close();
		createWS();
	} else if (WS.readyState === 1) {
		WS.send(JSON.stringify(data));
	}
};
// 建立连接之后发送消息
function initWS(params) {
    if (WS !== null && WS.readyState === 3) {
        WS.close();
        createWS();
    } else if (WS.readyState === 1) {
        WS.send(params);
    }
    console.log("websocket open...");
}
//发生错误时处理
function onerrorWS() {
	WS.close();
	if (WS.readyState !== 3) {
		WS = null;
		createWS();
	}
}
//关闭连接
function oncloseWS() {
	console.log("websocket closed...");
}

// 使用方法
import { createWS } from "../utils/websocket.js"
createWS(JSON.stringify({
       //发送的数据
}),function(e){
     let res = JSON.parse(e.data);
     console.log(res); //后端返回的数据
}
```

## 5.参考资料

1.[WebSocket 原理及单例组件封装](https://juejin.cn/post/7020344512903905316)

2.[前端webSocket实现聊天消息&心跳检测&断线重连](https://juejin.cn/post/6914113426436390926)

3.[阮一峰websocket教程](https://www.ruanyifeng.com/blog/2017/05/websocket.html)

