# Python的协程(Coroutine)

Ref1: https://docs.python.org/zh-cn/3/library/asyncio-task.html
Ref2: https://huoyingwhw.com/pythonGuide/python%E8%BF%9B%E9%98%B6/asyncio/


# 关键字
`async` `await` `aiofiles` `asyncio` `aiohttp`

# 案例-客户端同时发起多个请求

C-S架构中, 客户端应用协程同时发起请求

```
# file: server.py

from typing import Union
from fastapi import FastAPI
import uvicorn
import time

app = FastAPI()

@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    time.sleep(5)
    return {"item_id": item_id, "q": q}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8881)
```

```
# file: client.py
# 异步http, 应用协程进行高并发

import aiohttp
import asyncio
import time

async def get(i):
    async with aiohttp.ClientSession() as session:
        print("curl --------")
        async with session.get('http://127.0.0.1:8881/items/{}'.format(i)) as response:
            print("Status:", response.status)
            print("Content-type:", response.headers['content-type'])
            html = await response.text()
            print("Body:", html[:15], "...")

async def main():
    await asyncio.gather(get(1), get(2), get(3)) # 启动3个get请求, 共耗时大约5s

startTime = time.time()
asyncio.run(main()) # 该函数阻塞, 直到3个get请求都返回
endTime = time.time()
print("总耗时: ", endTime - startTime)

```
