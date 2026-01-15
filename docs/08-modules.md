# 第 8 章：模組與套件

本章節詳細說明 Python 的模組系統、套件結構、匯入方式，以及常用的標準函式庫。

---

## 8.1 匯入方式

### JavaScript vs Python 匯入對照

| JavaScript                   | Python                 | 說明                 |
| ---------------------------- | ---------------------- | -------------------- |
| `import x from 'mod'`        | `from mod import x`    | 匯入特定項目         |
| `import { a, b } from 'mod'` | `from mod import a, b` | 匯入多個項目         |
| `import * as mod from 'mod'` | `import mod`           | 匯入整個模組         |
| `import 'mod'`               | `import mod`           | 匯入模組（副作用）   |
| `export default x`           | 無直接對應             | Python 無預設匯出    |
| `export { x, y }`            | 自動匯出所有公開成員   | Python 不需要 export |

### import 語句

```python
# 匯入整個模組
import math

# 使用時需要加上模組名稱
print(math.pi)          # 3.141592653589793
print(math.sqrt(16))    # 4.0
print(math.sin(0))      # 0.0

# 匯入多個模組
import os
import sys
import json

# 或在同一行（不推薦，可讀性較差）
import os, sys, json
```

### from ... import 語句

```python
# 匯入特定項目
from math import pi, sqrt, sin

# 使用時不需要模組名稱
print(pi)          # 3.141592653589793
print(sqrt(16))    # 4.0

# 匯入所有公開成員（不推薦）
from math import *

# 為什麼不推薦 import *：
# 1. 可能覆蓋現有變數
# 2. 難以追蹤名稱來源
# 3. 增加命名衝突風險

# 例外：在互動式環境中方便測試
# 或模組明確設計為 import *（如 tkinter）
```

### 匯入別名（as）

```python
# 模組別名
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 使用別名
arr = np.array([1, 2, 3])
df = pd.DataFrame({"a": [1, 2], "b": [3, 4]})

# 項目別名
from datetime import datetime as dt
from collections import defaultdict as dd

now = dt.now()
counts = dd(int)

# 解決命名衝突
from module_a import helper as helper_a
from module_b import helper as helper_b

# JavaScript 對照
# import { helper as helperA } from './module_a';
# import numpy as np 類似 import * as np from 'numpy'
```

### 條件匯入

```python
# 根據條件匯入不同模組
import sys

if sys.version_info >= (3, 11):
    import tomllib
else:
    import tomli as tomllib

# 匯入可選模組
try:
    import numpy as np
    HAS_NUMPY = True
except ImportError:
    HAS_NUMPY = False

def process_array(data):
    if HAS_NUMPY:
        return np.array(data)
    return list(data)

# 延遲匯入（改善啟動時間）
def heavy_computation():
    import pandas as pd  # 只在需要時才匯入
    # ...
```

### 匯入的搜尋路徑

```python
import sys

# 查看模組搜尋路徑
print(sys.path)
# [
#     '',                              # 當前目錄
#     '/usr/lib/python3.x',           # 標準函式庫
#     '/usr/lib/python3.x/site-packages',  # 第三方套件
#     ...
# ]

# 動態新增搜尋路徑
sys.path.insert(0, '/path/to/my/modules')

# 查看模組位置
import os
print(os.__file__)  # /usr/lib/python3.x/os.py

# 查看模組的所有屬性
print(dir(os))
```

---

## 8.2 模組結構

### 單一檔案模組

```python
# mymodule.py

"""
這是模組的 docstring。
說明模組的用途和使用方式。
"""

# 模組級別的變數
VERSION = "1.0.0"
_private_var = "內部使用"

# 模組級別的函式
def greet(name):
    """問候函式"""
    return f"Hello, {name}!"

def _private_function():
    """內部函式"""
    pass

# 類別
class MyClass:
    """範例類別"""
    pass

# __all__ 定義 import * 時匯出的名稱
__all__ = ["VERSION", "greet", "MyClass"]
```

```python
# main.py

# 匯入模組
import mymodule

print(mymodule.VERSION)        # "1.0.0"
print(mymodule.greet("Alice")) # "Hello, Alice!"

# from import
from mymodule import greet, MyClass

# import * 只會匯入 __all__ 中的項目
from mymodule import *
print(VERSION)      # "1.0.0"
print(greet)        # <function greet>
# print(_private_var)  # NameError（不在 __all__ 中）
```

### 套件結構

套件是包含 `__init__.py` 的目錄：

```
my_package/
├── __init__.py          # 套件初始化檔案
├── module_a.py          # 子模組 A
├── module_b.py          # 子模組 B
├── subpackage/          # 子套件
│   ├── __init__.py
│   └── module_c.py
└── utils/               # 工具子套件
    ├── __init__.py
    ├── helpers.py
    └── validators.py
```

### `__init__.py` 檔案

```python
# my_package/__init__.py

"""
My Package - 套件說明

這個套件提供 XXX 功能。
"""

# 套件版本
__version__ = "1.0.0"

# 從子模組匯入，讓使用者可以直接從套件匯入
from .module_a import function_a, ClassA
from .module_b import function_b

# 定義公開 API
__all__ = [
    "function_a",
    "function_b",
    "ClassA",
    "__version__",
]

# 套件初始化程式碼
print(f"載入 my_package v{__version__}")
```

```python
# 使用套件
import my_package

print(my_package.__version__)
my_package.function_a()

# 或直接匯入
from my_package import function_a, ClassA
```

### 子模組範例

```python
# my_package/module_a.py

"""Module A 的功能"""

def function_a():
    return "來自 module_a"

class ClassA:
    pass
```

```python
# my_package/subpackage/__init__.py

from .module_c import function_c

__all__ = ["function_c"]
```

```python
# my_package/subpackage/module_c.py

def function_c():
    return "來自 subpackage.module_c"
```

### 匯入子模組

```python
# 方式 1：匯入子模組
import my_package.module_a
my_package.module_a.function_a()

# 方式 2：from 匯入
from my_package import module_a
module_a.function_a()

# 方式 3：直接匯入函式
from my_package.module_a import function_a
function_a()

# 方式 4：匯入子套件
from my_package.subpackage import function_c
function_c()

# 方式 5：深層匯入
from my_package.subpackage.module_c import function_c
```

---

## 8.3 相對匯入與絕對匯入

### 絕對匯入

```python
# 使用完整的套件路徑
from my_package.module_a import function_a
from my_package.subpackage.module_c import function_c
from my_package.utils.helpers import helper_function
```

### 相對匯入

在套件內部使用，以 `.` 開頭：

```python
# my_package/module_b.py

# . 代表當前套件
from . import module_a
from .module_a import function_a

# .. 代表父套件
from .. import other_package
from ..other_package import something

# 子套件
from .subpackage import function_c
from .utils.helpers import helper_function
```

### 相對匯入的規則

```python
# my_package/subpackage/module_c.py

# . 當前目錄（subpackage）
from . import another_module      # subpackage/another_module.py
from .another_module import func

# .. 上一層目錄（my_package）
from .. import module_a           # my_package/module_a.py
from ..module_a import function_a

# ... 上兩層目錄
from ... import top_level_module

# 注意：相對匯入只能在套件內使用
# 直接執行的腳本不能使用相對匯入
# python my_package/module_b.py  # 會失敗
# python -m my_package.module_b  # 可以
```

### `__name__` 與 `__main__`

```python
# my_module.py

def main():
    print("主程式執行")

def helper():
    print("輔助函式")

# 只在直接執行時運行
if __name__ == "__main__":
    main()

# 當模組被匯入時，__name__ 是模組名稱
# 當模組直接執行時，__name__ 是 "__main__"
```

### 套件的 `__main__.py`

```txt
my_package/
├── __init__.py
├── __main__.py      # python -m my_package 時執行
└── core.py
```

```python
# my_package/__main__.py

"""
讓套件可以用 python -m my_package 執行
"""

from .core import main

if __name__ == "__main__":
    main()
```

```bash
# 執行套件
python -m my_package
```

---

## 8.4 常用標準函式庫

### os - 作業系統介面

```python
import os

# 環境變數
print(os.environ.get("HOME"))
print(os.environ.get("PATH"))
os.environ["MY_VAR"] = "value"

# 當前工作目錄
print(os.getcwd())
os.chdir("/path/to/dir")

# 目錄操作
os.mkdir("new_dir")
os.makedirs("path/to/new/dir", exist_ok=True)
os.rmdir("empty_dir")
os.removedirs("path/to/empty/dirs")

# 檔案操作
os.rename("old.txt", "new.txt")
os.remove("file.txt")
os.path.exists("file.txt")

# 列出目錄內容
print(os.listdir("."))
for entry in os.scandir("."):
    print(entry.name, entry.is_file(), entry.is_dir())

# 執行系統命令（建議使用 subprocess）
os.system("ls -la")
```

### os.path - 路徑操作

```python
import os.path

# 路徑組合
path = os.path.join("dir", "subdir", "file.txt")
print(path)  # "dir/subdir/file.txt"

# 分解路徑
print(os.path.dirname("/path/to/file.txt"))   # "/path/to"
print(os.path.basename("/path/to/file.txt"))  # "file.txt"
print(os.path.split("/path/to/file.txt"))     # ("/path/to", "file.txt")

# 副檔名
print(os.path.splitext("file.txt"))  # ("file", ".txt")

# 絕對路徑
print(os.path.abspath("file.txt"))

# 檢查
print(os.path.exists("/path/to/file"))
print(os.path.isfile("/path/to/file"))
print(os.path.isdir("/path/to/dir"))

# 檔案資訊
print(os.path.getsize("file.txt"))     # 檔案大小（bytes）
print(os.path.getmtime("file.txt"))    # 修改時間（timestamp）
```

### pathlib - 現代路徑操作（推薦）

```python
from pathlib import Path

# 建立 Path 物件
p = Path("/path/to/file.txt")
p = Path.cwd()  # 當前目錄
p = Path.home()  # 家目錄

# 路徑組合（使用 / 運算子）
config_path = Path.home() / ".config" / "myapp" / "config.json"

# 路徑資訊
print(p.name)        # 檔案名稱
print(p.stem)        # 不含副檔名的檔案名稱
print(p.suffix)      # 副檔名
print(p.parent)      # 父目錄
print(p.parts)       # 路徑各部分的元組
print(p.absolute())  # 絕對路徑

# 檢查
print(p.exists())
print(p.is_file())
print(p.is_dir())

# 目錄操作
p.mkdir(parents=True, exist_ok=True)  # 建立目錄
p.rmdir()  # 刪除空目錄

# 檔案操作
p.touch()  # 建立空檔案
p.unlink()  # 刪除檔案
p.rename("new_name.txt")

# 讀寫檔案
content = p.read_text(encoding="utf-8")
p.write_text("Hello", encoding="utf-8")
data = p.read_bytes()
p.write_bytes(b"binary data")

# 遍歷目錄
for item in Path(".").iterdir():
    print(item)

# Glob 模式匹配
for py_file in Path(".").glob("*.py"):
    print(py_file)

for py_file in Path(".").rglob("*.py"):  # 遞迴
    print(py_file)
```

### sys - 系統特定參數

```python
import sys

# Python 版本
print(sys.version)
print(sys.version_info)
# sys.version_info(major=3, minor=11, micro=0, ...)

if sys.version_info >= (3, 10):
    # 使用 Python 3.10+ 的功能
    pass

# 命令列參數
print(sys.argv)  # ['script.py', 'arg1', 'arg2']

# 模組搜尋路徑
print(sys.path)
sys.path.insert(0, "/custom/path")

# 標準輸入輸出
sys.stdout.write("輸出到標準輸出\n")
sys.stderr.write("輸出到標準錯誤\n")

# 退出程式
sys.exit(0)  # 正常退出
sys.exit(1)  # 錯誤退出
sys.exit("錯誤訊息")

# 遞迴限制
print(sys.getrecursionlimit())  # 預設 1000
sys.setrecursionlimit(2000)

# 物件大小
print(sys.getsizeof([1, 2, 3]))  # bytes
```

### json - JSON 編解碼

```python
import json

# Python 物件轉 JSON 字串
data = {
    "name": "Alice",
    "age": 30,
    "cities": ["Taipei", "Tokyo"],
    "active": True,
    "score": None
}

json_str = json.dumps(data)
print(json_str)
# {"name": "Alice", "age": 30, "cities": ["Taipei", "Tokyo"], "active": true, "score": null}

# 格式化輸出
json_str = json.dumps(data, indent=2, ensure_ascii=False)
print(json_str)

# JSON 字串轉 Python 物件
json_str = '{"name": "Bob", "age": 25}'
obj = json.loads(json_str)
print(obj["name"])  # "Bob"

# 讀寫 JSON 檔案
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=2, ensure_ascii=False)

with open("data.json", "r", encoding="utf-8") as f:
    loaded_data = json.load(f)

# 自訂編碼
from datetime import datetime

class CustomEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        return super().default(obj)

data = {"timestamp": datetime.now()}
json_str = json.dumps(data, cls=CustomEncoder)
```

### datetime - 日期時間

```python
from datetime import datetime, date, time, timedelta, timezone

# 當前時間
now = datetime.now()
utc_now = datetime.now(timezone.utc)
print(now)  # 2024-01-15 10:30:45.123456

# 建立特定日期時間
dt = datetime(2024, 1, 15, 10, 30, 45)
d = date(2024, 1, 15)
t = time(10, 30, 45)

# 從字串解析
dt = datetime.strptime("2024-01-15 10:30:45", "%Y-%m-%d %H:%M:%S")
d = date.fromisoformat("2024-01-15")

# 格式化為字串
print(now.strftime("%Y-%m-%d %H:%M:%S"))
print(now.isoformat())

# 取得各部分
print(now.year, now.month, now.day)
print(now.hour, now.minute, now.second)
print(now.weekday())  # 0=Monday, 6=Sunday

# 時間計算
tomorrow = now + timedelta(days=1)
next_week = now + timedelta(weeks=1)
two_hours_later = now + timedelta(hours=2)

# 時間差
diff = datetime(2024, 12, 31) - datetime(2024, 1, 1)
print(diff.days)  # 365

# 時區
import zoneinfo  # Python 3.9+
taipei_tz = zoneinfo.ZoneInfo("Asia/Taipei")
taipei_time = datetime.now(taipei_tz)
```

### re - 正規表達式

```python
import re

text = "Email: alice@example.com, Phone: 123-456-7890"

# 搜尋
match = re.search(r"\d{3}-\d{3}-\d{4}", text)
if match:
    print(match.group())  # "123-456-7890"

# 找出所有匹配
emails = re.findall(r"[\w.-]+@[\w.-]+", text)
print(emails)  # ["alice@example.com"]

# 取代
new_text = re.sub(r"\d", "X", text)
print(new_text)  # "Email: alice@example.com, Phone: XXX-XXX-XXXX"

# 分割
parts = re.split(r"[,;]\s*", "a, b; c, d")
print(parts)  # ["a", "b", "c", "d"]

# 編譯正規表達式（重複使用時效率較高）
pattern = re.compile(r"\d{3}-\d{3}-\d{4}")
matches = pattern.findall(text)

# 群組
pattern = re.compile(r"(\w+)@(\w+)\.(\w+)")
match = pattern.search("alice@example.com")
if match:
    print(match.group())   # "alice@example.com"
    print(match.group(1))  # "alice"
    print(match.group(2))  # "example"
    print(match.groups())  # ("alice", "example", "com")

# 命名群組
pattern = re.compile(r"(?P<user>\w+)@(?P<domain>\w+\.\w+)")
match = pattern.search("alice@example.com")
if match:
    print(match.group("user"))    # "alice"
    print(match.group("domain"))  # "example.com"
    print(match.groupdict())      # {"user": "alice", "domain": "example.com"}
```

### collections - 容器資料型別

```python
from collections import (
    namedtuple, deque, Counter, OrderedDict, defaultdict, ChainMap
)

# namedtuple - 具名元組
Point = namedtuple("Point", ["x", "y"])
p = Point(10, 20)
print(p.x, p.y)

# deque - 雙端佇列
dq = deque([1, 2, 3])
dq.append(4)       # 右邊加入
dq.appendleft(0)   # 左邊加入
dq.pop()           # 右邊移除
dq.popleft()       # 左邊移除
dq.rotate(1)       # 旋轉

# Counter - 計數器
counter = Counter("abracadabra")
print(counter)  # Counter({"a": 5, "b": 2, "r": 2, "c": 1, "d": 1})
print(counter.most_common(2))  # [("a", 5), ("b", 2)]

# defaultdict - 預設值字典
dd = defaultdict(list)
dd["a"].append(1)
dd["a"].append(2)
dd["b"].append(3)
print(dict(dd))  # {"a": [1, 2], "b": [3]}

dd = defaultdict(int)
for char in "hello":
    dd[char] += 1

# OrderedDict - 有序字典（Python 3.7+ dict 已有序）
od = OrderedDict()
od["a"] = 1
od["b"] = 2
od.move_to_end("a")  # 移到最後

# ChainMap - 鏈式字典
defaults = {"color": "blue", "size": "medium"}
custom = {"color": "red"}
combined = ChainMap(custom, defaults)
print(combined["color"])  # "red"（從 custom）
print(combined["size"])   # "medium"（從 defaults）
```

### itertools - 迭代工具

```python
from itertools import (
    count, cycle, repeat,
    chain, compress, islice,
    groupby, permutations, combinations, product
)

# 無限迭代器
for i in count(10, 2):  # 10, 12, 14, ...
    if i > 20:
        break
    print(i)

# cycle - 循環迭代
colors = cycle(["red", "green", "blue"])
for _ in range(5):
    print(next(colors))

# chain - 串接多個迭代器
combined = chain([1, 2], [3, 4], [5, 6])
print(list(combined))  # [1, 2, 3, 4, 5, 6]

# islice - 切片迭代器
result = islice(range(100), 5, 15, 2)
print(list(result))  # [5, 7, 9, 11, 13]

# groupby - 分組
data = [("a", 1), ("a", 2), ("b", 3), ("b", 4)]
for key, group in groupby(data, key=lambda x: x[0]):
    print(key, list(group))
# a [("a", 1), ("a", 2)]
# b [("b", 3), ("b", 4)]

# permutations - 排列
print(list(permutations([1, 2, 3], 2)))
# [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]

# combinations - 組合
print(list(combinations([1, 2, 3], 2)))
# [(1, 2), (1, 3), (2, 3)]

# product - 笛卡爾積
print(list(product([1, 2], ["a", "b"])))
# [(1, "a"), (1, "b"), (2, "a"), (2, "b")]
```

### functools - 函式工具

```python
from functools import (
    partial, reduce, lru_cache, cache, wraps, total_ordering
)

# partial - 部分應用
def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)
print(square(5))  # 25
print(cube(5))    # 125

# reduce - 累積運算
from functools import reduce
result = reduce(lambda acc, x: acc + x, [1, 2, 3, 4, 5], 0)
print(result)  # 15

# lru_cache - 快取裝飾器
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(100))  # 快速計算

# cache - 無限制快取（Python 3.9+）
@cache
def expensive_function(x):
    print(f"計算 {x}...")
    return x ** 2

# wraps - 保留函式資訊的裝飾器
def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

# total_ordering - 自動生成比較方法
@total_ordering
class Student:
    def __init__(self, grade):
        self.grade = grade

    def __eq__(self, other):
        return self.grade == other.grade

    def __lt__(self, other):
        return self.grade < other.grade
```

### subprocess - 子程序管理

```python
import subprocess

# 執行命令並取得輸出
result = subprocess.run(
    ["ls", "-la"],
    capture_output=True,
    text=True
)
print(result.stdout)
print(result.returncode)

# 執行命令並檢查錯誤
try:
    subprocess.run(["false"], check=True)
except subprocess.CalledProcessError as e:
    print(f"命令失敗，返回碼：{e.returncode}")

# 傳遞輸入
result = subprocess.run(
    ["grep", "hello"],
    input="hello world\nfoo bar\nhello again",
    capture_output=True,
    text=True
)
print(result.stdout)

# 使用 shell（注意安全性）
result = subprocess.run(
    "ls -la | grep py",
    shell=True,
    capture_output=True,
    text=True
)

# Popen - 更細緻的控制
process = subprocess.Popen(
    ["python", "-c", "print('hello')"],
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE
)
stdout, stderr = process.communicate()
```

### logging - 日誌記錄

```python
import logging

# 基本設定
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    filename="app.log"
)

# 取得 logger
logger = logging.getLogger(__name__)

# 記錄訊息
logger.debug("除錯訊息")
logger.info("一般資訊")
logger.warning("警告訊息")
logger.error("錯誤訊息")
logger.critical("嚴重錯誤")

# 例外記錄
try:
    1 / 0
except Exception:
    logger.exception("發生例外")

# 進階設定
logger = logging.getLogger("myapp")
logger.setLevel(logging.DEBUG)

# 處理器（Handler）
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)

file_handler = logging.FileHandler("app.log")
file_handler.setLevel(logging.DEBUG)

# 格式器（Formatter）
formatter = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
console_handler.setFormatter(formatter)
file_handler.setFormatter(formatter)

# 加入處理器
logger.addHandler(console_handler)
logger.addHandler(file_handler)
```

### argparse - 命令列參數解析

```python
import argparse

# 建立解析器
parser = argparse.ArgumentParser(
    description="程式說明"
)

# 位置參數
parser.add_argument("filename", help="輸入檔案")

# 選項參數
parser.add_argument("-o", "--output", help="輸出檔案")
parser.add_argument("-v", "--verbose", action="store_true", help="詳細輸出")
parser.add_argument("-n", "--number", type=int, default=10, help="數量")
parser.add_argument("--mode", choices=["fast", "slow"], default="fast")

# 解析參數
args = parser.parse_args()

print(args.filename)
print(args.output)
print(args.verbose)
print(args.number)

# 使用範例
# python script.py input.txt -o output.txt -v -n 20
```

---

## 8.5 常用第三方套件

### 套件管理

```bash
# pip - 套件安裝工具
pip install requests
pip install numpy pandas
pip install package==1.0.0  # 指定版本
pip install package>=1.0.0  # 最低版本

# 升級套件
pip install --upgrade package

# 移除套件
pip uninstall package

# 列出已安裝套件
pip list
pip freeze > requirements.txt

# 從 requirements.txt 安裝
pip install -r requirements.txt
```

### 虛擬環境

```bash
# 建立虛擬環境
python -m venv venv

# 啟用（macOS/Linux）
source venv/bin/activate

# 啟用（Windows）
venv\Scripts\activate

# 停用
deactivate

# 在虛擬環境中安裝套件
pip install requests
```

### 常用第三方套件列表

| 用途         | 套件            | 說明                      |
| ------------ | --------------- | ------------------------- |
| HTTP 請求    | `requests`      | 簡單的 HTTP 函式庫        |
| HTTP 客戶端  | `httpx`         | 支援 async 的 HTTP 函式庫 |
| Web 框架     | `Flask`         | 輕量級 Web 框架           |
| Web 框架     | `FastAPI`       | 現代 async Web 框架       |
| Web 框架     | `Django`        | 全功能 Web 框架           |
| 測試         | `pytest`        | 測試框架                  |
| 程式碼格式化 | `black`         | 程式碼格式化工具          |
| Linting      | `ruff`          | 快速的 linter             |
| 型別檢查     | `mypy`          | 靜態型別檢查              |
| 資料處理     | `pandas`        | 資料分析                  |
| 數值計算     | `numpy`         | 數值計算                  |
| ORM          | `SQLAlchemy`    | 資料庫 ORM                |
| 環境變數     | `python-dotenv` | 載入 .env 檔案            |

---

## 練習題

### 練習 1：建立套件

建立一個計算工具套件：

```txt
calculator/
├── __init__.py
├── basic.py      # add, subtract, multiply, divide
├── advanced.py   # power, sqrt, factorial
└── utils.py      # 輔助函式
```

### 練習 2：使用標準函式庫

寫一個腳本，接收命令列參數，讀取 JSON 檔案，處理資料後輸出結果。

### 練習 3：日期時間處理

寫一個函式，計算兩個日期之間的工作天數（排除週末）。

---

## 小結

### 匯入方式對照

| 方式         | 語法                      | 使用情境               |
| ------------ | ------------------------- | ---------------------- |
| 匯入模組     | `import module`           | 需要使用模組的多個功能 |
| 匯入特定項目 | `from module import item` | 只需要少數幾個功能     |
| 匯入並取別名 | `import module as m`      | 模組名稱太長           |
| 相對匯入     | `from . import module`    | 套件內部匯入           |

### 常用標準函式庫

| 類別      | 模組                                      |
| --------- | ----------------------------------------- |
| 檔案/路徑 | `os`, `pathlib`, `shutil`                 |
| 系統      | `sys`, `subprocess`                       |
| 資料格式  | `json`, `csv`, `xml`                      |
| 日期時間  | `datetime`, `time`, `calendar`            |
| 文字處理  | `re`, `string`, `textwrap`                |
| 資料結構  | `collections`, `heapq`, `bisect`          |
| 函式工具  | `functools`, `itertools`, `operator`      |
| 網路      | `urllib`, `http`, `socket`                |
| 並行處理  | `threading`, `multiprocessing`, `asyncio` |
| 測試      | `unittest`, `doctest`                     |
| 日誌      | `logging`                                 |
| 命令列    | `argparse`                                |

### JavaScript vs Python 匯入

```javascript
// JavaScript
import module from 'module';
import { a, b } from 'module';
import * as mod from 'module';
export default x;
export { a, b };
```

```python
# Python
import module
from module import a, b
import module as mod
# 無 export，所有公開成員自動可匯入
# 使用 __all__ 控制 import *
```
