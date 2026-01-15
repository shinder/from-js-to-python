# 第 10 章：非同步程式設計

本章節詳細說明 Python 的非同步程式設計，包含 async/await 語法、asyncio 模組，以及與 JavaScript 非同步模型的差異。

---

## 10.1 非同步基礎概念

### 同步 vs 非同步

```python
# 同步程式碼：依序執行，一個完成才執行下一個
import time

def sync_task(name, seconds):
    print(f"{name} 開始")
    time.sleep(seconds)  # 阻塞整個程式
    print(f"{name} 完成")

# 執行三個任務需要 6 秒
sync_task("任務1", 2)
sync_task("任務2", 2)
sync_task("任務3", 2)

# 非同步程式碼：可以在等待時執行其他任務
import asyncio

async def async_task(name, seconds):
    print(f"{name} 開始")
    await asyncio.sleep(seconds)  # 非阻塞等待
    print(f"{name} 完成")

# 執行三個任務只需要 2 秒（並行執行）
async def main():
    await asyncio.gather(
        async_task("任務1", 2),
        async_task("任務2", 2),
        async_task("任務3", 2),
    )

asyncio.run(main())
```

### JavaScript vs Python 非同步模型

| 特性       | JavaScript                  | Python                  |
| ---------- | --------------------------- | ----------------------- |
| 執行環境   | 單執行緒 + 事件迴圈（內建） | 需要明確建立事件迴圈    |
| 啟動方式   | 自動執行                    | `asyncio.run()`         |
| Promise    | 內建 `Promise`              | `asyncio.Future`        |
| async 函式 | 回傳 `Promise`              | 回傳 `Coroutine`        |
| 頂層 await | 支援（ES2022+）             | 僅在 `asyncio.run()` 內 |

---

## 10.2 async/await 語法

### 定義協程函式（Coroutine）

```python
import asyncio

# 使用 async def 定義協程函式
async def fetch_data(url):
    print(f"開始取得 {url}")
    await asyncio.sleep(1)  # 模擬網路請求
    print(f"完成取得 {url}")
    return {"url": url, "data": "some data"}

# 呼叫協程函式會回傳協程物件，不會立即執行
coro = fetch_data("https://example.com")
print(type(coro))  # <class 'coroutine'>

# 必須使用 await 或 asyncio.run() 來執行
async def main():
    result = await fetch_data("https://example.com")
    print(result)

asyncio.run(main())
```

### JavaScript 對照

```javascript
// JavaScript
async function fetchData(url) {
    console.log(`開始取得 ${url}`);
    await new Promise(resolve => setTimeout(resolve, 1000));
    console.log(`完成取得 ${url}`);
    return { url, data: "some data" };
}

// 可以直接呼叫，回傳 Promise
fetchData("https://example.com").then(console.log);

// 或在 async 函式中使用 await
async function main() {
    const result = await fetchData("https://example.com");
    console.log(result);
}
main();
```

```python
# Python
import asyncio

async def fetch_data(url):
    print(f"開始取得 {url}")
    await asyncio.sleep(1)
    print(f"完成取得 {url}")
    return {"url": url, "data": "some data"}

# 必須透過 asyncio.run() 執行
async def main():
    result = await fetch_data("https://example.com")
    print(result)

asyncio.run(main())
```

### await 關鍵字

```python
import asyncio

async def get_user(user_id):
    await asyncio.sleep(0.5)
    return {"id": user_id, "name": f"User {user_id}"}

async def get_posts(user_id):
    await asyncio.sleep(0.5)
    return [{"id": 1, "title": "Post 1"}, {"id": 2, "title": "Post 2"}]

async def main():
    # await 會暫停執行直到協程完成
    user = await get_user(1)
    print(f"使用者：{user}")

    posts = await get_posts(1)
    print(f"文章：{posts}")

asyncio.run(main())

# 注意：await 只能在 async 函式內使用
# def regular_function():
#     await asyncio.sleep(1)  # SyntaxError!
```

### 可等待物件（Awaitables）

Python 中有三種可等待物件：

```python
import asyncio

# 1. 協程（Coroutine）
async def my_coroutine():
    return "coroutine result"

# 2. Task
async def main():
    # 建立 Task
    task = asyncio.create_task(my_coroutine())
    result = await task
    print(result)

# 3. Future
async def main():
    loop = asyncio.get_event_loop()
    future = loop.create_future()

    # 在某處設定結果
    future.set_result("future result")

    result = await future
    print(result)
```

---

## 10.3 asyncio 模組

### 執行協程

```python
import asyncio

async def main():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

# 方式 1：asyncio.run()（推薦，Python 3.7+）
asyncio.run(main())

# 方式 2：手動管理事件迴圈（較舊的方式）
loop = asyncio.get_event_loop()
try:
    loop.run_until_complete(main())
finally:
    loop.close()

# 方式 3：在已有事件迴圈中（如 Jupyter Notebook）
# await main()  # 或使用 nest_asyncio
```

### asyncio.gather() - 並行執行多個協程

```python
import asyncio

async def fetch_url(url, delay):
    print(f"開始：{url}")
    await asyncio.sleep(delay)
    print(f"完成：{url}")
    return f"{url} 的資料"

async def main():
    # gather 會並行執行所有協程
    results = await asyncio.gather(
        fetch_url("url1", 2),
        fetch_url("url2", 1),
        fetch_url("url3", 3),
    )
    print(f"所有結果：{results}")

asyncio.run(main())

# 輸出順序：
# 開始：url1
# 開始：url2
# 開始：url3
# 完成：url2（1秒後）
# 完成：url1（2秒後）
# 完成：url3（3秒後）
# 所有結果：['url1 的資料', 'url2 的資料', 'url3 的資料']

# 總時間約 3 秒（最長的那個），而非 6 秒

# JavaScript 對照
# const results = await Promise.all([
#     fetchUrl("url1", 2),
#     fetchUrl("url2", 1),
#     fetchUrl("url3", 3),
# ]);
```

### gather 的錯誤處理

```python
import asyncio

async def may_fail(name, should_fail=False):
    await asyncio.sleep(1)
    if should_fail:
        raise ValueError(f"{name} 失敗了")
    return f"{name} 成功"

async def main():
    # 預設：一個失敗會拋出例外
    try:
        results = await asyncio.gather(
            may_fail("任務1"),
            may_fail("任務2", should_fail=True),
            may_fail("任務3"),
        )
    except ValueError as e:
        print(f"錯誤：{e}")

    # return_exceptions=True：錯誤會作為結果回傳
    results = await asyncio.gather(
        may_fail("任務1"),
        may_fail("任務2", should_fail=True),
        may_fail("任務3"),
        return_exceptions=True
    )
    for result in results:
        if isinstance(result, Exception):
            print(f"錯誤：{result}")
        else:
            print(f"成功：{result}")

asyncio.run(main())
```

### asyncio.create_task() - 建立任務

```python
import asyncio

async def background_task(name, seconds):
    print(f"{name} 開始")
    await asyncio.sleep(seconds)
    print(f"{name} 完成")
    return f"{name} 的結果"

async def main():
    # create_task 會立即開始執行任務
    task1 = asyncio.create_task(background_task("任務1", 2))
    task2 = asyncio.create_task(background_task("任務2", 1))

    print("任務已建立，繼續做其他事...")

    # 等待任務完成
    result1 = await task1
    result2 = await task2

    print(f"結果：{result1}, {result2}")

asyncio.run(main())

# 差異：
# await coro() - 立即等待，完成後才繼續
# create_task(coro()) - 排程執行，可以稍後等待
```

### asyncio.wait() - 更細緻的控制

```python
import asyncio

async def task(name, seconds):
    await asyncio.sleep(seconds)
    return f"{name} 完成"

async def main():
    tasks = [
        asyncio.create_task(task("A", 1)),
        asyncio.create_task(task("B", 2)),
        asyncio.create_task(task("C", 3)),
    ]

    # 等待第一個完成
    done, pending = await asyncio.wait(
        tasks,
        return_when=asyncio.FIRST_COMPLETED
    )
    print(f"第一個完成：{[t.result() for t in done]}")
    print(f"還在執行：{len(pending)} 個")

    # 等待所有完成
    done, pending = await asyncio.wait(pending)
    print(f"全部完成：{[t.result() for t in done]}")

asyncio.run(main())

# return_when 選項：
# FIRST_COMPLETED - 第一個完成時回傳
# FIRST_EXCEPTION - 第一個例外時回傳
# ALL_COMPLETED - 全部完成時回傳（預設）
```

### asyncio.wait_for() - 設定超時

```python
import asyncio

async def slow_operation():
    await asyncio.sleep(10)
    return "完成"

async def main():
    try:
        # 設定 2 秒超時
        result = await asyncio.wait_for(
            slow_operation(),
            timeout=2.0
        )
        print(result)
    except asyncio.TimeoutError:
        print("操作超時！")

asyncio.run(main())

# JavaScript 對照
# const result = await Promise.race([
#     slowOperation(),
#     new Promise((_, reject) =>
#         setTimeout(() => reject(new Error('Timeout')), 2000)
#     )
# ]);
```

### asyncio.sleep() vs time.sleep()

```python
import asyncio
import time

async def demo():
    # 錯誤：time.sleep() 會阻塞整個事件迴圈
    # time.sleep(1)  # 不要在 async 函式中使用！

    # 正確：asyncio.sleep() 是非阻塞的
    await asyncio.sleep(1)

# time.sleep() 會阻塞所有協程
async def blocking_example():
    async def task(name):
        print(f"{name} 開始")
        time.sleep(1)  # 阻塞！
        print(f"{name} 完成")

    # 這會花 3 秒（依序執行）
    await asyncio.gather(
        task("A"),
        task("B"),
        task("C"),
    )

# asyncio.sleep() 不會阻塞
async def non_blocking_example():
    async def task(name):
        print(f"{name} 開始")
        await asyncio.sleep(1)  # 非阻塞
        print(f"{name} 完成")

    # 這只花 1 秒（並行執行）
    await asyncio.gather(
        task("A"),
        task("B"),
        task("C"),
    )
```

---

## 10.4 TaskGroup（Python 3.11+）

Python 3.11 引入了更好的任務管理方式：

```python
import asyncio

async def fetch(url):
    await asyncio.sleep(1)
    if "error" in url:
        raise ValueError(f"無法取得 {url}")
    return f"{url} 的資料"

async def main():
    # 使用 TaskGroup 管理多個任務
    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(fetch("url1"))
        task2 = tg.create_task(fetch("url2"))
        task3 = tg.create_task(fetch("url3"))

    # 離開 context manager 時，所有任務都已完成
    print(task1.result())
    print(task2.result())
    print(task3.result())

asyncio.run(main())

# TaskGroup vs gather 的差異：
# 1. TaskGroup 使用 context manager 語法
# 2. 任何任務失敗會取消其他任務
# 3. 多個例外會合併為 ExceptionGroup
```

### 處理 TaskGroup 的例外

```python
import asyncio

async def task(name, should_fail=False):
    await asyncio.sleep(1)
    if should_fail:
        raise ValueError(f"{name} 失敗")
    return f"{name} 成功"

async def main():
    try:
        async with asyncio.TaskGroup() as tg:
            tg.create_task(task("A"))
            tg.create_task(task("B", should_fail=True))
            tg.create_task(task("C", should_fail=True))
    except* ValueError as eg:
        # Python 3.11+ 的 except* 語法處理 ExceptionGroup
        for exc in eg.exceptions:
            print(f"錯誤：{exc}")

asyncio.run(main())
```

---

## 10.5 非同步迭代器與生成器

### 非同步迭代器

```python
import asyncio

class AsyncRange:
    """非同步的 range"""

    def __init__(self, stop):
        self.stop = stop
        self.current = 0

    def __aiter__(self):
        return self

    async def __anext__(self):
        if self.current >= self.stop:
            raise StopAsyncIteration
        await asyncio.sleep(0.1)  # 模擬非同步操作
        value = self.current
        self.current += 1
        return value

async def main():
    # 使用 async for 迭代
    async for num in AsyncRange(5):
        print(num)

asyncio.run(main())
```

### 非同步生成器

```python
import asyncio

async def async_generator(n):
    """非同步生成器"""
    for i in range(n):
        await asyncio.sleep(0.1)
        yield i

async def main():
    # 使用 async for 迭代
    async for num in async_generator(5):
        print(num)

    # 使用非同步列表推導式（Python 3.6+）
    results = [num async for num in async_generator(5)]
    print(results)

asyncio.run(main())
```

### 非同步 Context Manager

```python
import asyncio

class AsyncResource:
    """非同步資源管理"""

    async def __aenter__(self):
        print("取得資源...")
        await asyncio.sleep(0.1)
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("釋放資源...")
        await asyncio.sleep(0.1)

    async def do_something(self):
        print("使用資源")

async def main():
    # 使用 async with
    async with AsyncResource() as resource:
        await resource.do_something()

asyncio.run(main())

# 輸出：
# 取得資源...
# 使用資源
# 釋放資源...
```

---

## 10.6 實際應用範例

### HTTP 請求（使用 aiohttp）

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    """非同步取得 URL 內容"""
    async with session.get(url) as response:
        return await response.text()

async def main():
    urls = [
        "https://httpbin.org/get",
        "https://httpbin.org/ip",
        "https://httpbin.org/headers",
    ]

    async with aiohttp.ClientSession() as session:
        # 並行請求所有 URL
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)

        for url, result in zip(urls, results):
            print(f"{url}: {len(result)} bytes")

# 需要安裝：pip install aiohttp
# asyncio.run(main())
```

### 檔案操作（使用 aiofiles）

```python
import asyncio
import aiofiles

async def read_file(filename):
    """非同步讀取檔案"""
    async with aiofiles.open(filename, 'r') as f:
        return await f.read()

async def write_file(filename, content):
    """非同步寫入檔案"""
    async with aiofiles.open(filename, 'w') as f:
        await f.write(content)

async def main():
    # 並行讀取多個檔案
    files = ["file1.txt", "file2.txt", "file3.txt"]
    tasks = [read_file(f) for f in files]
    contents = await asyncio.gather(*tasks, return_exceptions=True)

    for filename, content in zip(files, contents):
        if isinstance(content, Exception):
            print(f"{filename}: 錯誤 - {content}")
        else:
            print(f"{filename}: {len(content)} 字元")

# 需要安裝：pip install aiofiles
# asyncio.run(main())
```

### 生產者-消費者模式

```python
import asyncio
import random

async def producer(queue, name):
    """生產者：產生資料放入佇列"""
    for i in range(5):
        item = f"{name}-item-{i}"
        await queue.put(item)
        print(f"生產：{item}")
        await asyncio.sleep(random.uniform(0.1, 0.5))
    await queue.put(None)  # 結束信號

async def consumer(queue, name):
    """消費者：從佇列取出並處理資料"""
    while True:
        item = await queue.get()
        if item is None:
            break
        print(f"{name} 處理：{item}")
        await asyncio.sleep(random.uniform(0.1, 0.3))
        queue.task_done()

async def main():
    queue = asyncio.Queue()

    # 建立生產者和消費者
    producers = [
        asyncio.create_task(producer(queue, f"P{i}"))
        for i in range(2)
    ]
    consumers = [
        asyncio.create_task(consumer(queue, f"C{i}"))
        for i in range(3)
    ]

    # 等待生產者完成
    await asyncio.gather(*producers)

    # 發送結束信號給所有消費者
    for _ in consumers:
        await queue.put(None)

    # 等待消費者完成
    await asyncio.gather(*consumers)

asyncio.run(main())
```

### 限制並發數量

```python
import asyncio

async def fetch_with_semaphore(semaphore, url):
    """使用 Semaphore 限制並發"""
    async with semaphore:
        print(f"開始：{url}")
        await asyncio.sleep(1)  # 模擬請求
        print(f"完成：{url}")
        return f"{url} 的資料"

async def main():
    # 限制同時最多 3 個並發
    semaphore = asyncio.Semaphore(3)

    urls = [f"url{i}" for i in range(10)]
    tasks = [fetch_with_semaphore(semaphore, url) for url in urls]
    results = await asyncio.gather(*tasks)
    print(f"全部完成：{len(results)} 個結果")

asyncio.run(main())
```

### 超時與取消

```python
import asyncio

async def long_running_task():
    """長時間執行的任務"""
    try:
        print("任務開始")
        await asyncio.sleep(10)
        print("任務完成")
        return "結果"
    except asyncio.CancelledError:
        print("任務被取消")
        raise  # 重新拋出，讓呼叫者知道任務被取消

async def main():
    # 方式 1：使用 wait_for 設定超時
    try:
        result = await asyncio.wait_for(
            long_running_task(),
            timeout=2.0
        )
    except asyncio.TimeoutError:
        print("超時！")

    # 方式 2：手動取消任務
    task = asyncio.create_task(long_running_task())

    await asyncio.sleep(1)
    task.cancel()

    try:
        await task
    except asyncio.CancelledError:
        print("任務已被取消")

asyncio.run(main())
```

---

## 10.7 與同步程式碼整合

### 在 async 中執行同步程式碼

```python
import asyncio
import time

def blocking_io():
    """模擬阻塞的 I/O 操作"""
    time.sleep(2)
    return "結果"

def cpu_intensive():
    """模擬 CPU 密集運算"""
    total = sum(i * i for i in range(10**7))
    return total

async def main():
    loop = asyncio.get_event_loop()

    # 在執行緒池中執行阻塞 I/O
    result = await loop.run_in_executor(None, blocking_io)
    print(f"IO 結果：{result}")

    # 在程序池中執行 CPU 密集運算
    from concurrent.futures import ProcessPoolExecutor
    with ProcessPoolExecutor() as pool:
        result = await loop.run_in_executor(pool, cpu_intensive)
        print(f"CPU 結果：{result}")

asyncio.run(main())
```

### 在同步程式碼中執行 async

```python
import asyncio

async def async_function():
    await asyncio.sleep(1)
    return "非同步結果"

# 方式 1：使用 asyncio.run()
def sync_wrapper():
    result = asyncio.run(async_function())
    return result

# 方式 2：取得現有事件迴圈（在已有迴圈的環境中）
def sync_wrapper_in_loop():
    loop = asyncio.get_event_loop()
    if loop.is_running():
        # 在執行中的迴圈裡，建立新任務
        import concurrent.futures
        future = concurrent.futures.Future()

        async def wrapper():
            try:
                result = await async_function()
                future.set_result(result)
            except Exception as e:
                future.set_exception(e)

        asyncio.ensure_future(wrapper())
        return future.result()
    else:
        return loop.run_until_complete(async_function())
```

---

## 10.8 常見問題與陷阱

### 1. 忘記 await

```python
async def get_data():
    await asyncio.sleep(1)
    return "資料"

async def main():
    # 錯誤：忘記 await
    data = get_data()  # 這回傳協程物件，不是結果！
    print(data)  # <coroutine object get_data at 0x...>

    # 正確
    data = await get_data()
    print(data)  # "資料"

# Python 會顯示警告：
# RuntimeWarning: coroutine 'get_data' was never awaited
```

### 2. 在同步函式中使用 await

```python
# 錯誤：await 只能在 async 函式中使用
def sync_function():
    await asyncio.sleep(1)  # SyntaxError!

# 正確
async def async_function():
    await asyncio.sleep(1)
```

### 3. 使用阻塞操作

```python
import time
import asyncio

async def bad_example():
    # 錯誤：time.sleep 會阻塞事件迴圈
    time.sleep(1)

async def good_example():
    # 正確：使用 asyncio.sleep
    await asyncio.sleep(1)

# 如果必須使用阻塞操作，放到執行緒池
async def better_example():
    loop = asyncio.get_event_loop()
    await loop.run_in_executor(None, time.sleep, 1)
```

### 4. 沒有正確處理例外

```python
async def risky_task():
    await asyncio.sleep(1)
    raise ValueError("出錯了")

async def main():
    # 危險：任務的例外可能被忽略
    task = asyncio.create_task(risky_task())
    await asyncio.sleep(2)
    # 如果不 await task，例外可能不會被注意到

    # 正確：總是 await 任務或使用 try-except
    try:
        await task
    except ValueError as e:
        print(f"捕捉到錯誤：{e}")
```

### 5. 事件迴圈已關閉

```python
import asyncio

async def my_coro():
    return "結果"

# 錯誤：多次呼叫 asyncio.run()
asyncio.run(my_coro())
# asyncio.run(my_coro())  # 可能會出問題

# 正確：在一個 run() 中執行所有協程
async def main():
    result1 = await my_coro()
    result2 = await my_coro()
    return result1, result2

asyncio.run(main())
```

---

## 練習題

### 練習 1：並行下載

模擬並行下載多個檔案：

```python
async def download_file(filename, size):
    """
    模擬下載檔案

    Args:
        filename: 檔案名稱
        size: 檔案大小（MB），影響下載時間

    Returns:
        dict: {"filename": filename, "size": size, "status": "completed"}
    """
    # 你的程式碼
    pass

async def main():
    files = [
        ("file1.zip", 10),
        ("file2.zip", 5),
        ("file3.zip", 15),
        ("file4.zip", 3),
    ]

    # 並行下載所有檔案
    results = # 你的程式碼

    for result in results:
        print(result)

# 預期：總時間約 15 秒（最大的檔案），而非 33 秒（加總）
```

### 練習 2：限流器

實作一個限制每秒請求數的裝飾器：

```python
def rate_limit(calls_per_second):
    """
    限制每秒呼叫次數的裝飾器

    @rate_limit(5)  # 每秒最多 5 次
    async def api_call():
        ...
    """
    # 你的程式碼
    pass
```

### 練習 3：非同步快取

實作一個非同步快取裝飾器：

```python
def async_cache(ttl=60):
    """
    非同步函式的快取裝飾器

    @async_cache(ttl=30)
    async def fetch_data(key):
        ...
    """
    # 你的程式碼
    pass
```

---

## 小結

### JavaScript vs Python 非同步對照表

| 功能     | JavaScript             | Python                          |
| -------- | ---------------------- | ------------------------------- |
| 定義     | `async function`       | `async def`                     |
| 等待     | `await`                | `await`                         |
| 執行     | 自動 / `.then()`       | `asyncio.run()`                 |
| 並行執行 | `Promise.all()`        | `asyncio.gather()`              |
| 競速     | `Promise.race()`       | `asyncio.wait(FIRST_COMPLETED)` |
| 任一完成 | `Promise.any()`        | 需自行實作                      |
| 超時     | 手動實作               | `asyncio.wait_for()`            |
| 建立任務 | 自動                   | `asyncio.create_task()`         |
| 延遲     | `setTimeout` + Promise | `asyncio.sleep()`               |
| 佇列     | 無內建                 | `asyncio.Queue`                 |
| 信號量   | 無內建                 | `asyncio.Semaphore`             |

### asyncio 常用 API

| API                               | 用途               |
| --------------------------------- | ------------------ |
| `asyncio.run(coro)`               | 執行協程           |
| `asyncio.create_task(coro)`       | 建立任務           |
| `asyncio.gather(*coros)`          | 並行執行多個協程   |
| `asyncio.wait(tasks)`             | 等待任務完成       |
| `asyncio.wait_for(coro, timeout)` | 帶超時的等待       |
| `asyncio.sleep(seconds)`          | 非同步延遲         |
| `asyncio.Queue()`                 | 非同步佇列         |
| `asyncio.Semaphore(n)`            | 信號量（限制並發） |
| `asyncio.Lock()`                  | 非同步鎖           |
| `asyncio.Event()`                 | 非同步事件         |

### 關鍵差異提醒

1. **Python 需要明確啟動事件迴圈**（`asyncio.run()`）
2. **await 只能在 async 函式內使用**
3. **不要使用 `time.sleep()`**，使用 `asyncio.sleep()`
4. **注意阻塞操作**，使用 `run_in_executor()` 處理
5. **記得處理任務的例外**
6. **使用 `asyncio.gather()` 並行執行**，不要依序 await
