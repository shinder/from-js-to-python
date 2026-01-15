# 第 2 章：資料型別

本章節詳細說明 JavaScript 與 Python 在資料型別上的差異，包含基本型別對照、型別檢查方式，以及型別轉換的方法。

---

## 2.1 基本型別對照

### 型別對照總覽

| JavaScript | Python | 說明 |
|------------|--------|------|
| `number` | `int`, `float` | Python 區分整數與浮點數 |
| `bigint` | `int` | Python 的 int 原生支援大數 |
| `string` | `str` | 字串 |
| `boolean` | `bool` | 布林值（注意大小寫差異） |
| `null` | `None` | 空值 |
| `undefined` | 無對應 | Python 變數必須先賦值 |
| `symbol` | 無直接對應 | 可用其他方式模擬 |
| `object` | `dict`, `list`, `class` 等 | Python 有更多細分型別 |

---

### 2.1.1 數值型別（Number）

#### JavaScript 的數值

JavaScript 只有一種數值型別 `number`（IEEE 754 雙精度浮點數）：

```javascript
// JavaScript - 全部都是 number
const integer = 42;
const float = 3.14;
const negative = -10;

console.log(typeof 42);      // "number"
console.log(typeof 3.14);    // "number"

// 大數需要使用 BigInt
const big = 9007199254740991n;
console.log(typeof big);     // "bigint"
```

#### Python 的數值

Python 區分多種數值型別：

```python
# int - 整數（無大小限制）
integer = 42
negative = -10
big_number = 99999999999999999999999999999  # 原生支援大數

# float - 浮點數
pi = 3.14159
scientific = 1.5e10  # 科學記號：1.5 × 10^10

# complex - 複數（JavaScript 無對應）
c = 3 + 4j
print(c.real)  # 3.0
print(c.imag)  # 4.0

# 型別確認
print(type(42))       # <class 'int'>
print(type(3.14))     # <class 'float'>
print(type(3 + 4j))   # <class 'complex'>
```

#### 數值運算差異

```python
# 除法運算
print(10 / 3)     # 3.3333...（真除法，回傳 float）
print(10 // 3)    # 3（整數除法，回傳 int）
print(10 % 3)     # 1（取餘數）

# JavaScript 對照：
# 10 / 3   → 3.3333...
# Math.floor(10 / 3) → 3
# 10 % 3   → 1

# 次方運算
print(2 ** 10)    # 1024
print(pow(2, 10)) # 1024（內建函式）

# JavaScript 對照：
# 2 ** 10 或 Math.pow(2, 10)

# 大數運算（Python 原生支援）
huge = 10 ** 100
print(huge)  # 100 位數的數字，無精度損失

# JavaScript 需要 BigInt：
# const huge = 10n ** 100n;
```

#### 數值精度問題

```python
# 浮點數精度問題（與 JavaScript 相同）
print(0.1 + 0.2)           # 0.30000000000000004
print(0.1 + 0.2 == 0.3)    # False

# 解決方案 1：使用 round()
print(round(0.1 + 0.2, 1) == 0.3)  # True

# 解決方案 2：使用 decimal 模組（精確計算）
from decimal import Decimal, getcontext

getcontext().prec = 6  # 設定精度
a = Decimal("0.1")
b = Decimal("0.2")
print(a + b)           # 0.3（精確）

# 解決方案 3：使用 fractions 模組（分數）
from fractions import Fraction

f = Fraction(1, 3)
print(f)               # 1/3
print(float(f))        # 0.3333...
```

#### 特殊數值

```python
# 無限大
import math

inf = float("inf")
neg_inf = float("-inf")
print(inf > 99999999)     # True
print(math.isinf(inf))    # True

# NaN（Not a Number）
nan = float("nan")
print(math.isnan(nan))    # True
print(nan == nan)         # False（NaN 不等於自己）

# JavaScript 對照：
# Infinity, -Infinity, NaN
# Number.isNaN(), Number.isFinite()
```

---

### 2.1.2 布林值（Boolean）

#### 大小寫差異

```javascript
// JavaScript - 小寫
const yes = true;
const no = false;
```

```python
# Python - 首字母大寫
yes = True
no = False

# 常見錯誤
# true = True   # NameError: name 'true' is not defined
```

#### 布林運算子

| JavaScript | Python | 說明 |
|------------|--------|------|
| `&&` | `and` | 且 |
| `\|\|` | `or` | 或 |
| `!` | `not` | 非 |

```python
a = True
b = False

print(a and b)    # False
print(a or b)     # True
print(not a)      # False

# 短路求值（與 JavaScript 相同）
result = None or "default"     # "default"
result = "value" and "other"   # "other"
```

#### Truthy 與 Falsy 值

JavaScript 和 Python 對於「假值」的定義略有不同：

```python
# Python 的 Falsy 值
falsy_values = [
    False,      # 布林假值
    None,       # 空值
    0,          # 整數零
    0.0,        # 浮點數零
    0j,         # 複數零
    "",         # 空字串
    [],         # 空列表
    {},         # 空字典
    set(),      # 空集合
    (),         # 空元組
    range(0),   # 空範圍
]

# 測試 Falsy
for val in falsy_values:
    print(f"{repr(val):12} -> {bool(val)}")  # 全部印出 False

# 與 JavaScript 的差異
# JavaScript: [] 和 {} 是 truthy
# Python: [] 和 {} 是 falsy

# JavaScript:
# Boolean([])  → true
# Boolean({})  → true

# Python:
print(bool([]))   # False
print(bool({}))   # False
```

#### 布林值與整數

```python
# Python 中 bool 是 int 的子類別
print(isinstance(True, int))   # True
print(isinstance(False, int))  # True

# True 等於 1，False 等於 0
print(True == 1)    # True
print(False == 0)   # True

# 可以進行數學運算
print(True + True)  # 2
print(True * 10)    # 10
print(sum([True, True, False, True]))  # 3（計算 True 的數量）
```

---

### 2.1.3 空值（Null / None）

#### JavaScript 的 null 和 undefined

```javascript
// JavaScript 有兩種「空」
let a = null;       // 明確設為空
let b;              // undefined（未賦值）
let c = undefined;  // 明確設為 undefined

console.log(a == b);   // true（寬鬆相等）
console.log(a === b);  // false（嚴格相等）
```

#### Python 的 None

```python
# Python 只有 None
a = None

# 檢查是否為 None（推薦用 is）
if a is None:
    print("a 是 None")

if a is not None:
    print("a 不是 None")

# 避免使用 == 比較 None
if a == None:      # 可行但不推薦
    print("a 是 None")

# 原因：== 可能被覆寫
class Weird:
    def __eq__(self, other):
        return True

w = Weird()
print(w == None)   # True（但 w 不是 None）
print(w is None)   # False（正確結果）
```

#### 未定義變數

```python
# Python 不允許使用未定義的變數
# print(undefined_var)  # NameError: name 'undefined_var' is not defined

# 檢查變數是否存在
# 方法 1：使用 try-except
try:
    value = maybe_exists
except NameError:
    value = "default"

# 方法 2：檢查局部變數
if "var_name" in locals():
    print("變數存在於局部作用域")

# 方法 3：檢查全域變數
if "var_name" in globals():
    print("變數存在於全域作用域")
```

#### 函式預設回傳值

```python
# Python 函式沒有 return 或 return 無值時，回傳 None
def no_return():
    pass

def empty_return():
    return

result1 = no_return()
result2 = empty_return()

print(result1)  # None
print(result2)  # None

# JavaScript 對照：
# function noReturn() {}  → undefined
```

---

### 2.1.4 字串型別（String）

Python 與 JavaScript 的字串型別相似，主要差異在方法名稱和編碼處理。
詳細說明請參考第 1 章的 1.4 節。

```python
# Python 字串是不可變的（immutable）
s = "hello"
# s[0] = "H"  # TypeError: 'str' object does not support item assignment

# 需要建立新字串
s = "H" + s[1:]  # "Hello"

# 與 JavaScript 相同，字串也是不可變的
```

---

### 2.1.5 Symbol 的替代方案

JavaScript 的 Symbol 用於建立唯一識別符，Python 沒有直接對應，但有替代方案：

```python
# 方法 1：使用 object() 建立唯一物件
UNIQUE_KEY = object()

data = {
    UNIQUE_KEY: "這個 key 不會與其他 key 衝突"
}

# 方法 2：使用 Enum
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()
    APPROVED = auto()
    REJECTED = auto()

# 每個成員都是唯一的
print(Status.PENDING)           # Status.PENDING
print(Status.PENDING.value)     # 1
print(Status.PENDING == Status.APPROVED)  # False

# 方法 3：使用 UUID
import uuid

unique_id = uuid.uuid4()
print(unique_id)  # 例如：550e8400-e29b-41d4-a716-446655440000
```

---

## 2.2 型別檢查

### JavaScript 的型別檢查

```javascript
// typeof - 基本型別
console.log(typeof 42);          // "number"
console.log(typeof "hello");     // "string"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof null);        // "object"（歷史遺留 bug）
console.log(typeof {});          // "object"
console.log(typeof []);          // "object"

// instanceof - 物件型別
console.log([] instanceof Array);  // true
console.log({} instanceof Object); // true

// Array.isArray()
console.log(Array.isArray([]));    // true
```

### Python 的型別檢查

#### 使用 `type()`

```python
# type() 回傳物件的型別
print(type(42))           # <class 'int'>
print(type(3.14))         # <class 'float'>
print(type("hello"))      # <class 'str'>
print(type(True))         # <class 'bool'>
print(type(None))         # <class 'NoneType'>
print(type([]))           # <class 'list'>
print(type({}))           # <class 'dict'>
print(type(()))           # <class 'tuple'>
print(type({1, 2}))       # <class 'set'>

# 比較型別
print(type(42) == int)    # True
print(type("hi") == str)  # True
```

#### 使用 `isinstance()`（推薦）

```python
# isinstance() 檢查物件是否為特定型別（含子類別）
print(isinstance(42, int))          # True
print(isinstance(3.14, float))      # True
print(isinstance("hello", str))     # True
print(isinstance(True, bool))       # True
print(isinstance(True, int))        # True（bool 是 int 的子類別）

# 檢查多種型別
print(isinstance(42, (int, float)))     # True
print(isinstance("hi", (int, float)))   # False

# 實用範例：接受多種數值型別
def process_number(n):
    if not isinstance(n, (int, float)):
        raise TypeError("參數必須是數值")
    return n * 2
```

#### `type()` vs `isinstance()` 的差異

```python
class Animal:
    pass

class Dog(Animal):
    pass

dog = Dog()

# type() 只檢查確切型別
print(type(dog) == Dog)      # True
print(type(dog) == Animal)   # False（不是 Animal 類別）

# isinstance() 會檢查繼承鏈
print(isinstance(dog, Dog))     # True
print(isinstance(dog, Animal))  # True（是 Animal 的子類別）

# 一般建議使用 isinstance()
# 因為它符合物件導向的多型原則
```

#### 使用 `issubclass()`

```python
# 檢查類別之間的繼承關係
print(issubclass(bool, int))       # True
print(issubclass(Dog, Animal))     # True
print(issubclass(Dog, object))     # True（所有類別都繼承自 object）
```

#### 鴨子型別（Duck Typing）

Python 通常不強調型別檢查，而是採用「鴨子型別」：

```python
# 「如果它走起來像鴨子、叫起來像鴨子，那它就是鴨子」

def get_length(obj):
    # 不檢查型別，直接嘗試呼叫
    return len(obj)

# 只要物件支援 len()，就能使用
print(get_length("hello"))     # 5
print(get_length([1, 2, 3]))   # 3
print(get_length({"a": 1}))    # 1

# 使用 EAFP 原則（Easier to Ask Forgiveness than Permission）
def safe_get_length(obj):
    try:
        return len(obj)
    except TypeError:
        return None
```

#### 檢查物件是否有特定屬性或方法

```python
# hasattr() - 檢查是否有屬性
class Person:
    def __init__(self, name):
        self.name = name

    def greet(self):
        return f"Hi, I'm {self.name}"

person = Person("Alice")

print(hasattr(person, "name"))     # True
print(hasattr(person, "age"))      # False
print(hasattr(person, "greet"))    # True

# callable() - 檢查是否可呼叫
print(callable(person.greet))      # True
print(callable(person.name))       # False

# getattr() - 取得屬性（可設預設值）
name = getattr(person, "name", "Unknown")
age = getattr(person, "age", 0)
print(name)  # "Alice"
print(age)   # 0
```

---

## 2.3 型別轉換

### 基本型別轉換

#### 轉換為整數 `int()`

```python
# 從字串轉換
print(int("42"))        # 42
print(int("  42  "))    # 42（自動去除空白）
print(int("-17"))       # -17

# 從浮點數轉換（截斷，不是四捨五入）
print(int(3.7))         # 3
print(int(-3.7))        # -3
print(int(3.2))         # 3

# 從布林值轉換
print(int(True))        # 1
print(int(False))       # 0

# 指定進位制
print(int("1010", 2))   # 10（二進位）
print(int("ff", 16))    # 255（十六進位）
print(int("77", 8))     # 63（八進位）

# 錯誤情況
# int("3.14")           # ValueError: invalid literal
# int("hello")          # ValueError: invalid literal
# int(None)             # TypeError: int() argument must be a string...
```

#### 轉換為浮點數 `float()`

```python
# 從字串轉換
print(float("3.14"))      # 3.14
print(float("-2.5"))      # -2.5
print(float("1e10"))      # 10000000000.0
print(float("inf"))       # inf
print(float("-inf"))      # -inf
print(float("nan"))       # nan

# 從整數轉換
print(float(42))          # 42.0

# 從布林值轉換
print(float(True))        # 1.0
print(float(False))       # 0.0
```

#### 轉換為字串 `str()`

```python
# 幾乎所有物件都可以轉換為字串
print(str(42))            # "42"
print(str(3.14))          # "3.14"
print(str(True))          # "True"
print(str(False))         # "False"
print(str(None))          # "None"
print(str([1, 2, 3]))     # "[1, 2, 3]"
print(str({"a": 1}))      # "{'a': 1}"

# 使用 repr() 取得更精確的表示
print(repr("hello"))      # "'hello'"（包含引號）
print(repr([1, 2]))       # "[1, 2]"
```

#### 轉換為布林值 `bool()`

```python
# 數值
print(bool(0))        # False
print(bool(1))        # True
print(bool(-1))       # True
print(bool(0.0))      # False
print(bool(0.1))      # True

# 字串
print(bool(""))       # False
print(bool(" "))      # True（空白字元不是空字串）
print(bool("False"))  # True（非空字串都是 True）

# 容器
print(bool([]))       # False
print(bool([0]))      # True（有元素就是 True）
print(bool({}))       # False
print(bool({0: 0}))   # True

# None
print(bool(None))     # False
```

### 容器型別轉換

#### 轉換為列表 `list()`

```python
# 從字串
print(list("hello"))           # ['h', 'e', 'l', 'l', 'o']

# 從元組
print(list((1, 2, 3)))         # [1, 2, 3]

# 從集合
print(list({3, 1, 2}))         # [1, 2, 3]（順序可能不同）

# 從字典（取得 keys）
print(list({"a": 1, "b": 2}))  # ['a', 'b']

# 從 range
print(list(range(5)))          # [0, 1, 2, 3, 4]

# 從生成器
print(list(x * 2 for x in range(3)))  # [0, 2, 4]
```

#### 轉換為元組 `tuple()`

```python
# 從列表
print(tuple([1, 2, 3]))        # (1, 2, 3)

# 從字串
print(tuple("abc"))            # ('a', 'b', 'c')

# 從集合
print(tuple({1, 2, 3}))        # (1, 2, 3)
```

#### 轉換為集合 `set()`

```python
# 從列表（自動去除重複）
print(set([1, 2, 2, 3, 3, 3])) # {1, 2, 3}

# 從字串
print(set("hello"))            # {'h', 'e', 'l', 'o'}

# 從元組
print(set((1, 2, 3)))          # {1, 2, 3}
```

#### 轉換為字典 `dict()`

```python
# 從鍵值對列表
pairs = [("a", 1), ("b", 2)]
print(dict(pairs))             # {'a': 1, 'b': 2}

# 從兩個列表（使用 zip）
keys = ["name", "age"]
values = ["Alice", 30]
print(dict(zip(keys, values))) # {'name': 'Alice', 'age': 30}

# 從關鍵字參數
print(dict(name="Alice", age=30))  # {'name': 'Alice', 'age': 30}
```

### 安全的型別轉換

```python
def safe_int(value, default=0):
    """安全地轉換為整數"""
    try:
        return int(value)
    except (ValueError, TypeError):
        return default

def safe_float(value, default=0.0):
    """安全地轉換為浮點數"""
    try:
        return float(value)
    except (ValueError, TypeError):
        return default

# 使用範例
print(safe_int("42"))        # 42
print(safe_int("hello"))     # 0
print(safe_int(None, -1))    # -1

print(safe_float("3.14"))    # 3.14
print(safe_float("abc"))     # 0.0
```

### 隱式型別轉換

```python
# Python 在某些情況下會自動轉換型別

# 數值運算中的提升
print(1 + 2.0)          # 3.0（int + float → float）
print(True + 1)         # 2（bool + int → int）

# 字串格式化
print(f"Value: {42}")   # "Value: 42"（int 自動轉 str）

# 條件判斷
if []:                  # 空列表自動轉為 False
    print("不會執行")

if "hello":             # 非空字串自動轉為 True
    print("會執行")
```

### JavaScript vs Python 型別轉換對照

| 目的 | JavaScript | Python |
|------|------------|--------|
| 轉整數 | `parseInt(x)` | `int(x)` |
| 轉浮點數 | `parseFloat(x)` | `float(x)` |
| 轉字串 | `String(x)` 或 `x.toString()` | `str(x)` |
| 轉布林 | `Boolean(x)` 或 `!!x` | `bool(x)` |
| 轉陣列 | `Array.from(x)` 或 `[...x]` | `list(x)` |
| JSON 解析 | `JSON.parse(x)` | `json.loads(x)` |
| JSON 序列化 | `JSON.stringify(x)` | `json.dumps(x)` |

```python
import json

# JSON 轉換
data = {"name": "Alice", "age": 30}
json_str = json.dumps(data)           # '{"name": "Alice", "age": 30}'
parsed = json.loads(json_str)         # {'name': 'Alice', 'age': 30}

# 格式化輸出
pretty = json.dumps(data, indent=2, ensure_ascii=False)
print(pretty)
```

---

## 練習題

### 練習 1：型別判斷

寫一個函式，接受任意參數並回傳其型別名稱：

```python
def get_type_name(value):
    # 你的程式碼
    pass

# 預期結果
print(get_type_name(42))        # "int"
print(get_type_name(3.14))      # "float"
print(get_type_name("hello"))   # "str"
print(get_type_name([1, 2]))    # "list"
print(get_type_name(None))      # "NoneType"
```

### 練習 2：安全轉換

寫一個函式，將輸入安全地轉換為指定型別：

```python
def safe_convert(value, target_type, default=None):
    # 你的程式碼
    pass

# 預期結果
print(safe_convert("42", int))           # 42
print(safe_convert("abc", int, 0))       # 0
print(safe_convert("3.14", float))       # 3.14
print(safe_convert(123, str))            # "123"
```

### 練習 3：Falsy 值檢查

寫一個函式，檢查值是否為 Python 的 Falsy 值：

```python
def is_falsy(value):
    # 你的程式碼
    pass

# 預期結果
print(is_falsy(0))        # True
print(is_falsy(""))       # True
print(is_falsy([]))       # True
print(is_falsy(None))     # True
print(is_falsy(1))        # False
print(is_falsy("hello"))  # False
```

---

## 小結

### 型別系統差異

| 特性 | JavaScript | Python |
|------|------------|--------|
| 數值型別 | `number`, `bigint` | `int`, `float`, `complex` |
| 整數大小 | 有限制（需 BigInt） | 無限制 |
| 布林值 | `true`, `false` | `True`, `False` |
| 空值 | `null`, `undefined` | `None` |
| 空陣列/物件 | Truthy | Falsy |
| 型別檢查 | `typeof`, `instanceof` | `type()`, `isinstance()` |

### 型別轉換函式

| 目標型別 | Python 函式 | 注意事項 |
|----------|-------------|----------|
| 整數 | `int()` | 浮點數會截斷，非四捨五入 |
| 浮點數 | `float()` | 支援科學記號、inf、nan |
| 字串 | `str()` | 幾乎所有物件都可轉換 |
| 布林值 | `bool()` | 空容器為 False |
| 列表 | `list()` | 可從任何可迭代物件轉換 |
| 元組 | `tuple()` | 不可變序列 |
| 集合 | `set()` | 自動去除重複 |
| 字典 | `dict()` | 需要鍵值對結構 |

### 最佳實踐

1. **優先使用 `isinstance()` 而非 `type()` 進行型別檢查**
2. **使用 `is` 而非 `==` 比較 `None`**
3. **善用鴨子型別，減少不必要的型別檢查**
4. **進行型別轉換時，考慮使用 try-except 處理錯誤**
5. **注意空容器在 Python 中是 Falsy 值**
