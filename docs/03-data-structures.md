# 第 3 章：資料結構

本章節詳細說明 Python 的四種主要資料結構：List、Dictionary、Tuple 和 Set，並與 JavaScript 的對應結構進行比較。

---

## 3.1 List（列表）

Python 的 List 對應 JavaScript 的 Array，是最常用的資料結構之一。

### 建立列表

```python
# 基本建立方式
empty = []
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", True, None, 3.14]  # 可混合型別

# 使用 list() 建構子
from_string = list("hello")    # ['h', 'e', 'l', 'l', 'o']
from_range = list(range(5))    # [0, 1, 2, 3, 4]
from_tuple = list((1, 2, 3))   # [1, 2, 3]

# 列表推導式（後面章節會詳細說明）
squares = [x ** 2 for x in range(5)]  # [0, 1, 4, 9, 16]

# 重複元素
zeros = [0] * 5                # [0, 0, 0, 0, 0]
pattern = [1, 2] * 3           # [1, 2, 1, 2, 1, 2]
```

### 存取元素

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

# 正向索引（從 0 開始）
print(fruits[0])    # "apple"
print(fruits[2])    # "cherry"

# 負向索引（從 -1 開始，表示最後一個）
print(fruits[-1])   # "elderberry"
print(fruits[-2])   # "date"

# 索引超出範圍會報錯
# print(fruits[10])   # IndexError: list index out of range

# JavaScript 對照：
# fruits[10] → undefined（不會報錯）
```

### 切片（Slicing）

切片是 Python 的強大特性，JavaScript 沒有直接對應語法。

```python
nums = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# 基本切片 [start:end]（不包含 end）
print(nums[2:5])      # [2, 3, 4]
print(nums[0:3])      # [0, 1, 2]

# 省略 start 或 end
print(nums[:4])       # [0, 1, 2, 3]（從開頭到索引 4）
print(nums[6:])       # [6, 7, 8, 9]（從索引 6 到結尾）
print(nums[:])        # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]（完整複製）

# 負向索引切片
print(nums[-3:])      # [7, 8, 9]（最後 3 個）
print(nums[:-2])      # [0, 1, 2, 3, 4, 5, 6, 7]（除了最後 2 個）

# 步進值 [start:end:step]
print(nums[::2])      # [0, 2, 4, 6, 8]（每隔一個）
print(nums[1::2])     # [1, 3, 5, 7, 9]（奇數索引）
print(nums[::-1])     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]（反轉）
print(nums[7:2:-1])   # [7, 6, 5, 4, 3]（倒著取）

# JavaScript 對照：
# nums.slice(2, 5)    → [2, 3, 4]
# nums.slice(-3)      → [7, 8, 9]
# nums.reverse()      → 會修改原陣列
# [...nums].reverse() → 不修改原陣列
```

### 修改列表

```python
fruits = ["apple", "banana", "cherry"]

# 修改單一元素
fruits[0] = "apricot"
print(fruits)         # ["apricot", "banana", "cherry"]

# 切片賦值（可改變長度）
fruits[1:2] = ["blueberry", "blackberry"]
print(fruits)         # ["apricot", "blueberry", "blackberry", "cherry"]

# 插入元素（不刪除）
nums = [1, 2, 5]
nums[2:2] = [3, 4]    # 在索引 2 處插入
print(nums)           # [1, 2, 3, 4, 5]

# 刪除元素（切片賦值空列表）
nums[1:3] = []
print(nums)           # [1, 4, 5]
```

### 列表方法對照表

| 操作           | JavaScript            | Python                       |
| -------------- | --------------------- | ---------------------------- |
| 新增到尾端     | `arr.push(x)`         | `arr.append(x)`              |
| 新增多個到尾端 | `arr.push(a, b, c)`   | `arr.extend([a, b, c])`      |
| 移除尾端       | `arr.pop()`           | `arr.pop()`                  |
| 新增到開頭     | `arr.unshift(x)`      | `arr.insert(0, x)`           |
| 移除開頭       | `arr.shift()`         | `arr.pop(0)`                 |
| 插入           | `arr.splice(i, 0, x)` | `arr.insert(i, x)`           |
| 移除指定索引   | `arr.splice(i, 1)`    | `arr.pop(i)` 或 `del arr[i]` |
| 移除指定值     | 需手動                | `arr.remove(x)`              |
| 尋找索引       | `arr.indexOf(x)`      | `arr.index(x)`               |
| 包含檢查       | `arr.includes(x)`     | `x in arr`                   |
| 長度           | `arr.length`          | `len(arr)`                   |
| 排序           | `arr.sort()`          | `arr.sort()`                 |
| 反轉           | `arr.reverse()`       | `arr.reverse()`              |
| 複製           | `[...arr]`            | `arr.copy()` 或 `arr[:]`     |
| 合併           | `[...a, ...b]`        | `a + b` 或 `[*a, *b]`        |
| 清空           | `arr.length = 0`      | `arr.clear()`                |

### 列表方法詳解

#### 新增元素

```python
fruits = ["apple", "banana"]

# append() - 新增單一元素到尾端
fruits.append("cherry")
print(fruits)         # ["apple", "banana", "cherry"]

# extend() - 新增多個元素（展開可迭代物件）
fruits.extend(["date", "elderberry"])
print(fruits)         # ["apple", "banana", "cherry", "date", "elderberry"]

# 注意 append vs extend 的差異
a = [1, 2]
a.append([3, 4])      # [1, 2, [3, 4]]（新增一個列表元素）

b = [1, 2]
b.extend([3, 4])      # [1, 2, 3, 4]（展開並新增）

# insert() - 在指定位置插入
fruits = ["apple", "cherry"]
fruits.insert(1, "banana")
print(fruits)         # ["apple", "banana", "cherry"]

# 使用 + 運算子合併（建立新列表）
combined = [1, 2] + [3, 4]  # [1, 2, 3, 4]

# 使用 += 就地合併（等同 extend）
nums = [1, 2]
nums += [3, 4]        # [1, 2, 3, 4]
```

#### 移除元素

```python
fruits = ["apple", "banana", "cherry", "banana", "date"]

# pop() - 移除並回傳指定索引的元素（預設最後一個）
last = fruits.pop()       # "date"
print(fruits)             # ["apple", "banana", "cherry", "banana"]

second = fruits.pop(1)    # "banana"
print(fruits)             # ["apple", "cherry", "banana"]

# remove() - 移除第一個符合的值
fruits.remove("banana")
print(fruits)             # ["apple", "cherry"]

# remove() 找不到會報錯
# fruits.remove("xyz")    # ValueError: list.remove(x): x not in list

# 安全移除
def safe_remove(lst, item):
    try:
        lst.remove(item)
    except ValueError:
        pass

# del 陳述式 - 刪除指定索引或切片
nums = [0, 1, 2, 3, 4, 5]
del nums[0]               # [1, 2, 3, 4, 5]
del nums[1:3]             # [1, 4, 5]
del nums[:]               # []（清空）

# clear() - 清空列表
fruits = ["apple", "banana"]
fruits.clear()
print(fruits)             # []
```

#### 搜尋與計數

```python
fruits = ["apple", "banana", "cherry", "banana", "date"]

# index() - 尋找第一個符合值的索引
print(fruits.index("banana"))       # 1
print(fruits.index("banana", 2))    # 3（從索引 2 開始找）

# index() 找不到會報錯
# print(fruits.index("xyz"))        # ValueError

# 安全尋找
def safe_index(lst, item, default=-1):
    try:
        return lst.index(item)
    except ValueError:
        return default

# 或使用 in 先檢查
if "banana" in fruits:
    idx = fruits.index("banana")

# count() - 計算出現次數
print(fruits.count("banana"))       # 2
print(fruits.count("xyz"))          # 0（不會報錯）

# in 運算子 - 檢查是否存在
print("apple" in fruits)            # True
print("xyz" in fruits)              # False
print("xyz" not in fruits)          # True
```

#### 排序

```python
# sort() - 就地排序（修改原列表）
nums = [3, 1, 4, 1, 5, 9, 2, 6]
nums.sort()
print(nums)               # [1, 1, 2, 3, 4, 5, 6, 9]

# 反向排序
nums.sort(reverse=True)
print(nums)               # [9, 6, 5, 4, 3, 2, 1, 1]

# sorted() - 回傳新列表（不修改原列表）
original = [3, 1, 4, 1, 5]
sorted_list = sorted(original)
print(original)           # [3, 1, 4, 1, 5]（未改變）
print(sorted_list)        # [1, 1, 3, 4, 5]

# 自訂排序鍵
words = ["banana", "Apple", "cherry", "Date"]

# 按長度排序
words.sort(key=len)
print(words)              # ["Date", "Apple", "banana", "cherry"]

# 不分大小寫排序
words.sort(key=str.lower)
print(words)              # ["Apple", "banana", "cherry", "Date"]

# 複雜排序（使用 lambda）
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78},
]
students.sort(key=lambda x: x["grade"], reverse=True)
# [{"name": "Bob", ...}, {"name": "Alice", ...}, {"name": "Charlie", ...}]

# JavaScript 對照：
# arr.sort((a, b) => a.grade - b.grade)
```

#### 其他方法

```python
# reverse() - 就地反轉
nums = [1, 2, 3, 4, 5]
nums.reverse()
print(nums)               # [5, 4, 3, 2, 1]

# reversed() - 回傳迭代器（不修改原列表）
nums = [1, 2, 3, 4, 5]
rev = list(reversed(nums))
print(nums)               # [1, 2, 3, 4, 5]（未改變）
print(rev)                # [5, 4, 3, 2, 1]

# copy() - 淺複製
original = [1, 2, [3, 4]]
copied = original.copy()

copied[0] = 99
print(original)           # [1, 2, [3, 4]]（未受影響）

copied[2][0] = 99
print(original)           # [1, 2, [99, 4]]（巢狀物件受影響！）

# 深複製需要 copy 模組
import copy
original = [1, 2, [3, 4]]
deep_copied = copy.deepcopy(original)

deep_copied[2][0] = 99
print(original)           # [1, 2, [3, 4]]（未受影響）
```

### 列表迭代

```python
fruits = ["apple", "banana", "cherry"]

# 基本迭代
for fruit in fruits:
    print(fruit)

# 帶索引迭代（使用 enumerate）
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry

# 指定起始索引
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}: {fruit}")
# 1: apple
# 2: banana
# 3: cherry

# 同時迭代多個列表（使用 zip）
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
for name, age in zip(names, ages):
    print(f"{name} is {age}")

# JavaScript 對照：
# fruits.forEach((fruit, i) => console.log(`${i}: ${fruit}`))
# for (const [i, fruit] of fruits.entries()) { ... }
```

---

## 3.2 Dictionary（字典）

Python 的 Dictionary 對應 JavaScript 的 Object，用於儲存鍵值對。

### 建立字典

```python
# 基本建立方式
empty = {}
person = {"name": "Alice", "age": 30, "city": "Taipei"}

# 使用 dict() 建構子
person = dict(name="Alice", age=30, city="Taipei")

# 從鍵值對列表建立
pairs = [("name", "Alice"), ("age", 30)]
person = dict(pairs)

# 從兩個列表建立（使用 zip）
keys = ["name", "age", "city"]
values = ["Alice", 30, "Taipei"]
person = dict(zip(keys, values))

# 字典推導式
squares = {x: x ** 2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# fromkeys() - 建立有相同預設值的字典
keys = ["a", "b", "c"]
default_dict = dict.fromkeys(keys, 0)
# {"a": 0, "b": 0, "c": 0}
```

### 字典鍵的限制

```python
# Python 字典的鍵必須是不可變（hashable）型別
valid_keys = {
    "string": "字串可以",
    42: "整數可以",
    3.14: "浮點數可以",
    (1, 2): "元組可以",
    True: "布林值可以",
}

# 列表不能作為鍵
# invalid = {[1, 2]: "錯誤"}  # TypeError: unhashable type: 'list'

# 字典不能作為鍵
# invalid = {{}: "錯誤"}      # TypeError: unhashable type: 'dict'

# JavaScript 對照：
# JS 的 Object 鍵會被轉為字串
# { [1, 2]: "value" } → { "1,2": "value" }
```

### 存取與修改

```python
person = {"name": "Alice", "age": 30}

# 存取值
print(person["name"])         # "Alice"
# print(person["email"])      # KeyError: 'email'

# get() - 安全存取（找不到回傳 None 或預設值）
print(person.get("name"))     # "Alice"
print(person.get("email"))    # None
print(person.get("email", "N/A"))  # "N/A"

# 修改值
person["age"] = 31
print(person)                 # {"name": "Alice", "age": 31}

# 新增鍵值對
person["email"] = "alice@example.com"

# setdefault() - 取得值，若不存在則設定預設值
person.setdefault("city", "Unknown")
print(person["city"])         # "Unknown"

person.setdefault("city", "Taipei")  # 已存在，不會改變
print(person["city"])         # "Unknown"
```

### 字典方法對照表

| 操作     | JavaScript                         | Python                     |
| -------- | ---------------------------------- | -------------------------- |
| 取值     | `obj.key` 或 `obj["key"]`          | `dict["key"]`              |
| 安全取值 | `obj?.key` 或 `obj.key ?? default` | `dict.get("key", default)` |
| 設值     | `obj.key = value`                  | `dict["key"] = value`      |
| 刪除     | `delete obj.key`                   | `del dict["key"]`          |
| 所有鍵   | `Object.keys(obj)`                 | `dict.keys()`              |
| 所有值   | `Object.values(obj)`               | `dict.values()`            |
| 鍵值對   | `Object.entries(obj)`              | `dict.items()`             |
| 包含鍵   | `"key" in obj`                     | `"key" in dict`            |
| 合併     | `{...a, ...b}`                     | `{**a, **b}` 或 `a \| b`   |
| 長度     | `Object.keys(obj).length`          | `len(dict)`                |

### 字典方法詳解

#### 檢視方法

```python
person = {"name": "Alice", "age": 30, "city": "Taipei"}

# keys() - 取得所有鍵
print(person.keys())        # dict_keys(['name', 'age', 'city'])
print(list(person.keys()))  # ['name', 'age', 'city']

# values() - 取得所有值
print(person.values())        # dict_values(['Alice', 30, 'Taipei'])
print(list(person.values()))  # ['Alice', 30, 'Taipei']

# items() - 取得所有鍵值對
print(person.items())
# dict_items([('name', 'Alice'), ('age', 30), ('city', 'Taipei')])

# 迭代鍵值對
for key, value in person.items():
    print(f"{key}: {value}")
```

#### 更新與合併

```python
person = {"name": "Alice", "age": 30}

# update() - 更新/合併字典
person.update({"age": 31, "city": "Taipei"})
print(person)  # {"name": "Alice", "age": 31, "city": "Taipei"}

# 也可以用關鍵字參數
person.update(email="alice@example.com")

# | 運算子合併（Python 3.9+）
a = {"x": 1, "y": 2}
b = {"y": 3, "z": 4}
merged = a | b
print(merged)  # {"x": 1, "y": 3, "z": 4}

# |= 就地合併（Python 3.9+）
a |= b
print(a)       # {"x": 1, "y": 3, "z": 4}

# 使用 ** 展開合併
merged = {**a, **b}

# JavaScript 對照：
# const merged = { ...a, ...b };
# Object.assign(a, b);
```

#### 刪除方法

```python
person = {"name": "Alice", "age": 30, "city": "Taipei"}

# pop() - 移除並回傳值
age = person.pop("age")
print(age)        # 30
print(person)     # {"name": "Alice", "city": "Taipei"}

# pop() 找不到鍵會報錯，但可設預設值
email = person.pop("email", "N/A")
print(email)      # "N/A"

# popitem() - 移除並回傳最後加入的鍵值對（Python 3.7+）
person = {"name": "Alice", "age": 30, "city": "Taipei"}
item = person.popitem()
print(item)       # ("city", "Taipei")

# del - 刪除指定鍵
del person["age"]

# clear() - 清空字典
person.clear()
print(person)     # {}
```

#### 複製

```python
original = {"name": "Alice", "info": {"age": 30}}

# copy() - 淺複製
copied = original.copy()
copied["name"] = "Bob"
print(original["name"])  # "Alice"（未受影響）

copied["info"]["age"] = 25
print(original["info"]["age"])  # 25（巢狀物件受影響！）

# 深複製
import copy
deep_copied = copy.deepcopy(original)
```

### 字典迭代

```python
person = {"name": "Alice", "age": 30, "city": "Taipei"}

# 迭代鍵（預設行為）
for key in person:
    print(key)

# 明確迭代鍵
for key in person.keys():
    print(key)

# 迭代值
for value in person.values():
    print(value)

# 迭代鍵值對
for key, value in person.items():
    print(f"{key}: {value}")

# 檢查鍵是否存在
if "name" in person:
    print("有 name 鍵")

if "email" not in person:
    print("沒有 email 鍵")
```

### 巢狀字典

```python
# 巢狀字典
users = {
    "user1": {
        "name": "Alice",
        "email": "alice@example.com",
        "orders": [101, 102, 103]
    },
    "user2": {
        "name": "Bob",
        "email": "bob@example.com",
        "orders": [201, 202]
    }
}

# 存取巢狀值
print(users["user1"]["name"])        # "Alice"
print(users["user1"]["orders"][0])   # 101

# 安全存取巢狀值
def safe_get(d, *keys, default=None):
    """安全地取得巢狀字典的值"""
    for key in keys:
        if isinstance(d, dict):
            d = d.get(key, default)
        else:
            return default
    return d

print(safe_get(users, "user1", "name"))           # "Alice"
print(safe_get(users, "user3", "name", default="Unknown"))  # "Unknown"
```

### defaultdict 與 Counter

```python
from collections import defaultdict, Counter

# defaultdict - 自動建立預設值
word_counts = defaultdict(int)
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]

for word in words:
    word_counts[word] += 1
    # 一般 dict 需要先檢查 key 是否存在

print(dict(word_counts))  # {"apple": 3, "banana": 2, "cherry": 1}

# defaultdict 搭配 list
grouped = defaultdict(list)
data = [("a", 1), ("b", 2), ("a", 3), ("b", 4)]

for key, value in data:
    grouped[key].append(value)

print(dict(grouped))  # {"a": [1, 3], "b": [2, 4]}

# Counter - 計數器
word_counts = Counter(words)
print(word_counts)              # Counter({"apple": 3, "banana": 2, "cherry": 1})
print(word_counts.most_common(2))  # [("apple", 3), ("banana", 2)]
```

---

## 3.3 Tuple（元組）

Tuple 是不可變的序列，JavaScript 沒有直接對應的結構（類似 `Object.freeze([])` 但更徹底）。

### 建立元組

```python
# 基本建立方式
empty = ()
single = (42,)              # 單一元素需要逗號
point = (10, 20)
rgb = (255, 128, 0)

# 不需要括號也能建立
coordinates = 10, 20, 30    # 等同於 (10, 20, 30)

# 使用 tuple() 建構子
from_list = tuple([1, 2, 3])
from_string = tuple("abc")  # ('a', 'b', 'c')
from_range = tuple(range(5))  # (0, 1, 2, 3, 4)
```

### 存取元素

```python
point = (10, 20, 30)

# 索引存取
print(point[0])     # 10
print(point[-1])    # 30

# 切片
print(point[1:])    # (20, 30)
print(point[:2])    # (10, 20)

# 不能修改！
# point[0] = 99     # TypeError: 'tuple' object does not support item assignment
```

### 解構賦值

```python
# 基本解構
point = (10, 20, 30)
x, y, z = point
print(x, y, z)      # 10 20 30

# 交換變數
a, b = 1, 2
a, b = b, a
print(a, b)         # 2 1

# 使用 * 收集剩餘元素
first, *rest = (1, 2, 3, 4, 5)
print(first)        # 1
print(rest)         # [2, 3, 4, 5]（注意：是 list）

first, *middle, last = (1, 2, 3, 4, 5)
print(first)        # 1
print(middle)       # [2, 3, 4]
print(last)         # 5

# 忽略不需要的值
x, _, z = (1, 2, 3)
print(x, z)         # 1 3

# JavaScript 對照：
# const [x, y, z] = [1, 2, 3];
# const [first, ...rest] = [1, 2, 3, 4, 5];
```

### Tuple vs List

| 特性           | Tuple            | List             |
| -------------- | ---------------- | ---------------- |
| 可變性         | 不可變           | 可變             |
| 語法           | `()`             | `[]`             |
| 效能           | 較快、較省記憶體 | 較慢、較佔記憶體 |
| 可作為 dict 鍵 | 可以             | 不可以           |
| 使用場景       | 固定結構資料     | 動態集合資料     |

```python
# Tuple 可作為字典鍵
locations = {
    (25.0, 121.5): "Taipei",
    (35.7, 139.7): "Tokyo",
}

# List 不行
# locations = {[25.0, 121.5]: "Taipei"}  # TypeError

# Tuple 較省記憶體
import sys
print(sys.getsizeof((1, 2, 3)))  # 64 bytes（大約）
print(sys.getsizeof([1, 2, 3]))  # 88 bytes（大約）
```

### 具名元組（Named Tuple）

```python
from collections import namedtuple

# 定義具名元組
Point = namedtuple("Point", ["x", "y"])
Person = namedtuple("Person", "name age city")

# 建立實例
p = Point(10, 20)
alice = Person("Alice", 30, "Taipei")

# 存取（可用名稱或索引）
print(p.x, p.y)              # 10 20
print(p[0], p[1])            # 10 20
print(alice.name)            # "Alice"

# 解構
x, y = p
print(x, y)                  # 10 20

# 轉換為字典
print(alice._asdict())       # {"name": "Alice", "age": 30, "city": "Taipei"}

# 建立修改後的副本（因為是不可變的）
bob = alice._replace(name="Bob", age=25)
print(bob)                   # Person(name='Bob', age=25, city='Taipei')
```

### typing.NamedTuple（推薦）

```python
from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float

class Person(NamedTuple):
    name: str
    age: int
    city: str = "Unknown"  # 可設預設值

p = Point(10.5, 20.3)
alice = Person("Alice", 30)

print(p.x, p.y)            # 10.5 20.3
print(alice)               # Person(name='Alice', age=30, city='Unknown')
```

---

## 3.4 Set（集合）

Set 是無序且不重複的元素集合，JavaScript 的 `Set` 與之類似。

### 建立集合

```python
# 基本建立方式
empty = set()               # 注意：{} 是空字典，不是空集合
numbers = {1, 2, 3, 4, 5}
mixed = {1, "hello", 3.14}

# 自動去除重複
nums = {1, 2, 2, 3, 3, 3}
print(nums)                 # {1, 2, 3}

# 使用 set() 建構子
from_list = set([1, 2, 2, 3])   # {1, 2, 3}
from_string = set("hello")       # {'h', 'e', 'l', 'o'}

# 集合推導式
squares = {x ** 2 for x in range(5)}  # {0, 1, 4, 9, 16}
```

### 集合操作

```python
s = {1, 2, 3}

# 新增元素
s.add(4)
print(s)          # {1, 2, 3, 4}

# 新增多個元素
s.update([5, 6, 7])
print(s)          # {1, 2, 3, 4, 5, 6, 7}

# 移除元素
s.remove(7)       # 不存在會報錯 KeyError
s.discard(99)     # 不存在不會報錯
popped = s.pop()  # 移除並回傳任意元素

# 清空
s.clear()

# 長度
print(len({1, 2, 3}))  # 3

# 成員檢查（非常快，O(1)）
print(2 in {1, 2, 3})  # True
```

### 集合運算

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

# 聯集（Union）
print(a | b)              # {1, 2, 3, 4, 5, 6, 7, 8}
print(a.union(b))         # 同上

# 交集（Intersection）
print(a & b)              # {4, 5}
print(a.intersection(b))  # 同上

# 差集（Difference）
print(a - b)              # {1, 2, 3}
print(a.difference(b))    # 同上

# 對稱差集（Symmetric Difference）
print(a ^ b)                      # {1, 2, 3, 6, 7, 8}
print(a.symmetric_difference(b))  # 同上

# 子集與超集
small = {1, 2}
large = {1, 2, 3, 4, 5}

print(small <= large)     # True（small 是 large 的子集）
print(small.issubset(large))

print(large >= small)     # True（large 是 small 的超集）
print(large.issuperset(small))

# 嚴格子集（不相等）
print(small < large)      # True
print(large < large)      # False

# 不相交
x = {1, 2}
y = {3, 4}
print(x.isdisjoint(y))    # True（沒有共同元素）
```

### 集合運算對照表

| 運算     | 運算子   | 方法                        | 說明              |
| -------- | -------- | --------------------------- | ----------------- |
| 聯集     | `a \| b` | `a.union(b)`                | 所有元素          |
| 交集     | `a & b`  | `a.intersection(b)`         | 共同元素          |
| 差集     | `a - b`  | `a.difference(b)`           | 在 a 但不在 b     |
| 對稱差集 | `a ^ b`  | `a.symmetric_difference(b)` | 只在其中一個      |
| 子集     | `a <= b` | `a.issubset(b)`             | a 的元素都在 b 中 |
| 超集     | `a >= b` | `a.issuperset(b)`           | b 的元素都在 a 中 |
| 不相交   | -        | `a.isdisjoint(b)`           | 沒有共同元素      |

### frozenset（不可變集合）

```python
# frozenset 是不可變的集合
fs = frozenset([1, 2, 3])

# 不能修改
# fs.add(4)  # AttributeError

# 可以作為字典的鍵或集合的元素
d = {fs: "value"}
s = {frozenset([1, 2]), frozenset([3, 4])}

# 支援集合運算
a = frozenset([1, 2, 3])
b = frozenset([2, 3, 4])
print(a | b)  # frozenset({1, 2, 3, 4})
```

### 集合的實際應用

```python
# 去除重複
names = ["Alice", "Bob", "Alice", "Charlie", "Bob"]
unique_names = list(set(names))

# 保持順序的去重（Python 3.7+）
unique_ordered = list(dict.fromkeys(names))

# 找出共同元素
user_a_skills = {"Python", "JavaScript", "SQL"}
user_b_skills = {"JavaScript", "Java", "SQL"}
common_skills = user_a_skills & user_b_skills
print(common_skills)  # {"JavaScript", "SQL"}

# 找出差異
only_a = user_a_skills - user_b_skills  # {"Python"}
only_b = user_b_skills - user_a_skills  # {"Java"}

# 快速成員檢查
valid_statuses = {"pending", "approved", "rejected"}
if status in valid_statuses:
    process(status)

# 移除禁用詞
text = "the quick brown fox jumps over the lazy dog"
stop_words = {"the", "a", "an", "over"}
words = text.split()
filtered = [w for w in words if w not in stop_words]
```

---

## 練習題

### 練習 1：列表操作

將以下 JavaScript 程式碼轉換為 Python：

```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.push(6);
numbers.unshift(0);
const last = numbers.pop();
const first = numbers.shift();
const sliced = numbers.slice(1, 4);
const reversed = [...numbers].reverse();
```

### 練習 2：字典操作

寫一個函式統計字串中每個字元出現的次數：

```python
def count_chars(s):
    # 你的程式碼
    pass

# 預期結果
print(count_chars("hello"))
# {"h": 1, "e": 1, "l": 2, "o": 1}
```

### 練習 3：集合運算

給定兩個列表，找出：
1. 兩個列表的共同元素
2. 只在第一個列表的元素
3. 只在第二個列表的元素
4. 合併後的不重複元素

```python
list1 = [1, 2, 3, 4, 5, 5]
list2 = [4, 5, 6, 7, 8, 8]

# 你的程式碼
```

---

## 小結

### 資料結構選擇指南

| 需求                                | 推薦結構                  |
| ----------------------------------- | ------------------------- |
| 有序、可變、允許重複                | `list`                    |
| 有序、不可變、允許重複              | `tuple`                   |
| 無序、可變、不允許重複              | `set`                     |
| 無序、不可變、不允許重複            | `frozenset`               |
| 鍵值對映射                          | `dict`                    |
| 計數                                | `collections.Counter`     |
| 預設值字典                          | `collections.defaultdict` |
| 有序字典（Python 3.7+ dict 已有序） | `dict`                    |

### JavaScript vs Python 對照

| JavaScript          | Python      | 說明               |
| ------------------- | ----------- | ------------------ |
| `Array`             | `list`      | 有序可變序列       |
| `Object`            | `dict`      | 鍵值對映射         |
| `Set`               | `set`       | 不重複集合         |
| `Object.freeze([])` | `tuple`     | 不可變序列         |
| `Map`               | `dict`      | Python dict 更常用 |
| -                   | `frozenset` | 不可變集合         |

### 效能考量

| 操作     | list  | dict | set  |
| -------- | ----- | ---- | ---- |
| 索引存取 | O(1)  | -    | -    |
| 鍵存取   | -     | O(1) | -    |
| 搜尋     | O(n)  | O(1) | O(1) |
| 新增     | O(1)* | O(1) | O(1) |
| 刪除     | O(n)  | O(1) | O(1) |
| 成員檢查 | O(n)  | O(1) | O(1) |

*list.append() 是 O(1)，但 insert(0, x) 是 O(n)
