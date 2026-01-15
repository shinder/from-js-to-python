# 第 16 章：實用技巧

## 概述

本章整理 Python 的各種實用技巧，這些是日常開發中經常使用的模式和慣用法。對於熟悉 JavaScript 的開發者，許多概念會有對應但語法不同，掌握這些技巧能讓你的 Python 程式碼更加簡潔優雅。

---

## 16.1 解構賦值（Unpacking）

Python 的解構賦值比 JavaScript 更為靈活，支援序列、字典等多種形式。

### 基本序列解構

```python
# 列表解構
a, b, c = [1, 2, 3]
print(a, b, c)  # 1 2 3

# 元組解構
x, y = (10, 20)
print(x, y)  # 10 20

# 字串解構
first, second, third = "ABC"
print(first, second, third)  # A B C

# 交換變數（Python 特有的優雅寫法）
a, b = b, a
```

對比 JavaScript：

```javascript
// JavaScript
const [a, b, c] = [1, 2, 3];
let [x, y] = [10, 20];

// 交換變數
[a, b] = [b, a];
```

### 星號運算子（*）- 收集剩餘元素

```python
# 取得首尾元素
first, *middle, last = [1, 2, 3, 4, 5]
print(first)   # 1
print(middle)  # [2, 3, 4]
print(last)    # 5

# 只取第一個
head, *tail = [1, 2, 3, 4, 5]
print(head)  # 1
print(tail)  # [2, 3, 4, 5]

# 只取最後一個
*init, last = [1, 2, 3, 4, 5]
print(init)  # [1, 2, 3, 4]
print(last)  # 5

# 忽略中間元素
first, *_, last = [1, 2, 3, 4, 5]
print(first, last)  # 1 5
```

對比 JavaScript：

```javascript
// JavaScript
const [first, ...rest] = [1, 2, 3, 4, 5];
const [head, ...tail] = [1, 2, 3, 4, 5];

// JavaScript 的 rest 只能在最後
// Python 的 * 可以在任何位置
```

### 巢狀解構

```python
# 巢狀列表
[[a, b], [c, d]] = [[1, 2], [3, 4]]
print(a, b, c, d)  # 1 2 3 4

# 混合結構
data = ("Alice", (25, "Engineer"))
name, (age, job) = data
print(name, age, job)  # Alice 25 Engineer

# 複雜結構
users = [
    ("Alice", ["python", "javascript"]),
    ("Bob", ["rust", "go"]),
]

for name, [lang1, lang2] in users:
    print(f"{name}: {lang1}, {lang2}")
```

### 函式參數解構

```python
# 在函式參數中解構
def process_point(point):
    x, y = point
    return x + y

# 更直接的寫法
def process_point_v2((x, y)):  # Python 2 支援，Python 3 不支援
    return x + y

# Python 3 的替代方案
def process_point_v3(point):
    match point:
        case (x, y):
            return x + y
```

### 字典解構

Python 的字典解構與 JavaScript 不同，主要用於函式參數。

```python
# 使用 ** 展開字典
def greet(name, age):
    print(f"{name} is {age} years old")

user = {"name": "Alice", "age": 30}
greet(**user)  # Alice is 30 years old

# 合併字典
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
merged = {**dict1, **dict2}
print(merged)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Python 3.9+ 可用 | 運算子
merged = dict1 | dict2

# 從字典取值（類似 JavaScript 的物件解構）
user = {"name": "Alice", "age": 30, "email": "alice@example.com"}
name = user["name"]
age = user["age"]

# 更 Pythonic 的方式：使用 .values() 或 .items()
name, age, email = user.values()  # 注意：字典順序在 Python 3.7+ 才保證

# 使用 itemgetter
from operator import itemgetter
get_name_age = itemgetter("name", "age")
name, age = get_name_age(user)
```

對比 JavaScript：

```javascript
// JavaScript 物件解構
const { name, age } = { name: "Alice", age: 30 };

// 展開運算子
const merged = { ...dict1, ...dict2 };
```

---

## 16.2 Context Manager（with 語句）

Context Manager 是 Python 處理資源管理的優雅方式，確保資源正確釋放。

### 基本用法

```python
# 檔案處理 - 自動關閉檔案
with open("file.txt", "r") as f:
    content = f.read()
# 離開 with 區塊時自動關閉檔案

# 多個資源
with open("input.txt", "r") as f_in, open("output.txt", "w") as f_out:
    f_out.write(f_in.read())

# Python 3.10+ 可用括號換行
with (
    open("input.txt", "r") as f_in,
    open("output.txt", "w") as f_out,
):
    f_out.write(f_in.read())
```

對比 JavaScript：

```javascript
// JavaScript 沒有內建的 context manager
// 需要手動處理或使用 try-finally

// Node.js 的檔案處理
import { readFile } from 'fs/promises';
const content = await readFile('file.txt', 'utf-8');

// 或使用 try-finally
const fs = require('fs');
const f = fs.openSync('file.txt', 'r');
try {
    // 使用檔案
} finally {
    fs.closeSync(f);
}
```

### 常用 Context Manager

```python
# 鎖定（多執行緒）
import threading

lock = threading.Lock()
with lock:
    # 執行緒安全的操作
    shared_resource += 1

# 資料庫連線
import sqlite3

with sqlite3.connect("database.db") as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users")
    # 自動 commit（若無錯誤）

# HTTP 請求
import requests

with requests.Session() as session:
    response = session.get("https://api.example.com")
    # Session 會被正確關閉

# 暫時改變目錄
import os
from contextlib import chdir  # Python 3.11+

with chdir("/tmp"):
    # 在 /tmp 目錄下操作
    pass
# 自動回到原目錄

# 計時器
import time
from contextlib import contextmanager

@contextmanager
def timer(name):
    start = time.time()
    yield
    end = time.time()
    print(f"{name}: {end - start:.2f}s")

with timer("Operation"):
    time.sleep(1)
# 輸出: Operation: 1.00s
```

### 自訂 Context Manager（類別方式）

```python
class DatabaseConnection:
    """自訂 Context Manager 範例。"""

    def __init__(self, host: str):
        self.host = host
        self.connection = None

    def __enter__(self):
        """進入 with 區塊時呼叫。"""
        print(f"Connecting to {self.host}")
        self.connection = self._create_connection()
        return self.connection

    def __exit__(self, exc_type, exc_val, exc_tb):
        """離開 with 區塊時呼叫。

        Args:
            exc_type: 例外類型（若有）
            exc_val: 例外值（若有）
            exc_tb: Traceback（若有）

        Returns:
            True 表示已處理例外，False 表示重新拋出
        """
        print("Closing connection")
        if self.connection:
            self.connection.close()

        # 若要吞掉例外，回傳 True
        if exc_type is not None:
            print(f"Error occurred: {exc_val}")
            return False  # 重新拋出例外

        return False

    def _create_connection(self):
        # 模擬建立連線
        return type("Connection", (), {"close": lambda self: None})()


# 使用
with DatabaseConnection("localhost") as conn:
    # 使用 conn 進行操作
    pass
```

### 自訂 Context Manager（裝飾器方式）

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(name):
    """使用 contextmanager 裝飾器建立 Context Manager。"""
    # __enter__ 部分
    print(f"Acquiring {name}")
    resource = {"name": name, "data": []}

    try:
        yield resource  # 回傳給 with ... as 的變數
    except Exception as e:
        print(f"Error: {e}")
        raise  # 重新拋出
    finally:
        # __exit__ 部分
        print(f"Releasing {name}")


with managed_resource("database") as res:
    res["data"].append(1)
    print(res)
# 輸出:
# Acquiring database
# {'name': 'database', 'data': [1]}
# Releasing database
```

### 實用 contextlib 工具

```python
from contextlib import (
    suppress,
    redirect_stdout,
    redirect_stderr,
    nullcontext,
    ExitStack,
)
import io

# suppress - 忽略特定例外
with suppress(FileNotFoundError):
    os.remove("nonexistent.txt")
# 不會拋出錯誤

# redirect_stdout - 重導向輸出
f = io.StringIO()
with redirect_stdout(f):
    print("Hello")
output = f.getvalue()  # "Hello\n"

# nullcontext - 條件性使用 context manager
def process(use_lock=True):
    lock = threading.Lock() if use_lock else nullcontext()
    with lock:
        # 操作
        pass

# ExitStack - 動態管理多個 context manager
with ExitStack() as stack:
    files = [stack.enter_context(open(f)) for f in filenames]
    # 所有檔案會在離開時關閉
```

---

## 16.3 常用內建函式

Python 提供許多強大的內建函式，善用它們可以大幅簡化程式碼。

### enumerate - 帶索引迭代

```python
items = ["a", "b", "c"]

# 傳統寫法
for i in range(len(items)):
    print(i, items[i])

# Pythonic 寫法
for i, item in enumerate(items):
    print(i, item)

# 指定起始索引
for i, item in enumerate(items, start=1):
    print(i, item)  # 1 a, 2 b, 3 c
```

對比 JavaScript：

```javascript
// JavaScript
items.forEach((item, i) => console.log(i, item));

// 或
for (const [i, item] of items.entries()) {
    console.log(i, item);
}
```

### zip - 並行迭代多個序列

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["Tokyo", "London", "Paris"]

# 並行迭代
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, {city}")

# 建立字典
user_dict = dict(zip(names, ages))
# {'Alice': 25, 'Bob': 30, 'Charlie': 35}

# 解壓縮（轉置）
pairs = [(1, "a"), (2, "b"), (3, "c")]
numbers, letters = zip(*pairs)
print(numbers)  # (1, 2, 3)
print(letters)  # ('a', 'b', 'c')

# 最短序列決定長度
short = [1, 2]
long = [1, 2, 3, 4]
list(zip(short, long))  # [(1, 1), (2, 2)]

# zip_longest - 使用最長序列
from itertools import zip_longest
list(zip_longest(short, long, fillvalue=0))
# [(1, 1), (2, 2), (0, 3), (0, 4)]
```

對比 JavaScript：

```javascript
// JavaScript 沒有內建 zip，需要自行實作或使用 lodash
const zip = (...arrays) =>
    arrays[0].map((_, i) => arrays.map(arr => arr[i]));
```

### map, filter, reduce

```python
numbers = [1, 2, 3, 4, 5]

# map - 映射
squared = list(map(lambda x: x ** 2, numbers))
# [1, 4, 9, 16, 25]

# 更 Pythonic 的寫法：列表推導式
squared = [x ** 2 for x in numbers]

# filter - 過濾
evens = list(filter(lambda x: x % 2 == 0, numbers))
# [2, 4]

# 更 Pythonic 的寫法：列表推導式
evens = [x for x in numbers if x % 2 == 0]

# reduce - 歸約
from functools import reduce
total = reduce(lambda acc, x: acc + x, numbers, 0)
# 15

# 更 Pythonic 的寫法：使用內建函式
total = sum(numbers)

# reduce 的實際應用場景
# 找最大值（雖然有 max()）
maximum = reduce(lambda a, b: a if a > b else b, numbers)

# 攤平巢狀列表
nested = [[1, 2], [3, 4], [5, 6]]
flat = reduce(lambda acc, x: acc + x, nested, [])
# [1, 2, 3, 4, 5, 6]

# 更好的寫法
from itertools import chain
flat = list(chain.from_iterable(nested))
```

對比 JavaScript：

```javascript
// JavaScript
const squared = numbers.map(x => x ** 2);
const evens = numbers.filter(x => x % 2 === 0);
const total = numbers.reduce((acc, x) => acc + x, 0);
```

### sorted 與 sort

```python
items = [3, 1, 4, 1, 5, 9, 2, 6]

# sorted - 回傳新列表（不修改原列表）
sorted_items = sorted(items)
print(items)        # [3, 1, 4, 1, 5, 9, 2, 6]（原列表不變）
print(sorted_items) # [1, 1, 2, 3, 4, 5, 6, 9]

# sort - 原地排序（修改原列表）
items.sort()
print(items)  # [1, 1, 2, 3, 4, 5, 6, 9]

# 反向排序
sorted(items, reverse=True)

# 自訂排序鍵
users = [
    {"name": "Alice", "age": 30},
    {"name": "Bob", "age": 25},
    {"name": "Charlie", "age": 35},
]

# 按年齡排序
sorted_users = sorted(users, key=lambda u: u["age"])

# 使用 operator.itemgetter（更快）
from operator import itemgetter
sorted_users = sorted(users, key=itemgetter("age"))

# 多重排序
data = [("Alice", 30), ("Bob", 25), ("Alice", 25)]
sorted(data, key=lambda x: (x[0], x[1]))  # 先按名字，再按年齡

# 穩定排序特性
# Python 的排序是穩定的，相等元素保持原順序
```

對比 JavaScript：

```javascript
// JavaScript
const sortedItems = [...items].sort((a, b) => a - b);
items.sort((a, b) => a - b);  // 原地排序

// 按屬性排序
users.sort((a, b) => a.age - b.age);
```

### any 與 all

```python
numbers = [1, 2, 3, 4, 5]

# any - 任一為 True
any([False, False, True])   # True
any([False, False, False])  # False
any(x > 3 for x in numbers) # True

# all - 全部為 True
all([True, True, True])     # True
all([True, False, True])    # False
all(x > 0 for x in numbers) # True

# 實際應用
def is_valid_user(user):
    required_fields = ["name", "email", "age"]
    return all(field in user for field in required_fields)

def has_permission(user, permissions):
    return any(p in user.get("permissions", []) for p in permissions)
```

對比 JavaScript：

```javascript
// JavaScript
[1, 2, 3].some(x => x > 2);   // true（類似 any）
[1, 2, 3].every(x => x > 0);  // true（類似 all）
```

### min, max, sum

```python
numbers = [1, 2, 3, 4, 5]

# 基本用法
min(numbers)  # 1
max(numbers)  # 5
sum(numbers)  # 15

# 帶預設值（空序列時使用）
min([], default=0)  # 0

# 自訂比較鍵
users = [
    {"name": "Alice", "age": 30},
    {"name": "Bob", "age": 25},
]
youngest = min(users, key=lambda u: u["age"])

# 同時取得最小最大值
min_val, max_val = min(numbers), max(numbers)

# 帶條件的 sum
total = sum(x for x in numbers if x > 2)  # 12

# 浮點數精確加總
from math import fsum
fsum([0.1, 0.2, 0.3])  # 比 sum 更精確
```

### isinstance 與 type

```python
# isinstance - 檢查型別（推薦）
x = 42
isinstance(x, int)           # True
isinstance(x, (int, float))  # True（多型別檢查）

# type - 取得精確型別
type(x)  # <class 'int'>
type(x) == int  # True（不建議，不處理繼承）

# 差異示範
class MyInt(int):
    pass

n = MyInt(42)
isinstance(n, int)  # True（處理繼承）
type(n) == int      # False（只檢查精確型別）

# typing 模組的型別檢查
from typing import get_type_hints
def greet(name: str) -> str:
    return f"Hello, {name}"

get_type_hints(greet)  # {'name': str, 'return': str}
```

### getattr, setattr, hasattr

```python
class User:
    def __init__(self, name):
        self.name = name

user = User("Alice")

# hasattr - 檢查屬性是否存在
hasattr(user, "name")   # True
hasattr(user, "email")  # False

# getattr - 取得屬性
getattr(user, "name")           # "Alice"
getattr(user, "email", None)    # None（預設值）
getattr(user, "email", "N/A")   # "N/A"

# setattr - 設定屬性
setattr(user, "email", "alice@example.com")
print(user.email)  # "alice@example.com"

# 動態屬性存取
field = "name"
value = getattr(user, field)

# 實際應用：從字典建立物件
data = {"name": "Bob", "age": 30}
user = User.__new__(User)
for key, value in data.items():
    setattr(user, key, value)
```

對比 JavaScript：

```javascript
// JavaScript
const user = { name: "Alice" };

// 檢查屬性
"name" in user;  // true
user.hasOwnProperty("name");  // true

// 動態存取
const field = "name";
user[field];  // "Alice"
```

### vars 與 dir

```python
class User:
    class_var = "shared"

    def __init__(self, name):
        self.name = name

user = User("Alice")

# vars - 取得物件的 __dict__
vars(user)  # {'name': 'Alice'}

# dir - 列出所有屬性和方法
dir(user)   # ['__class__', ..., 'class_var', 'name', ...]

# 實際應用
def obj_to_dict(obj):
    """將物件轉為字典（僅實例屬性）。"""
    return {k: v for k, v in vars(obj).items() if not k.startswith("_")}
```

---

## 16.4 字串處理技巧

### f-string 進階用法

```python
name = "Alice"
age = 30
price = 1234.5678

# 基本用法
f"Name: {name}, Age: {age}"

# 運算式
f"Next year: {age + 1}"

# 格式化數字
f"Price: {price:.2f}"       # "Price: 1234.57"
f"Price: {price:,.2f}"      # "Price: 1,234.57"
f"Price: {price:>10.2f}"    # "Price:    1234.57"（右對齊）

# 百分比
ratio = 0.85
f"Progress: {ratio:.1%}"    # "Progress: 85.0%"

# 日期時間
from datetime import datetime
now = datetime.now()
f"Date: {now:%Y-%m-%d %H:%M}"  # "Date: 2024-01-15 10:30"

# 除錯用（Python 3.8+）
x = 10
f"{x=}"         # "x=10"
f"{x = }"       # "x = 10"
f"{x*2=}"       # "x*2=20"

# 物件表示
class User:
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return f"User({self.name!r})"

user = User("Alice")
f"{user}"       # "User('Alice')"
f"{user!r}"     # "User('Alice')"（強制使用 __repr__）
f"{user!s}"     # 使用 __str__

# 字典取值
data = {"name": "Alice"}
f"Name: {data['name']}"

# 巢狀引號
f"Name: {data['name']}"  # 外雙內單
f'Name: {data["name"]}'  # 外單內雙
```

對比 JavaScript：

```javascript
// JavaScript 模板字串
const greeting = `Name: ${name}, Age: ${age}`;
const nextYear = `Next year: ${age + 1}`;

// 格式化需要額外處理
const formatted = price.toFixed(2);
const withCommas = price.toLocaleString();
```

### 字串方法

```python
text = "  Hello, World!  "

# 去除空白
text.strip()    # "Hello, World!"
text.lstrip()   # "Hello, World!  "
text.rstrip()   # "  Hello, World!"

# 分割與合併
"a,b,c".split(",")           # ["a", "b", "c"]
"a  b  c".split()            # ["a", "b", "c"]（多空格）
",".join(["a", "b", "c"])    # "a,b,c"

# 搜尋
"hello".find("l")      # 2（找不到回傳 -1）
"hello".index("l")     # 2（找不到拋出 ValueError）
"hello".count("l")     # 2
"hello".startswith("he")  # True
"hello".endswith("lo")    # True
"l" in "hello"            # True

# 替換
"hello".replace("l", "L")      # "heLLo"
"hello".replace("l", "L", 1)   # "heLlo"（只替換一次）

# 大小寫
"hello".upper()        # "HELLO"
"HELLO".lower()        # "hello"
"hello".capitalize()   # "Hello"
"hello world".title()  # "Hello World"
"Hello".swapcase()     # "hELLO"

# 對齊
"hi".ljust(10)         # "hi        "
"hi".rjust(10)         # "        hi"
"hi".center(10)        # "    hi    "
"42".zfill(5)          # "00042"

# 判斷
"123".isdigit()        # True
"abc".isalpha()        # True
"abc123".isalnum()     # True
"   ".isspace()        # True
```

### 正規表達式

```python
import re

text = "Contact: alice@example.com, bob@test.org"

# 搜尋
match = re.search(r"\w+@\w+\.\w+", text)
if match:
    print(match.group())  # "alice@example.com"

# 找出所有匹配
emails = re.findall(r"\w+@\w+\.\w+", text)
# ["alice@example.com", "bob@test.org"]

# 替換
clean = re.sub(r"\w+@\w+\.\w+", "[EMAIL]", text)
# "Contact: [EMAIL], [EMAIL]"

# 分割
parts = re.split(r"[,\s]+", "a, b,  c")
# ["a", "b", "c"]

# 編譯正規表達式（重複使用時更快）
email_pattern = re.compile(r"(?P<user>\w+)@(?P<domain>\w+\.\w+)")
match = email_pattern.search(text)
if match:
    print(match.group("user"))    # "alice"
    print(match.group("domain"))  # "example.com"

# 常用模式
patterns = {
    "email": r"[\w.-]+@[\w.-]+\.\w+",
    "url": r"https?://[\w.-]+(?:/[\w.-]*)*",
    "phone": r"\d{2,4}-\d{3,4}-\d{4}",
    "date": r"\d{4}-\d{2}-\d{2}",
}
```

---

## 16.5 集合操作

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# 聯集
a | b                  # {1, 2, 3, 4, 5, 6}
a.union(b)

# 交集
a & b                  # {3, 4}
a.intersection(b)

# 差集
a - b                  # {1, 2}
a.difference(b)

# 對稱差集（互斥元素）
a ^ b                  # {1, 2, 5, 6}
a.symmetric_difference(b)

# 子集與超集
{1, 2} <= {1, 2, 3}    # True（子集）
{1, 2, 3} >= {1, 2}    # True（超集）
{1, 2}.issubset({1, 2, 3})
{1, 2, 3}.issuperset({1, 2})

# 不相交
{1, 2}.isdisjoint({3, 4})  # True

# 實際應用：找出共同標籤
user1_tags = {"python", "javascript", "react"}
user2_tags = {"python", "django", "postgresql"}
common_tags = user1_tags & user2_tags  # {"python"}

# 找出某人獨有的技能
unique_to_user1 = user1_tags - user2_tags  # {"javascript", "react"}
```

對比 JavaScript：

```javascript
// JavaScript Set 操作需要手動實作
const union = new Set([...a, ...b]);
const intersection = new Set([...a].filter(x => b.has(x)));
const difference = new Set([...a].filter(x => !b.has(x)));
```

---

## 16.6 字典進階技巧

### 預設值處理

```python
user = {"name": "Alice"}

# get - 取得值或預設值
email = user.get("email", "N/A")  # "N/A"

# setdefault - 取得值，若不存在則設定
email = user.setdefault("email", "default@example.com")
# user 現在有 email 鍵

# defaultdict - 自動初始化
from collections import defaultdict

# 計數
word_count = defaultdict(int)
for word in ["a", "b", "a", "c", "a"]:
    word_count[word] += 1
# {'a': 3, 'b': 1, 'c': 1}

# 分組
groups = defaultdict(list)
items = [("a", 1), ("b", 2), ("a", 3)]
for key, value in items:
    groups[key].append(value)
# {'a': [1, 3], 'b': [2]}
```

### Counter - 計數器

```python
from collections import Counter

# 計數
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
counter = Counter(words)
# Counter({'apple': 3, 'banana': 2, 'cherry': 1})

# 最常見元素
counter.most_common(2)  # [('apple', 3), ('banana', 2)]

# 運算
c1 = Counter(a=3, b=1)
c2 = Counter(a=1, b=2)
c1 + c2  # Counter({'a': 4, 'b': 3})
c1 - c2  # Counter({'a': 2})

# 實際應用：字元頻率
text = "hello world"
char_freq = Counter(text)
# Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})
```

### OrderedDict 與字典順序

```python
# Python 3.7+ dict 保證插入順序
# OrderedDict 仍有其用途

from collections import OrderedDict

# move_to_end - 移動元素到末尾或開頭
od = OrderedDict([("a", 1), ("b", 2), ("c", 3)])
od.move_to_end("a")       # 移到末尾
od.move_to_end("c", last=False)  # 移到開頭

# LRU Cache 實作
class LRUCache(OrderedDict):
    def __init__(self, capacity):
        super().__init__()
        self.capacity = capacity

    def get(self, key):
        if key not in self:
            return -1
        self.move_to_end(key)
        return self[key]

    def put(self, key, value):
        if key in self:
            self.move_to_end(key)
        self[key] = value
        if len(self) > self.capacity:
            self.popitem(last=False)
```

### 字典合併

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}

# Python 3.9+ 使用 | 運算子
merged = dict1 | dict2  # {'a': 1, 'b': 3, 'c': 4}

# 原地更新
dict1 |= dict2

# 舊版本使用 ** 展開
merged = {**dict1, **dict2}

# update 方法
dict1.update(dict2)

# ChainMap - 不實際合併，建立視圖
from collections import ChainMap
combined = ChainMap(dict2, dict1)  # dict2 優先
combined["a"]  # 1（從 dict1）
combined["b"]  # 3（從 dict2）
```

---

## 16.7 實用工具函式

### functools 模組

```python
from functools import lru_cache, partial, reduce, wraps

# lru_cache - 快取函式結果
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

fibonacci(100)  # 瞬間完成

# 清除快取
fibonacci.cache_clear()

# 查看快取資訊
fibonacci.cache_info()

# partial - 部分應用
def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)
square(5)  # 25
cube(5)    # 125

# wraps - 保留原函式資訊（裝飾器中使用）
def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### itertools 模組

```python
from itertools import (
    chain,
    combinations,
    permutations,
    product,
    groupby,
    islice,
    cycle,
    repeat,
    count,
)

# chain - 串接多個迭代器
list(chain([1, 2], [3, 4], [5, 6]))  # [1, 2, 3, 4, 5, 6]

# combinations - 組合
list(combinations([1, 2, 3], 2))  # [(1, 2), (1, 3), (2, 3)]

# permutations - 排列
list(permutations([1, 2, 3], 2))  # [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]

# product - 笛卡爾積
list(product([1, 2], ["a", "b"]))  # [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

# groupby - 分組（需先排序）
data = [("A", 1), ("A", 2), ("B", 3), ("B", 4)]
for key, group in groupby(data, key=lambda x: x[0]):
    print(key, list(group))
# A [('A', 1), ('A', 2)]
# B [('B', 3), ('B', 4)]

# islice - 切片迭代器
list(islice(range(100), 5, 10))  # [5, 6, 7, 8, 9]

# cycle - 無限循環
colors = cycle(["red", "green", "blue"])
[next(colors) for _ in range(5)]  # ['red', 'green', 'blue', 'red', 'green']

# count - 無限計數
counter = count(start=10, step=2)
[next(counter) for _ in range(5)]  # [10, 12, 14, 16, 18]
```

### operator 模組

```python
from operator import itemgetter, attrgetter, methodcaller

# itemgetter - 取得項目
get_name = itemgetter("name")
get_name({"name": "Alice", "age": 30})  # "Alice"

# 多個項目
get_name_age = itemgetter("name", "age")
get_name_age({"name": "Alice", "age": 30})  # ("Alice", 30)

# 排序用法
users = [{"name": "Bob", "age": 25}, {"name": "Alice", "age": 30}]
sorted(users, key=itemgetter("age"))

# attrgetter - 取得屬性
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

users = [User("Bob", 25), User("Alice", 30)]
sorted(users, key=attrgetter("age"))

# 巢狀屬性
get_city = attrgetter("address.city")

# methodcaller - 呼叫方法
upper = methodcaller("upper")
upper("hello")  # "HELLO"

strip_and_lower = methodcaller("strip")
```

---

## 16.8 檔案與路徑處理

### pathlib - 現代路徑處理

```python
from pathlib import Path

# 建立路徑物件
p = Path("/home/user/documents")
p = Path.cwd()  # 當前目錄
p = Path.home()  # 家目錄

# 路徑操作
p = Path("/home/user")
new_path = p / "documents" / "file.txt"  # 使用 / 運算子

# 路徑資訊
p = Path("/home/user/documents/file.txt")
p.name        # "file.txt"
p.stem        # "file"
p.suffix      # ".txt"
p.parent      # Path("/home/user/documents")
p.parts       # ('/', 'home', 'user', 'documents', 'file.txt')

# 檢查
p.exists()     # 是否存在
p.is_file()    # 是否為檔案
p.is_dir()     # 是否為目錄

# 讀寫檔案
content = p.read_text(encoding="utf-8")
p.write_text("Hello", encoding="utf-8")
data = p.read_bytes()
p.write_bytes(b"Hello")

# 目錄操作
p.mkdir(parents=True, exist_ok=True)
list(p.iterdir())  # 列出目錄內容

# Glob 模式
list(Path(".").glob("*.py"))       # 當前目錄的 .py 檔
list(Path(".").rglob("*.py"))      # 遞迴搜尋所有 .py 檔
list(Path(".").glob("**/*.py"))    # 同上
```

對比 JavaScript：

```javascript
// Node.js
const path = require("path");
const fs = require("fs/promises");

const p = path.join("/home", "user", "file.txt");
path.basename(p);  // "file.txt"
path.dirname(p);   // "/home/user"
path.extname(p);   // ".txt"

await fs.readFile(p, "utf-8");
await fs.writeFile(p, "Hello");
```

### 暫存檔案

```python
import tempfile

# 暫存檔案
with tempfile.NamedTemporaryFile(mode="w", suffix=".txt", delete=False) as f:
    f.write("Hello")
    print(f.name)  # 暫存檔路徑

# 暫存目錄
with tempfile.TemporaryDirectory() as tmpdir:
    # 在暫存目錄中操作
    p = Path(tmpdir) / "test.txt"
    p.write_text("Hello")
# 離開時自動刪除目錄
```

---

## 練習題

### 練習 1：解構賦值

使用解構賦值處理以下資料：

```python
data = [
    ("Alice", 30, ["python", "javascript"]),
    ("Bob", 25, ["rust", "go", "python"]),
    ("Charlie", 35, ["java"]),
]

# 1. 印出每個人的名字和第一個技能
# 2. 找出所有人的所有技能（不重複）
```

**參考答案：**

```python
# 1. 印出名字和第一個技能
for name, age, skills in data:
    first_skill, *_ = skills
    print(f"{name}: {first_skill}")

# 2. 所有技能（不重複）
all_skills = set()
for _, _, skills in data:
    all_skills.update(skills)
print(all_skills)

# 或使用推導式
all_skills = {skill for _, _, skills in data for skill in skills}
```

### 練習 2：Context Manager

建立一個 context manager 來計算程式碼執行時間並記錄到檔案：

**參考答案：**

```python
from contextlib import contextmanager
import time
from pathlib import Path

@contextmanager
def timed_operation(name: str, log_file: str = "timing.log"):
    """計時並記錄到檔案。"""
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        log_entry = f"{name}: {elapsed:.4f}s\n"
        print(log_entry, end="")
        Path(log_file).open("a").write(log_entry)

# 使用
with timed_operation("Data processing"):
    time.sleep(0.5)
```

### 練習 3：資料處理

使用學到的技巧處理以下資料：

```python
sales = [
    {"product": "Apple", "category": "Fruit", "price": 1.5, "quantity": 100},
    {"product": "Banana", "category": "Fruit", "price": 0.8, "quantity": 150},
    {"product": "Carrot", "category": "Vegetable", "price": 1.2, "quantity": 80},
    {"product": "Broccoli", "category": "Vegetable", "price": 2.0, "quantity": 60},
]

# 1. 計算總銷售額
# 2. 按類別分組並計算各類別銷售額
# 3. 找出銷售額最高的商品
```

**參考答案：**

```python
from collections import defaultdict
from operator import itemgetter

# 1. 總銷售額
total = sum(item["price"] * item["quantity"] for item in sales)
print(f"Total: ${total:.2f}")

# 2. 按類別分組計算
category_sales = defaultdict(float)
for item in sales:
    revenue = item["price"] * item["quantity"]
    category_sales[item["category"]] += revenue
print(dict(category_sales))

# 3. 銷售額最高的商品
best = max(sales, key=lambda x: x["price"] * x["quantity"])
print(f"Best seller: {best['product']}")

# 或使用 itemgetter（需先計算）
sales_with_revenue = [
    {**item, "revenue": item["price"] * item["quantity"]}
    for item in sales
]
best = max(sales_with_revenue, key=itemgetter("revenue"))
```

---

## 總結

### JavaScript vs Python 實用技巧對照表

| 技巧 | JavaScript | Python |
|------|------------|--------|
| 陣列解構 | `const [a, b] = arr` | `a, b = arr` |
| 剩餘元素 | `const [a, ...rest] = arr` | `a, *rest = arr` |
| 物件解構 | `const {a, b} = obj` | `a, b = obj.values()` |
| 展開運算子 | `[...arr1, ...arr2]` | `[*arr1, *arr2]` |
| 字典展開 | `{...obj1, ...obj2}` | `{**obj1, **obj2}` |
| 資源管理 | try-finally | `with` 語句 |
| 帶索引迭代 | `arr.forEach((v, i) => ...)` | `for i, v in enumerate(arr)` |
| 並行迭代 | 手動或 lodash | `zip(arr1, arr2)` |
| 映射 | `arr.map(fn)` | `[fn(x) for x in arr]` |
| 過濾 | `arr.filter(fn)` | `[x for x in arr if fn(x)]` |
| 歸約 | `arr.reduce(fn, init)` | `reduce(fn, arr, init)` |
| 任一滿足 | `arr.some(fn)` | `any(fn(x) for x in arr)` |
| 全部滿足 | `arr.every(fn)` | `all(fn(x) for x in arr)` |
| 字串模板 | `` `${var}` `` | `f"{var}"` |
| 路徑處理 | `path.join()` | `Path() / "dir"` |

### 重點回顧

1. **解構賦值**：Python 的 `*` 運算子比 JavaScript 的 `...` 更靈活，可放在任何位置
2. **Context Manager**：使用 `with` 語句自動管理資源，比 try-finally 更優雅
3. **內建函式**：善用 `enumerate`、`zip`、`any`、`all` 等內建函式
4. **集合操作**：Python 的集合支援數學運算子（`|`、`&`、`-`）
5. **字典技巧**：`defaultdict` 和 `Counter` 大幅簡化計數和分組操作
6. **functools**：`lru_cache` 提供簡單的記憶化，`partial` 支援部分應用
7. **itertools**：提供強大的迭代器工具
8. **pathlib**：現代化的路徑處理，比字串操作更安全
