---
title: http 500 不一定是后端应用报出来的错，有可能是nginx报出来的
date: 2018-11-08 17:53:56
tags:
    - nginx
---


模拟出现的问题

nginx 出现的日志

```
2018/11/13 19:41:50 [alert] 2085#0: *5 8 worker_connections are not enough while connecting to upstream, client: 192.168.56.1, server: 192.168.56.102, request: "GET /api/ws HTTP/1.1", upstream: "http://192.168.56.1:8765/api/ws", host: "192.168.56.102:8090"
2018/11/13 19:41:52 [alert] 2085#0: *6 8 worker_connections are not enough while connecting to upstream, client: 192.168.56.1, server: 192.168.56.102, request: "GET /api/ws HTTP/1.1", upstream: "http://192.168.56.1:8765/api/ws", host: "192.168.56.102:8090"
2018/11/13 19:41:54 [alert] 2085#0: *7 8 worker_connections are not enough while connecting to upstream, client: 192.168.56.1, server: 192.168.56.102, request: "GET /api/ws HTTP/1.1", upstream: "http://192.168.56.1:8765/api/ws", host: "192.168.56.102:8090"
2018/11/13 19:41:55 [alert] 2085#0: *8 8 worker_connections are not enough while connecting to upstream, client: 192.168.56.1, server: 192.168.56.102, request: "GET /api/ws HTTP/1.1", upstream: "http://192.168.56.1:8765/api/ws", host: "192.168.56.102:8090"
2018/11/13 19:41:57 [alert] 2085#0: *9 8 worker_connections are not enough while connecting to upstream, client: 192.168.56.1, server: 192.168.56.102, request: "GET /api/ws HTTP/1.1", upstream: "http://192.168.56.1:8765/api/ws", host: "192.168.56.102:8090"
```

看日志是worker_connections are not enough while connecting to upstream ,连接数还不够，意思就是worker_connections设置小了


我为了测试下这个错误，把worker_connections值设小了

```
events {
    worker_connections  8;
}
```

nginx 配置


```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
  }

  upstream websocket {
    server 192.168.56.1:8765;
  }



  server {
    listen       8090;

    location /api/ws {
      proxy_pass http://websocket;
      proxy_read_timeout 300s;

      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
		}
	}
```


websocket 服务端脚本

```
#!/usr/bin/env python

# WS server example

import asyncio
import websockets

async def hello(websocket, path):
    name = await websocket.recv()
    print(f"< {name}")

    greeting = f"Hello {name}!"

    await websocket.send(greeting)
    print(f"> {greeting}")

start_server = websockets.serve(hello, '0.0.0.0', 8765)

asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()
```


websocket 客户端脚本

```
import asyncio
import websockets

async def hello():
    async with websockets.connect(
            'ws://192.168.56.102:8090/api/ws') as websocket:
        name = input("What's your name? ")

        await websocket.send(name)
        print(f"> {name}")

        greeting = await websocket.recv()
        print(f"< {greeting}")

asyncio.get_event_loop().run_until_complete(hello())
```


当客户端连接正常的时候

```
$ python client.py
What's your name? test
> test
< Hello test!

```


当连接数超限制，客户端连不上报下面的错误,nginx 也就会报上面提出的错误了

```
$ python client.py
Traceback (most recent call last):
  File "client.py", line 15, in <module>
    asyncio.get_event_loop().run_until_complete(hello())
  File "/usr/local/Cellar/python3/3.6.1/Frameworks/Python.framework/Versions/3.6/lib/python3.6/asyncio/base_events.py", line 466, in run_until_complete
    return future.result()
  File "client.py", line 6, in hello
    'ws://192.168.56.102:8090/api/ws') as websocket:
  File "/Users/jiangyd/python3_zy/lib/python3.6/site-packages/websockets/py35/client.py", line 2, in __aenter__
    return await self
  File "/Users/jiangyd/python3_zy/lib/python3.6/site-packages/websockets/py35/client.py", line 20, in __await_impl__
    extra_headers=protocol.extra_headers,
  File "/Users/jiangyd/python3_zy/lib/python3.6/site-packages/websockets/client.py", line 286, in handshake
    raise InvalidStatusCode(status_code)
websockets.exceptions.InvalidStatusCode: Status code not 101: 500
```


https://websockets.readthedocs.io/en/stable/intro.html   python websocket 参考
