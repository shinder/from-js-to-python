# 第 4 章：運算子差異

本章節詳細說明 JavaScript 與 Python 在運算子上的差異，包含比較運算子、邏輯運算子、算術運算子，以及各種特殊運算子。

---

## 4.1 比較運算子

### 相等比較

#### JavaScript 的相等比較

JavaScript 有兩種相等比較：寬鬆相等（`==`）和嚴格相等（`===`）：

```javascript
// 寬鬆相等（會進行型別轉換）
console.log(1 == "1");        // true
console.log(0 == false);      // true
console.log(null == undefined); // true
console.log("" == 0);         // true

// 嚴格相等（不進行型別轉換）
console.log(1 === "1");       // false
console.log(0 === false);     // false
console.log(null === undefined); // false
```

#### Python 的相等比較

Python 只有一種相等比較 `==`，且**不會進行隱式型別轉換**：

```python
# Python 的 == 類似 JavaScript 的 ===
print(1 == "1")          # False（不同型別）
print(0 == False)        # True（bool 是 int 的子類別，False == 0）
print("" == 0)           # False
print([] == False)       # False

# None 的比較
print(None == None)      # True
print(None == 0)         # False
print(None == "")        # False
print(None == False)     # False
```

### 比較運算子對照表

| 運算 | JavaScript | Python | 說明 |
|------|------------|--------|------|
| 相等 | `===` | `==` | Python 的 `==` 不做型別轉換 |
| 不相等 | `!==` | `!=` | |
| 大於 | `>` | `>` | 相同 |
| 小於 | `<` | `<` | 相同 |
| 大於等於 | `>=` | `>=` | 相同 |
| 小於等於 | `<=` | `<=` | 相同 |

### 鏈式比較（Python 特有）

Python 支援鏈式比較，這是 JavaScript 沒有的特性：

```python
# Python 鏈式比較
x = 5
print(1 < x < 10)         # True（x 大於 1 且小於 10）
print(1 < x < 3)          # False
print(1 <= x <= 5)        # True

# 更複雜的鏈式比較
a, b, c = 1, 2, 3
print(a < b < c)          # True
print(a < b > c)          # False（b > c 是 False）

# 等價於
print(1 < x and x < 10)   # True

# JavaScript 沒有鏈式比較
# 1 < x < 10 在 JS 中會被解析為 (1 < x) < 10
# 即 true < 10 或 false < 10，結果是 true（因為 true 轉為 1）
```

### 身份比較（is / is not）

Python 有身份比較運算子，用於檢查兩個變數是否指向**同一物件**：

```python
# is 檢查是否為同一物件（比較記憶體位址）
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)       # True（值相等）
print(a is b)       # False（不是同一物件）
print(a is c)       # True（是同一物件）

# 檢查 None 應該用 is
x = None
print(x is None)        # True（推薦）
print(x == None)        # True（不推薦）
print(x is not None)    # False

# 為什麼用 is 比較 None？
class Sneaky:
    def __eq__(self, other):
        return True  # 總是回傳 True

obj = Sneaky()
print(obj == None)      # True（被騙了！）
print(obj is None)      # False（正確結果）
```

### 小整數快取

Python 對小整數有快取機制，這可能導致 `is` 的行為出乎意料：

```python
# 小整數（-5 到 256）會被快取
a = 256
b = 256
print(a is b)       # True（指向同一快取物件）

a = 257
b = 257
print(a is b)       # False（或 True，取決於執行環境）

# 字串也有類似的 interning 機制
s1 = "hello"
s2 = "hello"
print(s1 is s2)     # True（通常）

s1 = "hello world"
s2 = "hello world"
print(s1 is s2)     # 可能 True 或 False

# 重點：比較值用 ==，比較身份用 is
# 只有在比較 None、True、False 或確定要比較身份時才用 is
```

---

## 4.2 邏輯運算子

### 運算子對照表

| 運算 | JavaScript | Python | 說明 |
|------|------------|--------|------|
| 且 | `&&` | `and` | |
| 或 | `\|\|` | `or` | |
| 非 | `!` | `not` | |

### 基本用法

```python
a = True
b = False

# and - 且
print(a and b)      # False
print(a and a)      # True

# or - 或
print(a or b)       # True
print(b or b)       # False

# not - 非
print(not a)        # False
print(not b)        # True

# 組合使用
print(a and not b)  # True
print(not a or b)   # False
```

### 短路求值（Short-circuit Evaluation）

Python 和 JavaScript 都支援短路求值：

```python
# and 短路：第一個為 False 就不會評估第二個
def side_effect():
    print("函式被呼叫了")
    return True

result = False and side_effect()  # 不會印出任何東西
print(result)  # False

result = True and side_effect()   # 印出「函式被呼叫了」
print(result)  # True

# or 短路：第一個為 True 就不會評估第二個
result = True or side_effect()    # 不會印出任何東西
print(result)  # True

result = False or side_effect()   # 印出「函式被呼叫了」
print(result)  # True
```

### 回傳值行為

與 JavaScript 相同，Python 的 `and` 和 `or` 回傳的是**運算元本身**，不一定是布林值：

```python
# and 回傳第一個 Falsy 值，或最後一個值
print(1 and 2 and 3)        # 3（全部 Truthy，回傳最後一個）
print(1 and 0 and 3)        # 0（第一個 Falsy 值）
print(1 and "" and 3)       # ""（空字串是 Falsy）
print([] and "hello")       # []（空列表是 Falsy）

# or 回傳第一個 Truthy 值，或最後一個值
print(0 or "" or "hello")   # "hello"（第一個 Truthy 值）
print(0 or "" or [])        # []（全部 Falsy，回傳最後一個）
print("first" or "second")  # "first"（第一個就是 Truthy）

# 實用技巧：設定預設值
name = user_input or "Anonymous"
config = custom_config or default_config

# JavaScript 對照：
# const name = userInput || "Anonymous";
```

### 與 JavaScript 的差異

```python
# Python 使用英文單字
if a and b:
    pass

if a or b:
    pass

if not a:
    pass

# JavaScript 使用符號
# if (a && b) { }
# if (a || b) { }
# if (!a) { }

# 注意：Python 中 and、or、not 是關鍵字，不能作為變數名
# and = 1  # SyntaxError
```

### 運算子優先順序

```python
# 優先順序（由高到低）：not > and > or
print(True or False and False)     # True（先算 and）
print((True or False) and False)   # False

print(not False or True)           # True（not 優先）
print(not (False or True))         # False

# 建議：使用括號明確表達意圖
result = (a and b) or (c and d)
```

---

## 4.3 算術運算子

### 運算子對照表

| 運算 | JavaScript | Python | 說明 |
|------|------------|--------|------|
| 加法 | `+` | `+` | 相同 |
| 減法 | `-` | `-` | 相同 |
| 乘法 | `*` | `*` | 相同 |
| 除法 | `/` | `/` | Python 回傳 float |
| 整數除法 | `Math.floor(a/b)` | `//` | Python 特有運算子 |
| 取餘數 | `%` | `%` | 負數行為不同 |
| 次方 | `**` | `**` | 相同 |

### 除法運算

```python
# 真除法 / - 總是回傳浮點數
print(10 / 3)       # 3.3333333333333335
print(10 / 2)       # 5.0（即使整除也是 float）
print(type(10 / 2)) # <class 'float'>

# 整數除法 // - 向下取整
print(10 // 3)      # 3
print(10 // 2)      # 5
print(type(10 // 2))  # <class 'int'>

# 負數的整數除法（向負無限大取整）
print(7 // 3)       # 2
print(-7 // 3)      # -3（不是 -2！）
print(7 // -3)      # -3

# JavaScript 對照：
# 10 / 3           → 3.3333...
# Math.floor(10/3) → 3
# Math.floor(-7/3) → -3（與 Python 相同）
# Math.trunc(-7/3) → -2（向零取整）

# Python 的 int() 是向零取整
print(int(-7 / 3))  # -2（與 Math.trunc 相同）
```

### 取餘數運算

```python
# 正數取餘數
print(10 % 3)       # 1
print(10 % 5)       # 0

# 負數取餘數（Python 與 JavaScript 不同！）
print(-7 % 3)       # 2（Python）
# JavaScript: -7 % 3 → -1

print(7 % -3)       # -2（Python）
# JavaScript: 7 % -3 → 1

# Python 的規則：餘數的符號與除數相同
# JavaScript 的規則：餘數的符號與被除數相同

# 數學關係：a = (a // b) * b + (a % b)
a, b = -7, 3
print(a // b)       # -3
print(a % b)        # 2
print((a // b) * b + (a % b))  # -7（驗證）
```

### divmod() 函式

```python
# 同時取得商和餘數
quotient, remainder = divmod(10, 3)
print(quotient)     # 3
print(remainder)    # 1

# 等價於
quotient = 10 // 3
remainder = 10 % 3

# 實用範例：時間轉換
total_seconds = 3665
minutes, seconds = divmod(total_seconds, 60)
hours, minutes = divmod(minutes, 60)
print(f"{hours}:{minutes}:{seconds}")  # 1:1:5
```

### 次方運算

```python
# ** 次方運算子
print(2 ** 10)      # 1024
print(10 ** 2)      # 100
print(2 ** 0.5)     # 1.4142...（平方根）
print(8 ** (1/3))   # 2.0（立方根）

# 負次方
print(2 ** -1)      # 0.5
print(10 ** -2)     # 0.01

# pow() 內建函式
print(pow(2, 10))   # 1024

# pow() 可以做模次方（效率較高）
print(pow(2, 10, 100))  # 24（等於 2**10 % 100）

# math 模組的函式
import math
print(math.sqrt(16))    # 4.0（平方根）
print(math.pow(2, 10))  # 1024.0（回傳 float）
```

### 複合賦值運算子

```python
x = 10

x += 5      # x = x + 5  → 15
x -= 3      # x = x - 3  → 12
x *= 2      # x = x * 2  → 24
x /= 4      # x = x / 4  → 6.0
x //= 2     # x = x // 2 → 3.0
x %= 2      # x = x % 2  → 1.0
x **= 3     # x = x ** 3 → 1.0

# 注意：Python 沒有 ++ 和 -- 運算子
# x++  # SyntaxError
# ++x  # 這是合法的，但意思是正號，不是遞增

# 使用 += 1 代替
x += 1
x -= 1
```

---

## 4.4 位元運算子

### 運算子對照表

| 運算 | JavaScript | Python | 說明 |
|------|------------|--------|------|
| AND | `&` | `&` | 相同 |
| OR | `\|` | `\|` | 相同 |
| XOR | `^` | `^` | 相同 |
| NOT | `~` | `~` | 相同 |
| 左移 | `<<` | `<<` | 相同 |
| 右移 | `>>` | `>>` | Python 保留符號 |
| 無符號右移 | `>>>` | 無 | Python 沒有 |

### 基本位元運算

```python
a = 0b1010  # 10
b = 0b1100  # 12

# AND - 兩者都是 1 才是 1
print(bin(a & b))   # 0b1000 (8)

# OR - 任一為 1 就是 1
print(bin(a | b))   # 0b1110 (14)

# XOR - 不同才是 1
print(bin(a ^ b))   # 0b0110 (6)

# NOT - 取反（注意：Python 整數無限精度）
print(bin(~a))      # -0b1011 (-11)
# ~x 等於 -(x+1)

# 左移 - 乘以 2 的 n 次方
print(5 << 1)       # 10（5 * 2）
print(5 << 2)       # 20（5 * 4）

# 右移 - 除以 2 的 n 次方（向下取整）
print(20 >> 1)      # 10（20 / 2）
print(20 >> 2)      # 5（20 / 4）
print(-20 >> 1)     # -10（保留符號）
```

### 位元運算的實際應用

```python
# 檢查奇偶數
def is_odd(n):
    return n & 1 == 1

print(is_odd(5))    # True
print(is_odd(4))    # False

# 交換兩個數（不使用暫存變數）
a, b = 5, 3
a ^= b
b ^= a
a ^= b
print(a, b)         # 3, 5

# 位元遮罩（flags）
READ = 0b001
WRITE = 0b010
EXECUTE = 0b100

permissions = READ | WRITE  # 設定權限
print(permissions & READ)   # 1（有讀取權限）
print(permissions & EXECUTE)  # 0（無執行權限）

# 新增權限
permissions |= EXECUTE

# 移除權限
permissions &= ~WRITE
```

---

## 4.5 成員運算子（in / not in）

Python 特有的運算子，用於檢查元素是否存在於序列中：

```python
# 列表
fruits = ["apple", "banana", "cherry"]
print("banana" in fruits)       # True
print("grape" in fruits)        # False
print("grape" not in fruits)    # True

# 字串
text = "Hello, World!"
print("World" in text)          # True
print("world" in text)          # False（區分大小寫）

# 字典（檢查鍵）
person = {"name": "Alice", "age": 30}
print("name" in person)         # True
print("Alice" in person)        # False（檢查的是鍵，不是值）
print("Alice" in person.values())  # True

# 集合
colors = {"red", "green", "blue"}
print("red" in colors)          # True

# 範圍
print(5 in range(10))           # True
print(10 in range(10))          # False（不包含 10）

# JavaScript 對照：
# fruits.includes("banana")
# text.includes("World")
# "name" in person（JS 的 in 也是檢查鍵）
```

### 效能考量

```python
# list 的 in 是 O(n)
large_list = list(range(1000000))
print(999999 in large_list)     # 慢

# set 的 in 是 O(1)
large_set = set(range(1000000))
print(999999 in large_set)      # 快

# dict 的 in 也是 O(1)
large_dict = {i: i for i in range(1000000)}
print(999999 in large_dict)     # 快

# 如果需要頻繁檢查成員，考慮轉換為 set
```

---

## 4.6 三元運算子

### JavaScript 的三元運算子

```javascript
// condition ? valueIfTrue : valueIfFalse
const result = score >= 60 ? "Pass" : "Fail";
const status = user ? user.name : "Anonymous";
```

### Python 的條件表達式

```python
# valueIfTrue if condition else valueIfFalse
result = "Pass" if score >= 60 else "Fail"
status = user.name if user else "Anonymous"

# 注意順序與 JavaScript 不同！
# JavaScript: condition ? true : false
# Python:     true if condition else false
```

### 巢狀條件表達式

```python
# 巢狀（但不建議過度使用，影響可讀性）
score = 85

# 方式 1：巢狀條件表達式
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"

# 方式 2：更清楚的格式
grade = (
    "A" if score >= 90 else
    "B" if score >= 80 else
    "C" if score >= 70 else
    "F"
)

# 方式 3：使用 if-elif-else（更推薦）
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

### 條件表達式的實際應用

```python
# 取得預設值
name = user_name if user_name else "Anonymous"

# 更簡潔的寫法（如果只是要預設值）
name = user_name or "Anonymous"

# 安全除法
result = a / b if b != 0 else 0

# 列表推導式中使用
numbers = [1, -2, 3, -4, 5]
absolute = [x if x >= 0 else -x for x in numbers]
# [1, 2, 3, 4, 5]

# 條件性格式化
items = ["apple", "banana", "cherry"]
message = f"有 {len(items)} 個{'項目' if len(items) > 1 else '項目'}"
```

---

## 4.7 海象運算子（:=）

Python 3.8 引入的賦值表達式運算子，可以在表達式中同時賦值和使用變數：

```python
# 傳統寫法
line = input()
while line != "quit":
    print(f"你輸入了: {line}")
    line = input()

# 使用海象運算子
while (line := input()) != "quit":
    print(f"你輸入了: {line}")

# 在條件判斷中使用
data = {"value": 42}
if (value := data.get("value")) is not None:
    print(f"找到值: {value}")

# 在列表推導式中使用（避免重複計算）
import re

text = "apple 123 banana 456 cherry 789"

# 傳統寫法（正規表達式執行兩次）
results = [
    match.group()
    for word in text.split()
    if (match := re.search(r'\d+', word))
]

# 更實際的例子：過濾並轉換
numbers = ["1", "2", "abc", "3", "def"]

# 傳統寫法
valid = []
for s in numbers:
    try:
        n = int(s)
        valid.append(n)
    except ValueError:
        pass

# 使用海象運算子的函式
def try_int(s):
    try:
        return int(s)
    except ValueError:
        return None

valid = [n for s in numbers if (n := try_int(s)) is not None]
# [1, 2, 3]
```

### 海象運算子的限制

```python
# 不能用於獨立的賦值語句
# (x := 10)  # 可以，但沒必要
# x := 10    # SyntaxError

# 需要括號來避免歧義
# if x := get_value() > 0:  # SyntaxError
if (x := get_value()) > 0:  # 正確
    pass
```

---

## 4.8 字串運算子

### 字串連接與重複

```python
# 連接（+）
greeting = "Hello" + ", " + "World!"
print(greeting)  # "Hello, World!"

# 重複（*）
line = "-" * 50
print(line)  # "--------------------------------------------------"

pattern = "ab" * 3
print(pattern)  # "ababab"

# 連接賦值（+=）
message = "Hello"
message += " World"
print(message)  # "Hello World"
```

### 字串格式化運算子

```python
# % 格式化（舊式）
name = "Alice"
age = 30
print("Name: %s, Age: %d" % (name, age))

# 格式規範
print("%10s" % "hi")      # "        hi"（寬度 10，右對齊）
print("%-10s" % "hi")     # "hi        "（左對齊）
print("%.2f" % 3.14159)   # "3.14"（小數點後 2 位）
print("%05d" % 42)        # "00042"（補零）

# f-string（推薦，Python 3.6+）
print(f"Name: {name}, Age: {age}")
print(f"{name:>10}")      # "     Alice"（寬度 10，右對齊）
print(f"{3.14159:.2f}")   # "3.14"
```

---

## 4.9 特殊運算子

### 展開運算子（* 和 **）

```python
# * 展開可迭代物件
numbers = [1, 2, 3]
print(*numbers)           # 1 2 3（等於 print(1, 2, 3)）

combined = [*numbers, 4, 5]  # [1, 2, 3, 4, 5]

# ** 展開字典
defaults = {"color": "blue", "size": "medium"}
custom = {"size": "large", "price": 100}
merged = {**defaults, **custom}
# {"color": "blue", "size": "large", "price": 100}

# 函式參數展開
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

args = ["Alice"]
kwargs = {"greeting": "Hi"}
greet(*args, **kwargs)    # "Hi, Alice!"

# JavaScript 對照：
# const combined = [...numbers, 4, 5];
# const merged = { ...defaults, ...custom };
# greet(...args);
```

### @ 矩陣乘法運算子

```python
# Python 3.5+ 引入，用於矩陣乘法
# 需要支援該運算子的物件（如 NumPy）

import numpy as np

a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

# 矩陣乘法
result = a @ b
print(result)
# [[19 22]
#  [43 50]]

# 等價於
result = np.matmul(a, b)
```

---

## 練習題

### 練習 1：比較運算子

預測以下程式碼的輸出：

```python
# 請先預測，再執行驗證
print(1 == True)
print(0 == False)
print(1 is True)
print([] == False)
print(not [])
print(1 < 2 < 3)
print(1 < 2 > 1)
```

### 練習 2：邏輯運算子

寫出以下表達式的結果：

```python
print(0 or 1 or 2)
print(1 and 2 and 3)
print(0 and 1 or 2)
print(1 or 0 and 2)
print(not 0 and not "")
```

### 練習 3：實作安全除法

寫一個函式，使用條件表達式實作安全除法：

```python
def safe_divide(a, b, default=0):
    # 你的程式碼（使用條件表達式）
    pass

print(safe_divide(10, 2))    # 5.0
print(safe_divide(10, 0))    # 0
print(safe_divide(10, 0, -1))  # -1
```

### 練習 4：取餘數

計算以下表達式的結果，並解釋為什麼：

```python
print(7 % 3)
print(-7 % 3)
print(7 % -3)
print(-7 % -3)
```

---

## 小結

### 運算子對照總表

| 類型 | JavaScript | Python | 說明 |
|------|------------|--------|------|
| 嚴格相等 | `===` | `==` | Python 不做隱式轉換 |
| 不相等 | `!==` | `!=` | |
| 且 | `&&` | `and` | |
| 或 | `\|\|` | `or` | |
| 非 | `!` | `not` | |
| 整數除法 | `Math.floor(a/b)` | `//` | |
| 次方 | `**` | `**` | 相同 |
| 身份比較 | `===`（部分情況） | `is` | Python 比較記憶體位址 |
| 成員檢查 | `includes()` / `in` | `in` | |
| 三元 | `? :` | `if else` | 語法順序不同 |
| 展開 | `...` | `*` / `**` | |
| 賦值表達式 | 無 | `:=` | Python 3.8+ |
| 鏈式比較 | 無 | `a < b < c` | Python 特有 |

### 主要差異提醒

1. **相等比較**：Python 的 `==` 不做型別轉換，類似 JS 的 `===`
2. **邏輯運算子**：使用英文單字 `and`、`or`、`not`
3. **身份比較**：比較 `None` 時使用 `is`，不要用 `==`
4. **整數除法**：使用 `//` 運算子
5. **取餘數**：負數時行為與 JavaScript 不同
6. **三元運算子**：語法順序是 `true if condition else false`
7. **沒有 `++` / `--`**：使用 `+= 1` / `-= 1`
8. **鏈式比較**：可以寫 `1 < x < 10`
