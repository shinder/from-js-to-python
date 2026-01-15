# JavaScript 工程師學習 Python 大綱

本文件為熟悉 JavaScript 的開發者整理學習 Python 時應關注的主題與差異。

---

## 1. 基礎語法差異

### 1.1 縮排與程式碼區塊

- Python 使用**縮排**定義程式碼區塊，而非 `{}`
- 標準為 4 個空格（非 Tab）
- 沒有分號結尾

### 1.2 變數宣告

| JavaScript            | Python                            |
| --------------------- | --------------------------------- |
| `let`, `const`, `var` | 直接賦值，無需關鍵字              |
| `const PI = 3.14`     | `PI = 3.14`（慣例用大寫表示常數） |

### 1.3 註解

| JavaScript   | Python                      |
| ------------ | --------------------------- |
| `// 單行`    | `# 單行`                    |
| `/* 多行 */` | `""" 多行（docstring） """` |

### 1.4 字串

- 單引號、雙引號皆可，無差異
- f-string 格式化：`f"Hello, {name}"`（類似 JS 的模板字串）
- 多行字串：`"""` 或 `'''`

---

## 2. 資料型別

### 2.1 基本型別對照

| JavaScript                 | Python                   |
| -------------------------- | ------------------------ |
| `number`                   | `int`, `float`           |
| `string`                   | `str`                    |
| `boolean` (`true`/`false`) | `bool` (`True`/`False`)  |
| `null`                     | `None`                   |
| `undefined`                | 無對應（變數必須先賦值） |
| `Symbol`                   | 無直接對應               |

### 2.2 型別檢查

- JavaScript: `typeof x`
- Python: `type(x)` 或 `isinstance(x, int)`

### 2.3 型別轉換

```python
int("42")      # 字串轉整數
str(42)        # 整數轉字串
float("3.14")  # 字串轉浮點數
bool(0)        # 轉布林值
```

---

## 3. 資料結構

### 3.1 List（對應 JS Array）

```python
arr = [1, 2, 3]
arr.append(4)      # push
arr.pop()          # pop
arr[0]             # 取值
arr[-1]            # 取最後一個元素
arr[1:3]           # 切片（slice）
len(arr)           # 長度
```

### 3.2 Dictionary（對應 JS Object）

```python
obj = {"name": "Alice", "age": 30}
obj["name"]           # 取值
obj.get("name")       # 安全取值，不存在回傳 None
obj["email"] = "..."  # 新增/修改
del obj["age"]        # 刪除
obj.keys()            # 所有 key
obj.values()          # 所有 value
obj.items()           # key-value pairs
```

### 3.3 Tuple（不可變序列）

```python
point = (10, 20)  # 不可修改
x, y = point      # 解構賦值
```

### 3.4 Set（集合）

```python
s = {1, 2, 3}
s.add(4)
s.remove(1)
s1 & s2  # 交集
s1 | s2  # 聯集
```

---

## 4. 運算子差異

### 4.1 比較運算子

| JavaScript | Python                          |
| ---------- | ------------------------------- |
| `===`      | `==`（Python 預設就是嚴格比較） |
| `!==`      | `!=`                            |
| `&&`       | `and`                           |
| `||`       | `or`                            |
| `!`        | `not`                           |

### 4.2 其他運算子

```python
# 整數除法
10 // 3  # 結果為 3（JS 需用 Math.floor）

# 次方
2 ** 10  # 1024（同 JS）

# 成員檢查
"a" in "abc"     # True
1 in [1, 2, 3]   # True

# 身份檢查
x is None        # 檢查是否為同一物件
x is not None
```

### 4.3 三元運算子

```python
# JavaScript: condition ? a : b
# Python:
result = a if condition else b
```

---

## 5. 流程控制

### 5.1 條件判斷

```python
if x > 0:
    print("正數")
elif x < 0:
    print("負數")
else:
    print("零")
```

### 5.2 迴圈

```python
# for 迴圈（類似 for...of）
for item in items:
    print(item)

# 帶索引
for i, item in enumerate(items):
    print(i, item)

# range
for i in range(5):       # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):    # 1, 2, 3, 4, 5
    print(i)

# while 迴圈
while condition:
    # ...
```

### 5.3 迴圈控制

- `break`：跳出迴圈
- `continue`：跳過本次迭代
- `else`：迴圈正常結束時執行（Python 特有）

---

## 6. 函式

### 6.1 定義與呼叫

```python
def greet(name):
    return f"Hello, {name}"

# 呼叫
greet("Alice")
```

### 6.2 預設參數與關鍵字參數

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}"

greet("Alice")                    # Hello, Alice
greet("Alice", greeting="Hi")     # Hi, Alice（關鍵字參數）
```

### 6.3 可變參數

```python
def sum_all(*args):          # 類似 JS 的 ...args
    return sum(args)

def print_info(**kwargs):    # 接收關鍵字參數為 dict
    for key, value in kwargs.items():
        print(f"{key}: {value}")
```

### 6.4 Lambda 函式

```python
# JavaScript: (x) => x * 2
# Python:
double = lambda x: x * 2
```

### 6.5 一等公民函式

- 函式可作為參數傳遞、賦值給變數、作為回傳值

---

## 7. 類別與物件導向

### 7.1 類別定義

```python
class Person:
    # 建構子
    def __init__(self, name, age):
        self.name = name    # 實例屬性
        self.age = age

    # 實例方法（第一個參數必須是 self）
    def greet(self):
        return f"Hi, I'm {self.name}"

    # 類別方法
    @classmethod
    def from_dict(cls, data):
        return cls(data["name"], data["age"])

    # 靜態方法
    @staticmethod
    def is_adult(age):
        return age >= 18
```

### 7.2 繼承

```python
class Student(Person):
    def __init__(self, name, age, student_id):
        super().__init__(name, age)
        self.student_id = student_id
```

### 7.3 存取控制

- `_name`：慣例表示 protected（仍可存取）
- `__name`：name mangling，較難從外部存取
- Python 沒有真正的 private

### 7.4 特殊方法（Magic Methods）

```python
__str__      # 類似 toString()
__repr__     # 除錯用字串表示
__len__      # len() 呼叫時觸發
__eq__       # == 比較
__iter__     # 使物件可迭代
__getitem__  # 支援 [] 取值
```

---

## 8. 模組與套件

### 8.1 匯入方式

```python
import math                    # 匯入整個模組
from math import sqrt, pi      # 匯入特定項目
from math import *             # 匯入全部（不建議）
import numpy as np             # 別名
```

### 8.2 模組結構

```
my_package/
├── __init__.py    # 套件初始化檔案
├── module_a.py
└── module_b.py
```

### 8.3 相對匯入

```python
from . import module_a         # 同層
from .. import other_package   # 上層
```

### 8.4 常用標準函式庫

| 用途       | 模組                           |
| ---------- | ------------------------------ |
| 系統操作   | `os`, `sys`                    |
| 檔案路徑   | `pathlib`, `os.path`           |
| JSON       | `json`                         |
| 日期時間   | `datetime`                     |
| 正規表達式 | `re`                           |
| 數學運算   | `math`                         |
| 隨機數     | `random`                       |
| 網路請求   | `urllib`, `http`               |
| 並行處理   | `threading`, `multiprocessing` |

---

## 9. 錯誤處理

### 9.1 try-except（對應 try-catch）

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"錯誤：{e}")
except (TypeError, ValueError):
    print("型別或值錯誤")
except Exception as e:
    print(f"未知錯誤：{e}")
else:
    print("無錯誤時執行")
finally:
    print("一定會執行")
```

### 9.2 拋出例外

```python
raise ValueError("無效的值")
raise CustomError("自訂錯誤")
```

### 9.3 自訂例外

```python
class CustomError(Exception):
    pass
```

---

## 10. 非同步程式設計

### 10.1 async/await

```python
import asyncio

async def fetch_data():
    await asyncio.sleep(1)
    return "data"

async def main():
    result = await fetch_data()
    print(result)

# 執行
asyncio.run(main())
```

### 10.2 與 JavaScript 差異

- Python 需要事件迴圈（event loop）來執行
- 使用 `asyncio.run()` 啟動
- 沒有內建 Promise，使用 `asyncio.Future`
- `asyncio.gather()` 類似 `Promise.all()`

---

## 11. 列表推導式與生成器

### 11.1 列表推導式（List Comprehension）

```python
# JavaScript: arr.map(x => x * 2)
doubled = [x * 2 for x in arr]

# JavaScript: arr.filter(x => x > 0)
positive = [x for x in arr if x > 0]

# 結合 map 與 filter
result = [x * 2 for x in arr if x > 0]
```

### 11.2 字典推導式

```python
squared = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

### 11.3 生成器（Generator）

```python
# 生成器表達式（惰性求值）
gen = (x * 2 for x in range(1000000))

# 生成器函式
def countdown(n):
    while n > 0:
        yield n
        n -= 1
```

---

## 12. 裝飾器（Decorator）

### 12.1 基本用法

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"呼叫 {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_call
def say_hello():
    print("Hello!")
```

### 12.2 帶參數的裝飾器

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def greet():
    print("Hi!")
```

### 12.3 常用內建裝飾器

- `@property`：將方法變成屬性存取
- `@staticmethod`：靜態方法
- `@classmethod`：類別方法
- `@functools.wraps`：保留原函式資訊

---

## 13. 型別提示（Type Hints）

### 13.1 基本型別提示

```python
def greet(name: str) -> str:
    return f"Hello, {name}"

age: int = 25
names: list[str] = ["Alice", "Bob"]
scores: dict[str, int] = {"Alice": 100}
```

### 13.2 進階型別

```python
from typing import Optional, Union, Callable, Any

def find(id: int) -> Optional[str]:     # 可能回傳 None
    pass

def process(data: Union[str, bytes]):   # 多種型別
    pass

def apply(fn: Callable[[int], int]):    # 函式型別
    pass
```

### 13.3 型別檢查工具

- `mypy`：靜態型別檢查器
- `pyright`：Microsoft 開發的型別檢查器

---

## 14. 常用工具與生態系

### 14.1 套件管理

| JavaScript        | Python                            |
| ----------------- | --------------------------------- |
| npm / yarn / pnpm | pip / poetry / pipenv             |
| package.json      | requirements.txt / pyproject.toml |
| node_modules      | 虛擬環境（venv）                  |
| npx               | pipx                              |

### 14.2 虛擬環境

```bash
# 建立虛擬環境
python -m venv venv

# 啟用（macOS/Linux）
source venv/bin/activate

# 啟用（Windows）
venv\Scripts\activate

# 停用
deactivate
```

### 14.3 常用第三方套件

| 用途         | 套件                         |
| ------------ | ---------------------------- |
| HTTP 請求    | `requests`, `httpx`          |
| Web 框架     | `Flask`, `FastAPI`, `Django` |
| 測試         | `pytest`                     |
| 程式碼格式化 | `black`, `ruff`              |
| Linting      | `flake8`, `pylint`, `ruff`   |
| 資料處理     | `pandas`, `numpy`            |
| ORM          | `SQLAlchemy`                 |

### 14.4 執行環境差異

| JavaScript | Python                     |
| ---------- | -------------------------- |
| Node.js    | CPython（預設）、PyPy      |
| 瀏覽器     | 無直接對應（可用 Pyodide） |

---

## 15. 重要慣例與風格

### 15.1 命名慣例

| 類型      | JavaScript         | Python              |
| --------- | ------------------ | ------------------- |
| 變數/函式 | camelCase          | snake_case          |
| 類別      | PascalCase         | PascalCase          |
| 常數      | UPPER_CASE         | UPPER_CASE          |
| 私有成員  | `_name` 或 `#name` | `_name` 或 `__name` |

### 15.2 PEP 8 風格指南

- 每行最多 79 字元（或 88/120，視團隊規範）
- 使用 4 個空格縮排
- import 順序：標準函式庫 → 第三方 → 本地模組
- 類別之間空兩行，方法之間空一行

### 15.3 Docstring 文件字串

```python
def calculate_area(radius: float) -> float:
    """計算圓的面積。

    Args:
        radius: 圓的半徑

    Returns:
        圓的面積

    Raises:
        ValueError: 當半徑為負數時
    """
    if radius < 0:
        raise ValueError("半徑不能為負數")
    return 3.14159 * radius ** 2
```

---

## 16. 實用技巧

### 16.1 解構賦值

```python
# 列表解構
a, b, c = [1, 2, 3]
first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]

# 字典解構（使用 **）
def func(**kwargs):
    pass
```

### 16.2 Context Manager（with 語句）

```python
# 自動處理資源釋放
with open("file.txt", "r") as f:
    content = f.read()
# 離開 with 區塊自動關閉檔案
```

### 16.3 常用內建函式

```python
len()       # 長度
range()     # 數列
enumerate() # 帶索引迭代
zip()       # 並行迭代多個序列
map()       # 映射
filter()    # 過濾
sorted()    # 排序（回傳新列表）
reversed()  # 反轉
any()       # 任一為 True
all()       # 全部為 True
isinstance()# 型別檢查
dir()       # 列出物件屬性
help()      # 查看說明文件
```

---

## 17. 學習資源

### 17.1 官方文件

- [Python 官方文件](https://docs.python.org/3/)
- [Python 教學](https://docs.python.org/3/tutorial/)

### 17.2 推薦書籍

- 《Fluent Python》
- 《Effective Python》
- 《Python Cookbook》

### 17.3 練習平台

- [LeetCode](https://leetcode.com/)
- [Exercism Python Track](https://exercism.org/tracks/python)
- [Codewars](https://www.codewars.com/)

---

## 附錄：快速對照表

| 概念       | JavaScript           | Python                  |
| ---------- | -------------------- | ----------------------- |
| 輸出       | `console.log()`      | `print()`               |
| 陣列長度   | `arr.length`         | `len(arr)`              |
| 字串長度   | `str.length`         | `len(str)`              |
| 新增元素   | `arr.push(x)`        | `arr.append(x)`         |
| 合併陣列   | `[...a, ...b]`       | `a + b` 或 `[*a, *b]`   |
| 物件展開   | `{...obj}`           | `{**obj}`               |
| 空值       | `null`, `undefined`  | `None`                  |
| 布林值     | `true`, `false`      | `True`, `False`         |
| 邏輯運算   | `&&`, `\|\|`,`!`     | `and`, `or`, `not`      |
| 字串模板   | `` `${var}` ``       | `f"{var}"`              |
| 箭頭函式   | `(x) => x * 2`       | `lambda x: x * 2`       |
| 解構       | `const {a, b} = obj` | `a, b = list`           |
| 預設匯出   | `export default`     | 無（使用 `__all__`）    |
| 具名匯出   | `export { fn }`      | 直接 `from x import fn` |
| 非同步     | `Promise`            | `asyncio.Future`        |
| await 多個 | `Promise.all()`      | `asyncio.gather()`      |
