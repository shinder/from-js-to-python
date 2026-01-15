# 第 12 章：裝飾器（Decorator）

本章節詳細說明 Python 的裝飾器，這是一種強大的語法糖，用於修改或增強函式和類別的行為。

---

## 12.1 裝飾器基礎

### 什麼是裝飾器？

裝飾器是一個接受函式作為參數並回傳新函式的函式。它允許在不修改原函式程式碼的情況下，增加額外的功能。

```python
# 裝飾器本質上是一個高階函式
def my_decorator(func):
    def wrapper():
        print("函式執行前")
        func()
        print("函式執行後")
    return wrapper

# 手動應用裝飾器
def say_hello():
    print("Hello!")

say_hello = my_decorator(say_hello)
say_hello()
# 輸出：
# 函式執行前
# Hello!
# 函式執行後

# 使用 @ 語法糖（等價於上面的寫法）
@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```

### JavaScript 對照

JavaScript 沒有內建的裝飾器語法，但可以用高階函式實現類似效果：

```javascript
// JavaScript
function myDecorator(func) {
    return function() {
        console.log("函式執行前");
        func();
        console.log("函式執行後");
    };
}

const sayHello = myDecorator(function() {
    console.log("Hello!");
});

sayHello();

// JavaScript 裝飾器提案（Stage 3）
// @myDecorator
// function sayHello() { ... }
```

### 基本裝飾器結構

```python
def decorator(func):
    """基本裝飾器模板"""
    def wrapper(*args, **kwargs):
        # 前置處理
        result = func(*args, **kwargs)  # 呼叫原函式
        # 後置處理
        return result
    return wrapper

@decorator
def my_function():
    pass
```

### 處理函式參數

```python
def log_call(func):
    """記錄函式呼叫"""
    def wrapper(*args, **kwargs):
        print(f"呼叫 {func.__name__}")
        print(f"  參數：args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)
        print(f"  回傳：{result}")
        return result
    return wrapper

@log_call
def add(a, b):
    return a + b

@log_call
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

add(3, 5)
# 呼叫 add
#   參數：args=(3, 5), kwargs={}
#   回傳：8

greet("Alice", greeting="Hi")
# 呼叫 greet
#   參數：args=('Alice',), kwargs={'greeting': 'Hi'}
#   回傳：Hi, Alice!
```

### 保留原函式資訊（functools.wraps）

裝飾器會改變函式的元資料，使用 `@wraps` 可以保留原函式的資訊：

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)  # 保留原函式的 __name__, __doc__ 等
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

# 不使用 @wraps
def bad_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@bad_decorator
def greet(name):
    """問候函式"""
    return f"Hello, {name}!"

print(greet.__name__)  # "wrapper"（錯誤！）
print(greet.__doc__)   # None（錯誤！）

# 使用 @wraps
@my_decorator
def greet(name):
    """問候函式"""
    return f"Hello, {name}!"

print(greet.__name__)  # "greet"（正確）
print(greet.__doc__)   # "問候函式"（正確）
```

---

## 12.2 實用裝飾器範例

### 計時裝飾器

```python
import time
from functools import wraps

def timer(func):
    """測量函式執行時間"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__} 執行時間：{end - start:.4f} 秒")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    return "完成"

slow_function()
# slow_function 執行時間：1.0012 秒
```

### 快取裝飾器

```python
from functools import wraps

def cache(func):
    """簡單的快取裝飾器"""
    cached_results = {}

    @wraps(func)
    def wrapper(*args):
        if args in cached_results:
            print(f"從快取取得 {args}")
            return cached_results[args]
        result = func(*args)
        cached_results[args] = result
        return result
    return wrapper

@cache
def fibonacci(n):
    """計算費氏數列"""
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(10))  # 55（只計算一次，之後從快取取得）
print(fibonacci(10))  # 從快取取得 (10,) → 55

# Python 內建的快取裝飾器（推薦使用）
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)
```

### 重試裝飾器

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1):
    """重試裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_exception = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_exception = e
                    print(f"第 {attempt} 次嘗試失敗：{e}")
                    if attempt < max_attempts:
                        time.sleep(delay)
            raise last_exception
        return wrapper
    return decorator

@retry(max_attempts=3, delay=0.5)
def unstable_api_call():
    import random
    if random.random() < 0.7:
        raise ConnectionError("連線失敗")
    return "成功"

# 會自動重試最多 3 次
result = unstable_api_call()
```

### 驗證裝飾器

```python
from functools import wraps

def validate_types(**expected_types):
    """型別驗證裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # 取得函式參數名稱
            import inspect
            sig = inspect.signature(func)
            bound = sig.bind(*args, **kwargs)
            bound.apply_defaults()

            # 驗證型別
            for param, expected in expected_types.items():
                if param in bound.arguments:
                    value = bound.arguments[param]
                    if not isinstance(value, expected):
                        raise TypeError(
                            f"參數 '{param}' 應為 {expected.__name__}，"
                            f"但收到 {type(value).__name__}"
                        )
            return func(*args, **kwargs)
        return wrapper
    return decorator

@validate_types(name=str, age=int)
def create_user(name, age):
    return {"name": name, "age": age}

create_user("Alice", 30)     # 正常
# create_user("Alice", "30") # TypeError: 參數 'age' 應為 int，但收到 str
```

### 權限檢查裝飾器

```python
from functools import wraps

def require_auth(func):
    """需要認證的裝飾器"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        # 假設從 context 取得當前使用者
        user = get_current_user()
        if not user:
            raise PermissionError("需要登入")
        return func(*args, **kwargs)
    return wrapper

def require_role(*roles):
    """需要特定角色的裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            user = get_current_user()
            if not user:
                raise PermissionError("需要登入")
            if user.role not in roles:
                raise PermissionError(f"需要角色：{roles}")
            return func(*args, **kwargs)
        return wrapper
    return decorator

@require_auth
def view_profile():
    return "個人資料"

@require_role("admin", "moderator")
def delete_user(user_id):
    return f"刪除使用者 {user_id}"
```

### 日誌裝飾器

```python
import logging
from functools import wraps

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def log(level=logging.INFO):
    """日誌裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            logger.log(level, f"呼叫 {func.__name__} args={args} kwargs={kwargs}")
            try:
                result = func(*args, **kwargs)
                logger.log(level, f"{func.__name__} 回傳 {result}")
                return result
            except Exception as e:
                logger.exception(f"{func.__name__} 發生例外：{e}")
                raise
        return wrapper
    return decorator

@log(level=logging.DEBUG)
def process_data(data):
    return data.upper()
```

---

## 12.3 帶參數的裝飾器

### 三層函式結構

```python
from functools import wraps

def repeat(times):
    """重複執行裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            results = []
            for _ in range(times):
                results.append(func(*args, **kwargs))
            return results
        return wrapper
    return decorator

@repeat(times=3)
def say_hello():
    print("Hello!")
    return "done"

results = say_hello()
# Hello!
# Hello!
# Hello!
print(results)  # ["done", "done", "done"]

# 等價於
# say_hello = repeat(times=3)(say_hello)
```

### 可選參數的裝飾器

```python
from functools import wraps

def debug(func=None, *, prefix="DEBUG"):
    """支援可選參數的裝飾器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            print(f"[{prefix}] 呼叫 {func.__name__}")
            return func(*args, **kwargs)
        return wrapper

    if func is not None:
        # 不帶參數呼叫：@debug
        return decorator(func)
    # 帶參數呼叫：@debug(prefix="INFO")
    return decorator

# 兩種用法都可以
@debug
def func1():
    pass

@debug(prefix="INFO")
def func2():
    pass

func1()  # [DEBUG] 呼叫 func1
func2()  # [INFO] 呼叫 func2
```

### 使用 class 實作裝飾器

```python
from functools import wraps

class CountCalls:
    """計算函式被呼叫次數的裝飾器（使用類別實作）"""

    def __init__(self, func):
        wraps(func)(self)
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"{self.func.__name__} 已被呼叫 {self.count} 次")
        return self.func(*args, **kwargs)

@CountCalls
def say_hello():
    print("Hello!")

say_hello()  # say_hello 已被呼叫 1 次 → Hello!
say_hello()  # say_hello 已被呼叫 2 次 → Hello!
say_hello()  # say_hello 已被呼叫 3 次 → Hello!
print(say_hello.count)  # 3
```

### 帶參數的類別裝飾器

```python
from functools import wraps

class Retry:
    """重試裝飾器（類別版本）"""

    def __init__(self, max_attempts=3, exceptions=(Exception,)):
        self.max_attempts = max_attempts
        self.exceptions = exceptions

    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_exception = None
            for attempt in range(1, self.max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except self.exceptions as e:
                    last_exception = e
                    print(f"嘗試 {attempt}/{self.max_attempts} 失敗：{e}")
            raise last_exception
        return wrapper

@Retry(max_attempts=5, exceptions=(ConnectionError, TimeoutError))
def fetch_data():
    import random
    if random.random() < 0.8:
        raise ConnectionError("連線失敗")
    return "資料"
```

---

## 12.4 多重裝飾器

### 堆疊裝飾器

```python
from functools import wraps

def bold(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<b>{func(*args, **kwargs)}</b>"
    return wrapper

def italic(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<i>{func(*args, **kwargs)}</i>"
    return wrapper

def underline(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<u>{func(*args, **kwargs)}</u>"
    return wrapper

@bold
@italic
@underline
def greet(name):
    return f"Hello, {name}!"

print(greet("Alice"))
# <b><i><u>Hello, Alice!</u></i></b>

# 執行順序：從下到上包裝，從外到內執行
# 等價於：bold(italic(underline(greet)))
```

### 執行順序示意

```python
from functools import wraps

def decorator_a(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("A - 前")
        result = func(*args, **kwargs)
        print("A - 後")
        return result
    return wrapper

def decorator_b(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("B - 前")
        result = func(*args, **kwargs)
        print("B - 後")
        return result
    return wrapper

@decorator_a
@decorator_b
def my_function():
    print("函式執行")

my_function()
# A - 前
# B - 前
# 函式執行
# B - 後
# A - 後
```

---

## 12.5 類別裝飾器

裝飾器不僅可以裝飾函式，也可以裝飾類別。

### 裝飾類別

```python
def singleton(cls):
    """單例模式裝飾器"""
    instances = {}

    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]

    return get_instance

@singleton
class Database:
    def __init__(self):
        print("建立資料庫連線")

db1 = Database()  # 建立資料庫連線
db2 = Database()  # 不會再次建立
print(db1 is db2)  # True
```

### 為類別添加方法

```python
def add_repr(cls):
    """自動產生 __repr__ 方法"""
    def __repr__(self):
        attrs = ", ".join(
            f"{k}={v!r}"
            for k, v in self.__dict__.items()
        )
        return f"{cls.__name__}({attrs})"

    cls.__repr__ = __repr__
    return cls

@add_repr
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("Alice", 30)
print(person)  # Person(name='Alice', age=30)
```

### 註冊類別

```python
# 插件註冊系統
registry = {}

def register(cls):
    """註冊類別到 registry"""
    registry[cls.__name__] = cls
    return cls

@register
class PluginA:
    def run(self):
        return "Plugin A"

@register
class PluginB:
    def run(self):
        return "Plugin B"

print(registry)
# {"PluginA": <class 'PluginA'>, "PluginB": <class 'PluginB'>}

# 動態取得並使用
plugin = registry["PluginA"]()
print(plugin.run())  # Plugin A
```

---

## 12.6 常用內建裝飾器

### @property

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        """getter"""
        return self._radius

    @radius.setter
    def radius(self, value):
        """setter"""
        if value < 0:
            raise ValueError("半徑不能為負數")
        self._radius = value

    @property
    def area(self):
        """唯讀屬性"""
        import math
        return math.pi * self._radius ** 2

circle = Circle(5)
print(circle.radius)   # 5
print(circle.area)     # 78.54...

circle.radius = 10
# circle.area = 100    # AttributeError: can't set attribute
```

### @staticmethod 和 @classmethod

```python
class MyClass:
    class_var = "類別變數"

    def __init__(self, value):
        self.instance_var = value

    def instance_method(self):
        """實例方法：可存取實例和類別"""
        return f"instance: {self.instance_var}, class: {self.class_var}"

    @classmethod
    def class_method(cls):
        """類別方法：只能存取類別"""
        return f"class: {cls.class_var}"

    @classmethod
    def create_default(cls):
        """工廠方法"""
        return cls("default")

    @staticmethod
    def static_method(x, y):
        """靜態方法：不能存取實例或類別"""
        return x + y

# 使用
obj = MyClass("實例值")
print(obj.instance_method())  # instance: 實例值, class: 類別變數
print(MyClass.class_method()) # class: 類別變數
print(MyClass.static_method(1, 2))  # 3

default_obj = MyClass.create_default()
print(default_obj.instance_var)  # default
```

### @functools.lru_cache

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    """帶快取的費氏數列"""
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(100))  # 快速計算

# 查看快取資訊
print(fibonacci.cache_info())
# CacheInfo(hits=98, misses=101, maxsize=128, currsize=101)

# 清除快取
fibonacci.cache_clear()
```

### @functools.cache（Python 3.9+）

```python
from functools import cache

@cache  # 等同於 @lru_cache(maxsize=None)
def factorial(n):
    return n * factorial(n - 1) if n else 1

print(factorial(10))  # 3628800
```

### @functools.cached_property（Python 3.8+）

```python
from functools import cached_property

class DataAnalyzer:
    def __init__(self, data):
        self.data = data

    @cached_property
    def statistics(self):
        """只計算一次的屬性"""
        print("計算統計資料...")
        return {
            "mean": sum(self.data) / len(self.data),
            "min": min(self.data),
            "max": max(self.data),
        }

analyzer = DataAnalyzer([1, 2, 3, 4, 5])
print(analyzer.statistics)  # 計算統計資料... → {'mean': 3.0, ...}
print(analyzer.statistics)  # 不再計算，直接回傳
```

### @functools.total_ordering

```python
from functools import total_ordering

@total_ordering
class Student:
    """只需定義 __eq__ 和一個比較方法"""

    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

    def __eq__(self, other):
        return self.grade == other.grade

    def __lt__(self, other):
        return self.grade < other.grade

    # @total_ordering 會自動產生 __le__, __gt__, __ge__

alice = Student("Alice", 85)
bob = Student("Bob", 90)

print(alice < bob)   # True
print(alice <= bob)  # True
print(alice > bob)   # False
print(alice >= bob)  # False
print(alice == bob)  # False
```

### @dataclass（Python 3.7+）

```python
from dataclasses import dataclass, field

@dataclass
class Point:
    x: float
    y: float

@dataclass
class Person:
    name: str
    age: int
    email: str = ""
    tags: list = field(default_factory=list)

# 自動產生 __init__, __repr__, __eq__
p1 = Point(1.0, 2.0)
p2 = Point(1.0, 2.0)
print(p1)          # Point(x=1.0, y=2.0)
print(p1 == p2)    # True

person = Person("Alice", 30)
print(person)      # Person(name='Alice', age=30, email='', tags=[])

# 更多選項
@dataclass(frozen=True)  # 不可變
class ImmutablePoint:
    x: float
    y: float

@dataclass(order=True)  # 可排序
class SortablePerson:
    name: str
    age: int
```

### @contextlib.contextmanager

```python
from contextlib import contextmanager

@contextmanager
def timer(name):
    """計時 context manager"""
    import time
    start = time.time()
    try:
        yield  # 這裡執行 with 區塊的程式碼
    finally:
        elapsed = time.time() - start
        print(f"{name}: {elapsed:.4f} 秒")

with timer("處理資料"):
    # 耗時操作
    import time
    time.sleep(1)
# 處理資料: 1.0012 秒

@contextmanager
def open_file(path, mode):
    """檔案處理 context manager"""
    f = open(path, mode)
    try:
        yield f
    finally:
        f.close()

with open_file("test.txt", "w") as f:
    f.write("Hello")
```

---

## 12.7 裝飾器最佳實踐

### 1. 總是使用 @wraps

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)  # 保留原函式資訊
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### 2. 處理所有參數

```python
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):  # 接受任意參數
        return func(*args, **kwargs)
    return wrapper
```

### 3. 保持裝飾器簡單

```python
# 好：單一職責
@timer
@log
@auth_required
def my_function():
    pass

# 不好：一個裝飾器做太多事
@do_everything  # 計時 + 日誌 + 權限檢查
def my_function():
    pass
```

### 4. 文件化裝飾器

```python
def retry(max_attempts=3, delay=1.0, exceptions=(Exception,)):
    """
    重試裝飾器。

    當函式拋出指定例外時，自動重試。

    Args:
        max_attempts: 最大嘗試次數
        delay: 每次重試間隔（秒）
        exceptions: 要捕捉的例外類型

    Example:
        @retry(max_attempts=5, delay=0.5)
        def unstable_api():
            ...

    Raises:
        最後一次捕捉到的例外
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # ...
            pass
        return wrapper
    return decorator
```

### 5. 考慮使用類別裝飾器

```python
# 當裝飾器需要維護狀態時
class RateLimiter:
    def __init__(self, calls_per_second):
        self.calls_per_second = calls_per_second
        self.last_call = 0

    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # 限流邏輯
            pass
        return wrapper
```

---

## 練習題

### 練習 1：基本裝飾器

寫一個裝飾器，印出函式的執行時間和記憶體使用量：

```python
def profile(func):
    """
    效能分析裝飾器

    印出執行時間和記憶體增量
    """
    # 你的程式碼
    pass

@profile
def process_data():
    return [i ** 2 for i in range(100000)]
```

### 練習 2：帶參數的裝飾器

寫一個裝飾器，限制函式的呼叫頻率：

```python
def rate_limit(calls_per_minute):
    """
    限流裝飾器

    @rate_limit(10)  # 每分鐘最多 10 次
    def api_call():
        ...
    """
    # 你的程式碼
    pass
```

### 練習 3：類別裝飾器

寫一個類別裝飾器，自動為類別添加 JSON 序列化功能：

```python
def jsonable(cls):
    """
    讓類別可以轉換為 JSON

    @jsonable
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

    p = Person("Alice", 30)
    print(p.to_json())  # {"name": "Alice", "age": 30}
    """
    # 你的程式碼
    pass
```

---

## 小結

### 裝飾器結構

```python
# 無參數裝飾器
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

# 帶參數裝飾器
def decorator_with_args(arg1, arg2):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            return func(*args, **kwargs)
        return wrapper
    return decorator

# 類別裝飾器
class Decorator:
    def __init__(self, func):
        wraps(func)(self)
        self.func = func

    def __call__(self, *args, **kwargs):
        return self.func(*args, **kwargs)
```

### 常用內建裝飾器

| 裝飾器 | 模組 | 用途 |
|--------|------|------|
| `@property` | 內建 | 將方法變成屬性 |
| `@staticmethod` | 內建 | 靜態方法 |
| `@classmethod` | 內建 | 類別方法 |
| `@lru_cache` | functools | 快取結果 |
| `@cache` | functools | 無限快取 |
| `@cached_property` | functools | 快取屬性 |
| `@total_ordering` | functools | 自動產生比較方法 |
| `@wraps` | functools | 保留函式資訊 |
| `@dataclass` | dataclasses | 自動產生類別方法 |
| `@contextmanager` | contextlib | 建立 context manager |
| `@abstractmethod` | abc | 抽象方法 |

### 裝飾器 vs JavaScript

| Python | JavaScript | 說明 |
|--------|------------|------|
| `@decorator` | 高階函式 | Python 有語法糖 |
| `@decorator(args)` | 高階函式 | 帶參數 |
| 類別裝飾器 | 類別裝飾器提案 | ES 提案中 |
| `@property` | `get/set` | 屬性存取器 |
| `@staticmethod` | `static` | 靜態方法 |
