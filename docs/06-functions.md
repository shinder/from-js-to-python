# 第 6 章：函式

本章節詳細說明 Python 函式的定義、參數處理、Lambda 表達式，以及函式作為一等公民的特性。

---

## 6.1 定義與呼叫

### 基本函式定義

#### JavaScript 的函式定義

```javascript
// 函式宣告
function greet(name) {
    return `Hello, ${name}!`;
}

// 函式表達式
const greet = function(name) {
    return `Hello, ${name}!`;
};

// 箭頭函式
const greet = (name) => `Hello, ${name}!`;
```

#### Python 的函式定義

```python
# 使用 def 關鍵字定義函式
def greet(name):
    return f"Hello, {name}!"

# 呼叫函式
message = greet("Alice")
print(message)  # "Hello, Alice!"

# 多行函式
def calculate_area(width, height):
    area = width * height
    return area

# 沒有 return 或 return 無值時，回傳 None
def print_greeting(name):
    print(f"Hello, {name}!")
    # 隱式回傳 None

result = print_greeting("Bob")
print(result)  # None
```

### 函式文件字串（Docstring）

```python
def calculate_bmi(weight, height):
    """計算身體質量指數（BMI）。

    根據體重和身高計算 BMI 值，用於評估體重是否健康。

    Args:
        weight: 體重，單位為公斤（kg）
        height: 身高，單位為公尺（m）

    Returns:
        BMI 值（float），計算公式為 weight / height²

    Raises:
        ValueError: 當 weight 或 height 為負數或零時

    Examples:
        >>> calculate_bmi(70, 1.75)
        22.857142857142858
        >>> calculate_bmi(60, 1.60)
        23.4375
    """
    if weight <= 0 or height <= 0:
        raise ValueError("體重和身高必須為正數")
    return weight / (height ** 2)

# 存取 docstring
print(calculate_bmi.__doc__)

# 使用 help() 查看說明
help(calculate_bmi)
```

### 多個回傳值

Python 函式可以回傳多個值（實際上是回傳一個元組）：

```python
def get_min_max(numbers):
    """回傳列表中的最小值和最大值"""
    return min(numbers), max(numbers)

# 接收多個回傳值
minimum, maximum = get_min_max([3, 1, 4, 1, 5, 9, 2, 6])
print(f"最小: {minimum}, 最大: {maximum}")

# 也可以接收為元組
result = get_min_max([3, 1, 4, 1, 5, 9, 2, 6])
print(result)        # (1, 9)
print(result[0])     # 1

# 回傳更多值
def analyze_text(text):
    """分析文字，回傳各種統計資訊"""
    words = text.split()
    return len(text), len(words), len(set(words))

char_count, word_count, unique_count = analyze_text("hello world hello")
print(f"字元: {char_count}, 單字: {word_count}, 不重複: {unique_count}")

# JavaScript 對照（使用物件或陣列）
# function getMinMax(numbers) {
#     return { min: Math.min(...numbers), max: Math.max(...numbers) };
# }
# const { min, max } = getMinMax([3, 1, 4, 1, 5]);
```

### 函式註解（Type Hints）

```python
# 基本型別註解
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

def divide(a: float, b: float) -> float:
    return a / b

# 複雜型別
from typing import List, Dict, Optional, Union, Tuple

def process_items(items: List[str]) -> Dict[str, int]:
    """計算每個項目出現的次數"""
    result = {}
    for item in items:
        result[item] = result.get(item, 0) + 1
    return result

def find_user(user_id: int) -> Optional[Dict[str, any]]:
    """找到使用者回傳資料，否則回傳 None"""
    # ...
    pass

def parse_value(value: Union[str, int]) -> int:
    """接受字串或整數，回傳整數"""
    if isinstance(value, str):
        return int(value)
    return value

def get_coordinates() -> Tuple[float, float]:
    """回傳經緯度座標"""
    return (25.0330, 121.5654)

# Python 3.10+ 可以用更簡潔的語法
def find_user(user_id: int) -> dict | None:
    pass

def parse_value(value: str | int) -> int:
    pass
```

---

## 6.2 預設參數與關鍵字參數

### 預設參數

```python
def greet(name, greeting="Hello"):
    """greeting 有預設值"""
    return f"{greeting}, {name}!"

# 使用預設值
print(greet("Alice"))              # "Hello, Alice!"

# 覆蓋預設值
print(greet("Alice", "Hi"))        # "Hi, Alice!"

# 多個預設參數
def create_user(name, age=0, city="Unknown", active=True):
    return {
        "name": name,
        "age": age,
        "city": city,
        "active": active
    }

print(create_user("Alice"))
# {"name": "Alice", "age": 0, "city": "Unknown", "active": True}

print(create_user("Bob", 30, "Taipei"))
# {"name": "Bob", "age": 30, "city": "Taipei", "active": True}
```

### 預設參數的陷阱：可變物件

```python
# 危險！使用可變物件作為預設值
def add_item_bad(item, items=[]):
    items.append(item)
    return items

print(add_item_bad("a"))  # ["a"]
print(add_item_bad("b"))  # ["a", "b"] - 預期是 ["b"]！
print(add_item_bad("c"))  # ["a", "b", "c"] - 預期是 ["c"]！

# 原因：預設值在函式定義時建立，不是每次呼叫時建立
# 所有呼叫共享同一個列表物件

# 正確做法：使用 None 作為預設值
def add_item_good(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item_good("a"))  # ["a"]
print(add_item_good("b"))  # ["b"]
print(add_item_good("c"))  # ["c"]

# 同樣適用於字典
def update_config_bad(key, value, config={}):
    config[key] = value
    return config

def update_config_good(key, value, config=None):
    if config is None:
        config = {}
    config[key] = value
    return config
```

### 關鍵字參數

```python
def create_user(name, age, city, active=True):
    return {"name": name, "age": age, "city": city, "active": active}

# 位置參數（按順序）
user1 = create_user("Alice", 30, "Taipei")

# 關鍵字參數（按名稱）
user2 = create_user(name="Bob", age=25, city="Tokyo")

# 混合使用（位置參數必須在關鍵字參數之前）
user3 = create_user("Charlie", age=35, city="Seoul")

# 關鍵字參數可以不按順序
user4 = create_user(city="Paris", name="David", age=40)

# 只覆蓋特定的預設值
user5 = create_user("Eve", 28, "London", active=False)

# JavaScript 對照（使用物件解構模擬）
# function createUser({ name, age, city, active = true }) {
#     return { name, age, city, active };
# }
# createUser({ name: "Alice", age: 30, city: "Taipei" });
```

### 強制關鍵字參數

使用 `*` 分隔，之後的參數必須用關鍵字方式傳遞：

```python
# * 之後的參數必須使用關鍵字
def create_user(name, *, age, city, active=True):
    return {"name": name, "age": age, "city": city, "active": active}

# 正確
user = create_user("Alice", age=30, city="Taipei")

# 錯誤
# user = create_user("Alice", 30, "Taipei")
# TypeError: create_user() takes 1 positional argument but 3 were given

# 實際應用：避免參數順序錯誤
def connect_database(host, *, port=5432, user, password, database):
    """強制使用關鍵字參數，避免混淆"""
    pass

# 必須明確指定每個參數
connect_database(
    "localhost",
    port=5432,
    user="admin",
    password="secret",
    database="mydb"
)
```

### 僅限位置參數（Python 3.8+）

使用 `/` 分隔，之前的參數只能用位置方式傳遞：

```python
# / 之前的參數只能用位置方式傳遞
def greet(name, /, greeting="Hello"):
    return f"{greeting}, {name}!"

# 正確
print(greet("Alice"))
print(greet("Alice", "Hi"))
print(greet("Alice", greeting="Hi"))

# 錯誤
# print(greet(name="Alice"))
# TypeError: greet() got some positional-only arguments passed as keyword arguments

# 結合使用
def func(pos_only, /, standard, *, kw_only):
    """
    pos_only: 只能用位置方式
    standard: 位置或關鍵字皆可
    kw_only: 只能用關鍵字方式
    """
    pass

func(1, 2, kw_only=3)        # 正確
func(1, standard=2, kw_only=3)  # 正確
# func(pos_only=1, standard=2, kw_only=3)  # 錯誤
```

---

## 6.3 可變參數

### *args：接收任意數量的位置參數

```python
def sum_all(*args):
    """接收任意數量的數字並求和"""
    print(f"收到 {len(args)} 個參數: {args}")
    return sum(args)

print(sum_all(1, 2, 3))        # 6
print(sum_all(1, 2, 3, 4, 5))  # 15
print(sum_all())               # 0

# args 是一個元組
def show_args(*args):
    print(type(args))  # <class 'tuple'>
    for i, arg in enumerate(args):
        print(f"  args[{i}] = {arg}")

show_args("a", "b", "c")

# 結合一般參數
def greet_all(greeting, *names):
    """第一個參數是問候語，其餘是名字"""
    for name in names:
        print(f"{greeting}, {name}!")

greet_all("Hello", "Alice", "Bob", "Charlie")
# Hello, Alice!
# Hello, Bob!
# Hello, Charlie!

# JavaScript 對照
# function sumAll(...args) {
#     return args.reduce((a, b) => a + b, 0);
# }
```

### **kwargs：接收任意數量的關鍵字參數

```python
def print_info(**kwargs):
    """接收任意數量的關鍵字參數"""
    print(f"收到 {len(kwargs)} 個參數")
    for key, value in kwargs.items():
        print(f"  {key} = {value}")

print_info(name="Alice", age=30, city="Taipei")
# 收到 3 個參數
#   name = Alice
#   age = 30
#   city = Taipei

# kwargs 是一個字典
def show_kwargs(**kwargs):
    print(type(kwargs))  # <class 'dict'>

# 結合一般參數
def create_user(name, **extra):
    user = {"name": name}
    user.update(extra)
    return user

print(create_user("Alice", age=30, city="Taipei"))
# {"name": "Alice", "age": 30, "city": "Taipei"}
```

### 結合 *args 和 **kwargs

```python
def flexible_function(*args, **kwargs):
    """接收任意參數"""
    print(f"位置參數: {args}")
    print(f"關鍵字參數: {kwargs}")

flexible_function(1, 2, 3, name="Alice", age=30)
# 位置參數: (1, 2, 3)
# 關鍵字參數: {"name": "Alice", "age": 30}

# 完整的參數順序
def full_example(pos1, pos2, /, standard, *args, kw_only, **kwargs):
    """
    參數順序：
    1. 僅限位置參數（/ 之前）
    2. 一般參數
    3. *args
    4. 僅限關鍵字參數（* 之後或 *args 之後）
    5. **kwargs
    """
    pass

# 常見用法：裝飾器和包裝函式
def wrapper(*args, **kwargs):
    """包裝任意函式"""
    print("函式呼叫前")
    result = original_function(*args, **kwargs)
    print("函式呼叫後")
    return result
```

### 參數展開

```python
# 展開列表/元組作為位置參數
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
print(add(*numbers))  # 6

# 展開字典作為關鍵字參數
def greet(name, greeting):
    return f"{greeting}, {name}!"

params = {"name": "Alice", "greeting": "Hello"}
print(greet(**params))  # "Hello, Alice!"

# 結合使用
args = [1, 2]
kwargs = {"c": 3}
print(add(*args, **kwargs))  # 6

# 合併字典
defaults = {"color": "blue", "size": "medium"}
custom = {"size": "large", "price": 100}
merged = {**defaults, **custom}
print(merged)  # {"color": "blue", "size": "large", "price": 100}

# JavaScript 對照
# const numbers = [1, 2, 3];
# add(...numbers);
#
# const params = { name: "Alice", greeting: "Hello" };
# greet(params);  // JS 沒有 ** 展開，需要解構
```

---

## 6.4 Lambda 函式

### 基本語法

```python
# lambda 參數: 表達式

# 一般函式
def add(x, y):
    return x + y

# 等價的 lambda
add = lambda x, y: x + y

print(add(3, 5))  # 8

# 單一參數
double = lambda x: x * 2
print(double(5))  # 10

# 無參數
get_greeting = lambda: "Hello, World!"
print(get_greeting())  # "Hello, World!"

# JavaScript 對照
# const add = (x, y) => x + y;
# const double = x => x * 2;
# const getGreeting = () => "Hello, World!";
```

### Lambda 的限制

```python
# Lambda 只能包含單一表達式，不能包含：
# - 多行語句
# - 賦值語句（= 但可以用 :=）
# - if/for/while 等語句

# 錯誤示範
# bad_lambda = lambda x:
#     result = x * 2
#     return result

# 可以使用條件表達式
check = lambda x: "正數" if x > 0 else "非正數"
print(check(5))   # "正數"
print(check(-3))  # "非正數"

# 複雜邏輯應該使用一般函式
def complex_logic(x):
    if x > 0:
        result = x * 2
    else:
        result = x * -1
    return result + 10
```

### Lambda 的常見用途

#### 作為排序的 key 函式

```python
# 按絕對值排序
numbers = [-5, 2, -1, 8, -3]
sorted_numbers = sorted(numbers, key=lambda x: abs(x))
print(sorted_numbers)  # [-1, 2, -3, -5, 8]

# 按字串長度排序
words = ["apple", "pie", "banana", "kiwi"]
sorted_words = sorted(words, key=lambda x: len(x))
print(sorted_words)  # ["pie", "kiwi", "apple", "banana"]

# 按字典的值排序
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78},
]
sorted_students = sorted(students, key=lambda x: x["grade"], reverse=True)
# [{"name": "Bob", ...}, {"name": "Alice", ...}, {"name": "Charlie", ...}]

# 多條件排序
data = [("Alice", 30), ("Bob", 25), ("Alice", 25)]
sorted_data = sorted(data, key=lambda x: (x[0], x[1]))
# [("Alice", 25), ("Alice", 30), ("Bob", 25)]
```

#### 搭配 map()、filter()、reduce()

```python
# map() - 對每個元素套用函式
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, numbers))
print(doubled)  # [2, 4, 6, 8, 10]

# 建議使用列表推導式
doubled = [x * 2 for x in numbers]

# filter() - 過濾元素
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4]

# 建議使用列表推導式
evens = [x for x in numbers if x % 2 == 0]

# reduce() - 累積運算
from functools import reduce

total = reduce(lambda acc, x: acc + x, numbers, 0)
print(total)  # 15

# 建議使用內建函式
total = sum(numbers)

# reduce 的實際應用：找最大值
maximum = reduce(lambda a, b: a if a > b else b, numbers)
print(maximum)  # 5

# 建議使用內建函式
maximum = max(numbers)
```

#### 作為回呼函式

```python
# 簡單的回呼
def process_data(data, callback):
    result = callback(data)
    return result

# 使用 lambda 作為回呼
print(process_data(10, lambda x: x ** 2))  # 100
print(process_data("hello", lambda x: x.upper()))  # "HELLO"

# 事件處理（模擬）
def on_click(handler):
    print("按鈕被點擊")
    handler()

on_click(lambda: print("執行動作"))
```

### Lambda vs 一般函式

```python
# 何時使用 Lambda
# 1. 簡單的單一表達式
# 2. 作為其他函式的參數（排序、map、filter 等）
# 3. 不需要重複使用的臨時函式

# 何時使用一般函式
# 1. 需要多行邏輯
# 2. 需要文件字串（docstring）
# 3. 需要重複使用
# 4. 邏輯複雜，需要良好的可讀性

# 不好：過於複雜的 lambda
process = lambda x: (x ** 2 if x > 0 else -x) * 2 + (10 if x % 2 == 0 else 5)

# 好：使用一般函式
def process(x):
    """處理數字的函式"""
    if x > 0:
        base = x ** 2
    else:
        base = -x

    multiplied = base * 2

    if x % 2 == 0:
        return multiplied + 10
    else:
        return multiplied + 5
```

---

## 6.5 一等公民函式

在 Python 中，函式是「一等公民」（First-class Citizen），這意味著函式可以：
- 賦值給變數
- 作為參數傳遞
- 作為回傳值
- 儲存在資料結構中

### 函式賦值給變數

```python
def greet(name):
    return f"Hello, {name}!"

# 將函式賦值給變數
say_hello = greet

print(say_hello("Alice"))  # "Hello, Alice!"
print(greet("Alice"))      # "Hello, Alice!"

# 它們指向同一個函式物件
print(say_hello is greet)  # True

# 函式也有屬性
print(greet.__name__)      # "greet"
print(say_hello.__name__)  # "greet"
```

### 函式作為參數

```python
def apply_operation(x, y, operation):
    """接收兩個數字和一個操作函式"""
    return operation(x, y)

def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

print(apply_operation(5, 3, add))       # 8
print(apply_operation(5, 3, multiply))  # 15

# 使用 lambda
print(apply_operation(5, 3, lambda a, b: a - b))  # 2
print(apply_operation(5, 3, lambda a, b: a ** b)) # 125

# 實際應用：自訂處理器
def process_list(items, processor):
    """對列表中的每個元素套用處理函式"""
    return [processor(item) for item in items]

numbers = [1, 2, 3, 4, 5]
print(process_list(numbers, lambda x: x * 2))     # [2, 4, 6, 8, 10]
print(process_list(numbers, lambda x: x ** 2))    # [1, 4, 9, 16, 25]
print(process_list(numbers, str))                  # ["1", "2", "3", "4", "5"]
```

### 函式作為回傳值

```python
def create_multiplier(factor):
    """建立一個乘法函式"""
    def multiplier(x):
        return x * factor
    return multiplier

double = create_multiplier(2)
triple = create_multiplier(3)

print(double(5))   # 10
print(triple(5))   # 15

# 這是閉包（Closure）的應用
def create_counter():
    """建立一個計數器"""
    count = 0

    def counter():
        nonlocal count
        count += 1
        return count

    return counter

counter1 = create_counter()
counter2 = create_counter()

print(counter1())  # 1
print(counter1())  # 2
print(counter1())  # 3
print(counter2())  # 1（獨立的計數器）

# 實際應用：建立驗證器
def create_validator(min_val, max_val):
    """建立範圍驗證函式"""
    def validator(value):
        return min_val <= value <= max_val
    return validator

is_valid_age = create_validator(0, 150)
is_valid_score = create_validator(0, 100)

print(is_valid_age(25))    # True
print(is_valid_age(200))   # False
print(is_valid_score(85))  # True
```

### 函式儲存在資料結構中

```python
# 儲存在列表中
operations = [
    lambda x: x + 1,
    lambda x: x * 2,
    lambda x: x ** 2,
]

value = 5
for op in operations:
    value = op(value)
print(value)  # ((5 + 1) * 2) ** 2 = 144

# 儲存在字典中（命令模式）
def start():
    return "啟動中..."

def stop():
    return "停止中..."

def restart():
    return "重新啟動中..."

commands = {
    "start": start,
    "stop": stop,
    "restart": restart,
}

def execute_command(cmd):
    if cmd in commands:
        return commands[cmd]()
    return "未知命令"

print(execute_command("start"))    # "啟動中..."
print(execute_command("restart"))  # "重新啟動中..."
print(execute_command("unknown"))  # "未知命令"

# 更進階：策略模式
strategies = {
    "sum": lambda data: sum(data),
    "average": lambda data: sum(data) / len(data) if data else 0,
    "max": lambda data: max(data) if data else None,
    "min": lambda data: min(data) if data else None,
}

def analyze(data, strategy_name):
    strategy = strategies.get(strategy_name)
    if strategy:
        return strategy(data)
    raise ValueError(f"未知策略: {strategy_name}")

numbers = [1, 2, 3, 4, 5]
print(analyze(numbers, "sum"))      # 15
print(analyze(numbers, "average"))  # 3.0
print(analyze(numbers, "max"))      # 5
```

### 閉包（Closure）

```python
def outer_function(x):
    """外層函式"""

    def inner_function(y):
        """內層函式可以存取外層函式的變數"""
        return x + y  # x 來自外層函式

    return inner_function

add_five = outer_function(5)
add_ten = outer_function(10)

print(add_five(3))   # 8（5 + 3）
print(add_ten(3))    # 13（10 + 3）

# 閉包會「記住」外層函式的變數
print(add_five.__closure__)  # (<cell ...>,)
print(add_five.__closure__[0].cell_contents)  # 5

# 實際應用：記錄日誌
def create_logger(prefix):
    """建立帶有前綴的日誌函式"""
    def logger(message):
        print(f"[{prefix}] {message}")
    return logger

info_log = create_logger("INFO")
error_log = create_logger("ERROR")

info_log("應用程式啟動")     # [INFO] 應用程式啟動
error_log("發生錯誤")        # [ERROR] 發生錯誤
```

### nonlocal 關鍵字

```python
# 在內層函式中修改外層函式的變數
def create_accumulator():
    total = 0

    def add(value):
        nonlocal total  # 宣告要修改外層變數
        total += value
        return total

    return add

acc = create_accumulator()
print(acc(10))   # 10
print(acc(20))   # 30
print(acc(5))    # 35

# 不使用 nonlocal 會建立新的區域變數
def broken_accumulator():
    total = 0

    def add(value):
        # total = total + value  # UnboundLocalError!
        # Python 認為 total 是區域變數，但還沒賦值就使用
        pass

    return add

# global vs nonlocal
global_var = 0

def outer():
    outer_var = 0

    def inner():
        global global_var   # 修改全域變數
        nonlocal outer_var  # 修改外層函式的變數

        global_var += 1
        outer_var += 1

    inner()
    return outer_var

print(outer())      # 1
print(global_var)   # 1
```

---

## 6.6 內建高階函式

### map()

```python
# map(function, iterable) - 對每個元素套用函式
numbers = [1, 2, 3, 4, 5]

# 使用 map
squared = map(lambda x: x ** 2, numbers)
print(list(squared))  # [1, 4, 9, 16, 25]

# 等價的列表推導式（推薦）
squared = [x ** 2 for x in numbers]

# 多個可迭代物件
a = [1, 2, 3]
b = [4, 5, 6]
sums = map(lambda x, y: x + y, a, b)
print(list(sums))  # [5, 7, 9]

# 使用具名函式
def fahrenheit_to_celsius(f):
    return (f - 32) * 5 / 9

temps_f = [32, 68, 86, 104]
temps_c = list(map(fahrenheit_to_celsius, temps_f))
print(temps_c)  # [0.0, 20.0, 30.0, 40.0]
```

### filter()

```python
# filter(function, iterable) - 過濾元素
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# 使用 filter
evens = filter(lambda x: x % 2 == 0, numbers)
print(list(evens))  # [2, 4, 6, 8, 10]

# 等價的列表推導式（推薦）
evens = [x for x in numbers if x % 2 == 0]

# 過濾 None 和 Falsy 值
data = [1, None, 2, "", 3, 0, 4, False]

# 過濾 None
without_none = filter(lambda x: x is not None, data)
print(list(without_none))  # [1, 2, "", 3, 0, 4, False]

# 過濾所有 Falsy 值
truthy_only = filter(None, data)  # 使用 None 作為函式
print(list(truthy_only))  # [1, 2, 3, 4]
```

### reduce()

```python
from functools import reduce

# reduce(function, iterable, initializer) - 累積運算
numbers = [1, 2, 3, 4, 5]

# 求和
total = reduce(lambda acc, x: acc + x, numbers, 0)
print(total)  # 15

# 等價於
# ((((0 + 1) + 2) + 3) + 4) + 5 = 15

# 求積
product = reduce(lambda acc, x: acc * x, numbers, 1)
print(product)  # 120

# 找最大值
maximum = reduce(lambda a, b: a if a > b else b, numbers)
print(maximum)  # 5

# 實際應用：扁平化巢狀列表
nested = [[1, 2], [3, 4], [5, 6]]
flat = reduce(lambda acc, x: acc + x, nested, [])
print(flat)  # [1, 2, 3, 4, 5, 6]

# 建議：大多數情況使用內建函式或列表推導式
print(sum(numbers))     # 15
print(max(numbers))     # 5
flat = [item for sublist in nested for item in sublist]
```

### sorted() 與 key 函式

```python
# sorted(iterable, key=None, reverse=False)

# 基本排序
print(sorted([3, 1, 4, 1, 5, 9, 2, 6]))  # [1, 1, 2, 3, 4, 5, 6, 9]

# 使用 key 函式
words = ["banana", "Apple", "cherry", "Date"]

# 按長度排序
print(sorted(words, key=len))
# ["Date", "Apple", "banana", "cherry"]

# 不分大小寫排序
print(sorted(words, key=str.lower))
# ["Apple", "banana", "cherry", "Date"]

# 使用 operator 模組
from operator import itemgetter, attrgetter

# itemgetter - 取得項目
students = [("Alice", 85), ("Bob", 92), ("Charlie", 78)]
print(sorted(students, key=itemgetter(1), reverse=True))
# [("Bob", 92), ("Alice", 85), ("Charlie", 78)]

# 多鍵排序
data = [("Alice", "A", 85), ("Bob", "B", 92), ("Alice", "B", 90)]
print(sorted(data, key=itemgetter(0, 2)))  # 先按名字，再按分數
# [("Alice", "A", 85), ("Alice", "B", 90), ("Bob", "B", 92)]

# attrgetter - 取得屬性
class Student:
    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

students = [Student("Alice", 85), Student("Bob", 92), Student("Charlie", 78)]
sorted_students = sorted(students, key=attrgetter("grade"), reverse=True)
```

---

## 練習題

### 練習 1：計算器函式

寫一個函式工廠，根據運算符號回傳對應的運算函式：

```python
def create_calculator(operator):
    # 你的程式碼
    pass

add = create_calculator("+")
subtract = create_calculator("-")
multiply = create_calculator("*")
divide = create_calculator("/")

print(add(10, 5))       # 15
print(subtract(10, 5))  # 5
print(multiply(10, 5))  # 50
print(divide(10, 5))    # 2.0
```

### 練習 2：裝飾器函式

寫一個函式，接收任意函式並回傳一個會印出執行時間的版本：

```python
import time

def timing(func):
    # 你的程式碼
    pass

@timing
def slow_function():
    time.sleep(1)
    return "完成"

result = slow_function()
# 應該印出類似 "slow_function 執行時間: 1.001 秒"
```

### 練習 3：管道函式

寫一個 `pipe` 函式，將多個函式串接起來：

```python
def pipe(*functions):
    # 你的程式碼
    pass

# 使用範例
process = pipe(
    lambda x: x + 1,
    lambda x: x * 2,
    lambda x: x ** 2
)

print(process(5))  # ((5 + 1) * 2) ** 2 = 144
```

---

## 小結

### 函式語法對照表

| 特性        | JavaScript          | Python            |
| ----------- | ------------------- | ----------------- |
| 定義        | `function f() {}`   | `def f():`        |
| 箭頭/Lambda | `(x) => x * 2`      | `lambda x: x * 2` |
| 預設參數    | `function f(x = 1)` | `def f(x=1):`     |
| 剩餘參數    | `...args`           | `*args`           |
| 關鍵字參數  | 無原生支援          | `**kwargs`        |
| 展開        | `f(...arr)`         | `f(*arr)`         |
| 回傳多值    | 回傳物件/陣列       | `return a, b`     |

### 參數類型總覽

```python
def example(
    pos_only,           # 僅限位置參數
    /,
    standard,           # 標準參數
    default=10,         # 預設參數
    *args,              # 任意位置參數
    kw_only,            # 僅限關鍵字參數
    **kwargs            # 任意關鍵字參數
):
    pass
```

### 最佳實踐

1. **使用 docstring** 記錄函式用途、參數和回傳值
2. **使用型別註解** 提高程式碼可讀性和 IDE 支援
3. **避免可變預設參數** 使用 `None` 替代 `[]` 或 `{}`
4. **保持函式簡短** 一個函式做一件事
5. **優先使用列表推導式** 而非 `map()`、`filter()`
6. **善用閉包** 建立具有狀態的函式
7. **Lambda 保持簡單** 複雜邏輯使用一般函式
