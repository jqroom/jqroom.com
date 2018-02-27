---
layout: post
title:  "浅尝websocket"
date:   2017-07-18 10:52
categories: techstack
permalink: /techstack/js/20170718/websocket
---

WebSocket一种在单个 TCP 连接上进行全双工通讯的协议。WebSocket通信协议于2011年被IETF定为标准RFC 6455，并被RFC7936所补充规范。WebSocket API也被W3C定为标准。
WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

> Websocket使用ws或wss的统一资源标志符，类似于HTTPS，其中wss表示在TLS之上的Websocket。如：

    ws://example.com/wsapi
    wss://secure.example.com/

> Websocket使用和 HTTP 相同的 TCP 端口，可以绕过大多数防火墙的限制。默认情况下，Websocket协议使用80端口；运行在TLS之上时，默认使用443端口。

## 动手实践

安装依赖：

    npm install --save ws

server.js

```js
const WebSocket = require('ws');
const wss = new WebSocket.Server({
    port: 1991,
    verifyClient: () => {
        return true;
    }
});

wss.on('connection', (ws, req) => {
    console.log('WebSocket 已连接');
    ws.onmessage = (msg) => {
        console.log(`server接受到数据: ${msg.data}`);
        ws.send('hello');
    };
});
```

client.js

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>tank war</title>
    </head>
    <body>
        <script type="text/javascript">
            function connect(){
                if('WebSocket' in window){
                    // 打开一个 web socket
                    var ws = new WebSocket("ws://localhost:1991");
                    ws.onopen = () => {
                        ws.send('我连接上了');
                    }

                    ws.onmessage = (msg) => {
                        console.log(`client: ${msg.data}`);
                    }

                    ws.onclose = () => {
                        console.log('连接已经关闭');
                    }

                    ws.onerror = (err) => {
                        console.log(`failed: ${JSON.stringify(err)}`);
                    }
                }else{
                    alert("您的浏览器不支持 WebSocket!");
                }
            }
        </script>
        <button type="button" onclick="connect()">连接服务器</button>
    </body>
</html>
```

## 实验结果截图：

![websocket1.png](https://i.loli.net/2017/07/18/596d7660353b7.png)

![websocket2.png](https://i.loli.net/2017/07/18/596d766034939.png)
