# 第 5 章：流程控制

本章節詳細說明 Python 的流程控制結構，包含條件判斷、迴圈，以及各種控制流程的技巧。

---

## 5.1 條件判斷

### 基本 if 語句

#### JavaScript 的寫法

```javascript
if (condition) {
    // 程式碼
}

if (score >= 60) {
    console.log("Pass");
}
```

#### Python 的寫法

```python
if condition:
    # 程式碼（縮排 4 空格）

if score >= 60:
    print("Pass")
```

### 主要語法差異

| 特性 | JavaScript | Python |
|------|------------|--------|
| 條件括號 | 必須 `(condition)` | 不需要 |
| 程式碼區塊 | 大括號 `{}` | 冒號 `:` + 縮排 |
| 關鍵字 | `else if` | `elif` |

### if-elif-else 完整結構

```python
score = 85

if score >= 90:
    grade = "A"
    print("優秀！")
elif score >= 80:
    grade = "B"
    print("良好")
elif score >= 70:
    grade = "C"
    print("及格")
elif score >= 60:
    grade = "D"
    print("勉強及格")
else:
    grade = "F"
    print("不及格")

print(f"成績等級：{grade}")
```

### 單行條件（條件表達式）

```python
# 條件表達式（三元運算子）
status = "Pass" if score >= 60 else "Fail"

# 對應 JavaScript
# const status = score >= 60 ? "Pass" : "Fail";

# 巢狀條件表達式
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"

# 為了可讀性，建議換行
grade = (
    "A" if score >= 90 else
    "B" if score >= 80 else
    "C" if score >= 70 else
    "F"
)
```

### 條件判斷的 Truthy / Falsy

```python
# Python 的 Falsy 值
# False, None, 0, 0.0, "", [], {}, set(), ()

# 檢查變數是否有值
name = ""
if name:
    print(f"Hello, {name}")
else:
    print("Name is empty")

# 檢查列表是否為空
items = []
if items:
    print(f"有 {len(items)} 個項目")
else:
    print("列表為空")

# 檢查字典
config = {}
if config:
    process(config)
else:
    use_default()

# 注意：與 JavaScript 的差異
# JavaScript: [] 和 {} 是 truthy
# Python: [] 和 {} 是 falsy
```

### 多條件判斷

```python
age = 25
has_license = True
is_sober = True

# 使用 and
if age >= 18 and has_license and is_sober:
    print("可以開車")

# 使用 or
day = "Saturday"
if day == "Saturday" or day == "Sunday":
    print("週末")

# 使用 in 簡化多個 or
if day in ("Saturday", "Sunday"):
    print("週末")

# 使用 not
if not items:
    print("列表為空")

# 複合條件
if (age >= 18 and has_license) or is_emergency:
    allow_driving()
```

### 成員檢查

```python
# 使用 in 運算子
fruits = ["apple", "banana", "cherry"]

if "banana" in fruits:
    print("有香蕉")

if "grape" not in fruits:
    print("沒有葡萄")

# 字典檢查（檢查鍵）
person = {"name": "Alice", "age": 30}

if "name" in person:
    print(person["name"])

if "email" not in person:
    print("沒有 email")

# 字串檢查
text = "Hello, World!"

if "World" in text:
    print("找到 World")
```

### None 檢查

```python
result = some_function()

# 推薦：使用 is
if result is None:
    print("沒有結果")

if result is not None:
    print(f"結果：{result}")

# 不推薦：使用 ==
if result == None:  # 可行但不推薦
    pass

# 簡化的 None 檢查（利用 Falsy）
# 注意：這也會在 result 為 0, "", [] 時觸發
if not result:
    print("沒有結果或結果為空")

# 如果需要區分 None 和其他 Falsy 值
if result is None:
    print("結果是 None")
elif not result:
    print("結果為空（但不是 None）")
else:
    print(f"結果：{result}")
```

### 型別檢查

```python
value = get_value()

# 使用 isinstance（推薦）
if isinstance(value, str):
    print(f"字串：{value}")
elif isinstance(value, (int, float)):
    print(f"數字：{value}")
elif isinstance(value, list):
    print(f"列表，長度：{len(value)}")

# 使用 type（較少用）
if type(value) == str:
    print("確切是 str 型別")

# 檢查多種型別
if isinstance(value, (list, tuple)):
    print("是序列型別")
```

### match-case（Python 3.10+）

Python 3.10 引入的結構化模式匹配，類似其他語言的 switch-case：

```python
def http_status(status):
    match status:
        case 200:
            return "OK"
        case 201:
            return "Created"
        case 400:
            return "Bad Request"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:  # 預設情況（類似 default）
            return "Unknown"

print(http_status(200))  # "OK"
print(http_status(999))  # "Unknown"
```

#### match-case 進階用法

```python
# 匹配多個值
def day_type(day):
    match day:
        case "Saturday" | "Sunday":
            return "週末"
        case "Monday" | "Tuesday" | "Wednesday" | "Thursday" | "Friday":
            return "工作日"
        case _:
            return "無效"

# 解構匹配
def process_point(point):
    match point:
        case (0, 0):
            return "原點"
        case (0, y):
            return f"在 Y 軸上，y = {y}"
        case (x, 0):
            return f"在 X 軸上，x = {x}"
        case (x, y):
            return f"點 ({x}, {y})"
        case _:
            return "不是有效的點"

print(process_point((0, 0)))   # "原點"
print(process_point((0, 5)))   # "在 Y 軸上，y = 5"
print(process_point((3, 4)))   # "點 (3, 4)"

# 字典匹配
def process_command(command):
    match command:
        case {"action": "create", "name": name}:
            return f"建立 {name}"
        case {"action": "delete", "id": id}:
            return f"刪除 ID: {id}"
        case {"action": action}:
            return f"未知動作：{action}"
        case _:
            return "無效命令"

# 帶條件的匹配（guard）
def categorize_number(n):
    match n:
        case x if x < 0:
            return "負數"
        case 0:
            return "零"
        case x if x % 2 == 0:
            return "正偶數"
        case _:
            return "正奇數"
```

### 傳統的 switch-case 替代方案

在 Python 3.10 之前，常用字典來模擬 switch-case：

```python
def switch_example(value):
    return {
        "a": "選擇了 A",
        "b": "選擇了 B",
        "c": "選擇了 C",
    }.get(value, "預設值")

# 使用函式作為值
def handle_start():
    return "開始處理"

def handle_stop():
    return "停止處理"

def handle_default():
    return "未知命令"

def switch_with_functions(command):
    actions = {
        "start": handle_start,
        "stop": handle_stop,
    }
    handler = actions.get(command, handle_default)
    return handler()  # 呼叫函式

print(switch_with_functions("start"))  # "開始處理"
print(switch_with_functions("unknown"))  # "未知命令"
```

---

## 5.2 迴圈

### for 迴圈

#### JavaScript 的 for 迴圈

```javascript
// 傳統 for
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// for...of（遍歷值）
for (const item of items) {
    console.log(item);
}

// for...in（遍歷鍵）
for (const key in obj) {
    console.log(key);
}

// forEach
items.forEach((item, index) => {
    console.log(index, item);
});
```

#### Python 的 for 迴圈

```python
# Python 的 for 類似 JavaScript 的 for...of
# 直接遍歷可迭代物件

# 遍歷列表
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# 遍歷字串
for char in "Hello":
    print(char)

# 遍歷字典（預設遍歷鍵）
person = {"name": "Alice", "age": 30}
for key in person:
    print(key)

# 遍歷字典的值
for value in person.values():
    print(value)

# 遍歷字典的鍵值對
for key, value in person.items():
    print(f"{key}: {value}")
```

### range() 函式

用於生成數字序列，類似傳統的計數迴圈：

```python
# range(stop) - 從 0 到 stop-1
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# range(start, stop) - 從 start 到 stop-1
for i in range(1, 6):
    print(i)  # 1, 2, 3, 4, 5

# range(start, stop, step) - 指定步進值
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8

# 倒數
for i in range(5, 0, -1):
    print(i)  # 5, 4, 3, 2, 1

# 對應 JavaScript
# for (let i = 0; i < 5; i++) { ... }
# for (let i = 1; i < 6; i++) { ... }
# for (let i = 0; i < 10; i += 2) { ... }
# for (let i = 5; i > 0; i--) { ... }
```

### enumerate() 函式

取得索引和值，類似 JavaScript 的 `forEach` 或 `entries()`：

```python
fruits = ["apple", "banana", "cherry"]

# enumerate 回傳 (索引, 值) 的元組
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry

# 指定起始索引
for index, fruit in enumerate(fruits, start=1):
    print(f"{index}: {fruit}")
# 1: apple
# 2: banana
# 3: cherry

# 對應 JavaScript
# fruits.forEach((fruit, index) => { ... })
# for (const [index, fruit] of fruits.entries()) { ... }
```

### zip() 函式

同時遍歷多個序列：

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["Taipei", "Tokyo", "Seoul"]

# 同時遍歷兩個列表
for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# 同時遍歷三個列表
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, from {city}")

# zip 在最短序列結束時停止
short = [1, 2]
long = [1, 2, 3, 4, 5]
for a, b in zip(short, long):
    print(a, b)  # 只印出 (1,1) 和 (2,2)

# 使用 zip_longest 處理不等長序列
from itertools import zip_longest

for a, b in zip_longest(short, long, fillvalue=0):
    print(a, b)  # (1,1), (2,2), (0,3), (0,4), (0,5)

# 結合 enumerate 和 zip
for i, (name, age) in enumerate(zip(names, ages)):
    print(f"{i}: {name} - {age}")
```

### while 迴圈

```python
# 基本 while 迴圈
count = 0
while count < 5:
    print(count)
    count += 1

# 無限迴圈（需要 break 跳出）
while True:
    user_input = input("輸入 'quit' 離開: ")
    if user_input == "quit":
        break
    print(f"你輸入了: {user_input}")

# 條件在迴圈內改變
items = [1, 2, 3, 4, 5]
while items:
    item = items.pop()
    print(f"處理: {item}")
# 當 items 為空（Falsy）時停止

# 使用海象運算子（Python 3.8+）
while (line := input("輸入: ")) != "quit":
    print(f"你輸入了: {line}")
```

### 迭代技巧

#### 反向迭代

```python
fruits = ["apple", "banana", "cherry"]

# 方法 1：reversed()
for fruit in reversed(fruits):
    print(fruit)

# 方法 2：切片
for fruit in fruits[::-1]:
    print(fruit)

# 方法 3：range 倒數
for i in range(len(fruits) - 1, -1, -1):
    print(fruits[i])
```

#### 排序迭代

```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# 排序後迭代（不修改原列表）
for num in sorted(numbers):
    print(num)  # 1, 1, 2, 3, 4, 5, 6, 9

# 反向排序
for num in sorted(numbers, reverse=True):
    print(num)  # 9, 6, 5, 4, 3, 2, 1, 1

# 自訂排序
words = ["banana", "Apple", "cherry"]
for word in sorted(words, key=str.lower):
    print(word)  # Apple, banana, cherry
```

#### 字典排序迭代

```python
scores = {"Alice": 85, "Bob": 92, "Charlie": 78}

# 按鍵排序
for name in sorted(scores):
    print(f"{name}: {scores[name]}")

# 按值排序
for name in sorted(scores, key=lambda x: scores[x], reverse=True):
    print(f"{name}: {scores[name]}")
# Bob: 92
# Alice: 85
# Charlie: 78

# 或使用 items()
for name, score in sorted(scores.items(), key=lambda x: x[1], reverse=True):
    print(f"{name}: {score}")
```

#### 同時取得多個迭代器的元素

```python
# 建立字典
keys = ["name", "age", "city"]
values = ["Alice", 30, "Taipei"]
person = dict(zip(keys, values))

# 矩陣轉置
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
transposed = list(zip(*matrix))
# [(1, 4, 7), (2, 5, 8), (3, 6, 9)]
```

---

## 5.3 迴圈控制

### break - 跳出迴圈

```python
# 找到目標後停止
numbers = [1, 3, 5, 7, 9, 2, 4, 6, 8]

for num in numbers:
    if num % 2 == 0:  # 找到第一個偶數
        print(f"找到偶數: {num}")
        break
else:
    print("沒有找到偶數")

# 巢狀迴圈中的 break（只跳出內層）
for i in range(3):
    for j in range(3):
        if j == 1:
            break  # 只跳出內層迴圈
        print(f"({i}, {j})")
# (0, 0)
# (1, 0)
# (2, 0)
```

### continue - 跳過本次迭代

```python
# 跳過特定條件
for i in range(10):
    if i % 2 == 0:  # 跳過偶數
        continue
    print(i)  # 只印出奇數：1, 3, 5, 7, 9

# 實際應用：處理有效資料
data = [1, None, 2, "", 3, 0, 4]

for item in data:
    if item is None or item == "":
        continue
    print(f"處理: {item}")
# 處理: 1
# 處理: 2
# 處理: 3
# 處理: 0
# 處理: 4
```

### else 子句（Python 特有）

Python 的迴圈可以有 `else` 子句，在迴圈**正常結束**（沒有被 `break`）時執行：

```python
# for-else
for i in range(5):
    if i == 10:  # 永遠不會成立
        break
else:
    print("迴圈正常結束")  # 會執行

# 實際應用：搜尋
def find_item(items, target):
    for item in items:
        if item == target:
            print(f"找到: {target}")
            break
    else:
        print(f"沒有找到: {target}")

find_item([1, 2, 3, 4, 5], 3)  # 找到: 3
find_item([1, 2, 3, 4, 5], 9)  # 沒有找到: 9

# 質數檢查
def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False  # 找到因數，不是質數
    else:
        return True  # 沒有找到因數，是質數

# while-else
count = 0
while count < 3:
    print(count)
    count += 1
else:
    print("while 迴圈正常結束")
```

### 跳出多層迴圈

Python 沒有 `goto` 或標籤跳轉，跳出多層迴圈的方法：

```python
# 方法 1：使用函式和 return
def find_in_matrix(matrix, target):
    for i, row in enumerate(matrix):
        for j, value in enumerate(row):
            if value == target:
                return (i, j)
    return None

matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
result = find_in_matrix(matrix, 5)
print(result)  # (1, 1)

# 方法 2：使用旗標變數
found = False
for i in range(3):
    for j in range(3):
        if matrix[i][j] == 5:
            found = True
            break
    if found:
        break

# 方法 3：使用例外（不推薦用於正常流程控制）
class BreakLoop(Exception):
    pass

try:
    for i in range(3):
        for j in range(3):
            if matrix[i][j] == 5:
                raise BreakLoop()
except BreakLoop:
    print(f"找到於 ({i}, {j})")

# 方法 4：使用 itertools.product
from itertools import product

for i, j in product(range(3), range(3)):
    if matrix[i][j] == 5:
        print(f"找到於 ({i}, {j})")
        break
```

### pass 語句

`pass` 是空操作，用於語法上需要語句但不想執行任何動作的地方：

```python
# 空的程式碼區塊
if condition:
    pass  # 之後再實作

# 空的函式
def not_implemented_yet():
    pass

# 空的類別
class PlaceholderClass:
    pass

# 在迴圈中跳過（但 continue 更明確）
for i in range(10):
    if i % 2 == 0:
        pass  # 不做任何事
    else:
        print(i)

# 例外處理中忽略例外
try:
    risky_operation()
except SomeError:
    pass  # 忽略這個錯誤
```

### 迴圈效能優化

```python
# 避免在迴圈中重複計算
# 不好
for i in range(len(items)):
    if items[i] == expensive_function():
        pass

# 好
target = expensive_function()
for item in items:
    if item == target:
        pass

# 使用列表推導式代替迴圈（通常更快）
# 一般迴圈
squares = []
for x in range(1000):
    squares.append(x ** 2)

# 列表推導式
squares = [x ** 2 for x in range(1000)]

# 使用生成器處理大量資料（節省記憶體）
# 列表（一次載入所有資料）
data = [x ** 2 for x in range(1000000)]

# 生成器（惰性求值）
data = (x ** 2 for x in range(1000000))
```

---

## 5.4 迭代器與可迭代物件

### 可迭代物件（Iterable）

```python
# 這些都是可迭代物件
for item in [1, 2, 3]:          # 列表
    pass

for char in "hello":            # 字串
    pass

for key in {"a": 1, "b": 2}:    # 字典
    pass

for num in range(5):            # range
    pass

for item in (1, 2, 3):          # 元組
    pass

for item in {1, 2, 3}:          # 集合
    pass

# 檢查是否可迭代
from collections.abc import Iterable

print(isinstance([1, 2, 3], Iterable))  # True
print(isinstance(42, Iterable))          # False
```

### 迭代器（Iterator）

```python
# iter() 和 next()
fruits = ["apple", "banana", "cherry"]

# 取得迭代器
iterator = iter(fruits)

# 手動迭代
print(next(iterator))  # "apple"
print(next(iterator))  # "banana"
print(next(iterator))  # "cherry"
# print(next(iterator))  # StopIteration 例外

# 使用預設值避免 StopIteration
iterator = iter(fruits)
print(next(iterator, "沒有了"))  # "apple"
print(next(iterator, "沒有了"))  # "banana"
print(next(iterator, "沒有了"))  # "cherry"
print(next(iterator, "沒有了"))  # "沒有了"

# for 迴圈自動處理迭代器
for fruit in iter(fruits):
    print(fruit)
```

### 自訂迭代器

```python
class Countdown:
    """倒數計時迭代器"""

    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1

# 使用
for num in Countdown(5):
    print(num)  # 5, 4, 3, 2, 1
```

---

## 練習題

### 練習 1：FizzBuzz

寫一個程式，印出 1 到 100 的數字，但是：
- 3 的倍數印出 "Fizz"
- 5 的倍數印出 "Buzz"
- 同時是 3 和 5 的倍數印出 "FizzBuzz"

```python
# 你的程式碼
```

### 練習 2：質數列表

寫一個函式，回傳小於 n 的所有質數：

```python
def get_primes(n):
    # 你的程式碼
    pass

print(get_primes(20))  # [2, 3, 5, 7, 11, 13, 17, 19]
```

### 練習 3：巢狀迴圈

印出 9x9 乘法表：

```python
# 你的程式碼

# 預期輸出格式：
# 1x1=1  1x2=2  1x3=3 ...
# 2x1=2  2x2=4  2x3=6 ...
# ...
```

### 練習 4：使用 for-else

寫一個函式，檢查列表中是否有負數：

```python
def has_negative(numbers):
    # 使用 for-else 結構
    pass

print(has_negative([1, 2, 3, 4, 5]))   # False
print(has_negative([1, -2, 3, 4, 5]))  # True
```

---

## 小結

### 條件判斷對照表

| 特性 | JavaScript | Python |
|------|------------|--------|
| 條件語法 | `if (cond) {}` | `if cond:` |
| 否則如果 | `else if` | `elif` |
| 三元運算 | `a ? b : c` | `b if a else c` |
| switch-case | `switch...case` | `match...case`（3.10+） |

### 迴圈對照表

| 特性 | JavaScript | Python |
|------|------------|--------|
| for-of | `for (x of arr)` | `for x in arr:` |
| for-in | `for (k in obj)` | `for k in dict:` |
| 傳統 for | `for (i=0; i<n; i++)` | `for i in range(n):` |
| forEach | `arr.forEach((x, i) => {})` | `for i, x in enumerate(arr):` |
| while | `while (cond) {}` | `while cond:` |

### 迴圈控制對照表

| 功能 | JavaScript | Python |
|------|------------|--------|
| 跳出迴圈 | `break` | `break` |
| 跳過迭代 | `continue` | `continue` |
| 迴圈正常結束 | 無 | `else:` |
| 空操作 | `{}` 或 `;` | `pass` |

### Python 特有功能

1. **for-else / while-else**：迴圈正常結束時執行
2. **match-case**（3.10+）：結構化模式匹配
3. **鏈式比較**：`1 < x < 10`
4. **enumerate()**：帶索引迭代
5. **zip()**：同時迭代多個序列
6. **海象運算子**：`while (x := func()):`
