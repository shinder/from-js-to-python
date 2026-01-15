# 第 11 章：列表推導式與生成器

本章節詳細說明 Python 的推導式（Comprehension）語法和生成器（Generator），這些是 Python 最具特色且強大的功能之一。

---

## 11.1 列表推導式（List Comprehension）

列表推導式是一種簡潔、可讀性高的語法，用於建立列表。

### 基本語法

```python
# 基本結構
# [表達式 for 項目 in 可迭代物件]

# 傳統 for 迴圈
squares = []
for x in range(5):
    squares.append(x ** 2)
print(squares)  # [0, 1, 4, 9, 16]

# 列表推導式
squares = [x ** 2 for x in range(5)]
print(squares)  # [0, 1, 4, 9, 16]

# JavaScript 對照
# const squares = [...Array(5).keys()].map(x => x ** 2);
# 或
# const squares = Array.from({length: 5}, (_, x) => x ** 2);
```

### 對應 JavaScript 的 map()

```python
numbers = [1, 2, 3, 4, 5]

# JavaScript: numbers.map(x => x * 2)
# Python 列表推導式
doubled = [x * 2 for x in numbers]
print(doubled)  # [2, 4, 6, 8, 10]

# Python map()（較不推薦）
doubled = list(map(lambda x: x * 2, numbers))

# 更多範例
names = ["alice", "bob", "charlie"]

# 轉大寫
upper_names = [name.upper() for name in names]
# ["ALICE", "BOB", "CHARLIE"]

# 取得長度
lengths = [len(name) for name in names]
# [5, 3, 7]

# 套用函式
def process(x):
    return x.strip().title()

processed = [process(name) for name in names]
```

### 帶條件的推導式（filter）

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# JavaScript: numbers.filter(x => x % 2 === 0)
# Python 列表推導式
evens = [x for x in numbers if x % 2 == 0]
print(evens)  # [2, 4, 6, 8, 10]

# 結構：[表達式 for 項目 in 可迭代物件 if 條件]

# 更多範例
words = ["apple", "", "banana", "  ", "cherry", None]

# 過濾空值和空白字串
valid_words = [w for w in words if w and w.strip()]
# ["apple", "banana", "cherry"]

# 過濾並轉換
positive_squares = [x ** 2 for x in range(-5, 6) if x > 0]
# [1, 4, 9, 16, 25]
```

### 結合 map 和 filter

```python
numbers = range(-10, 11)

# JavaScript:
# numbers.filter(x => x > 0).map(x => x ** 2)

# Python：結合過濾和轉換
result = [x ** 2 for x in numbers if x > 0]
print(result)  # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# 更複雜的範例
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 45},
    {"name": "Charlie", "score": 92},
    {"name": "David", "score": 58},
]

# 取得及格學生的名字
passed = [s["name"] for s in students if s["score"] >= 60]
print(passed)  # ["Alice", "Charlie"]

# 取得及格學生的名字和分數
passed_info = [
    f"{s['name']}: {s['score']}"
    for s in students
    if s["score"] >= 60
]
# ["Alice: 85", "Charlie: 92"]
```

### 條件表達式（三元運算）

```python
numbers = [1, 2, 3, 4, 5]

# 對每個元素套用條件轉換
# [真值 if 條件 else 假值 for 項目 in 可迭代物件]
labels = ["偶數" if x % 2 == 0 else "奇數" for x in numbers]
print(labels)  # ["奇數", "偶數", "奇數", "偶數", "奇數"]

# 注意位置差異：
# if 在 for 後面：過濾（只有符合條件的才會進入結果）
# if-else 在 for 前面：轉換（每個元素都會有結果）

# 比較
[x for x in range(10) if x % 2 == 0]        # [0, 2, 4, 6, 8]
[x if x % 2 == 0 else -x for x in range(5)] # [0, -1, 2, -3, 4]

# 實際應用：資料清理
data = [1, None, 2, "", 3, 0, 4]
cleaned = [x if x is not None else 0 for x in data if x != ""]
# [1, 0, 2, 3, 0, 4]
```

### 巢狀迴圈

```python
# 雙重迴圈
# 傳統寫法
pairs = []
for x in range(3):
    for y in range(3):
        pairs.append((x, y))

# 列表推導式
pairs = [(x, y) for x in range(3) for y in range(3)]
print(pairs)
# [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]

# 相當於：
# for x in range(3):
#     for y in range(3):
#         (x, y)

# 矩陣扁平化
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# 傳統寫法
flat = []
for row in matrix:
    for num in row:
        flat.append(num)

# 列表推導式
flat = [num for row in matrix for num in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# JavaScript 對照
# matrix.flat()
# 或 matrix.reduce((acc, row) => [...acc, ...row], [])
```

### 巢狀推導式（建立多維結構）

```python
# 建立 3x3 矩陣
matrix = [[0 for _ in range(3)] for _ in range(3)]
print(matrix)  # [[0, 0, 0], [0, 0, 0], [0, 0, 0]]

# 建立乘法表
multiplication_table = [
    [i * j for j in range(1, 10)]
    for i in range(1, 10)
]

# 矩陣轉置
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
transposed = [[row[i] for row in matrix] for i in range(len(matrix[0]))]
print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]

# 注意：巢狀推導式從外到內閱讀
# [[外層表達式] for 外層項目 in 外層可迭代物件]
# 外層表達式本身也是推導式
```

### 推導式中的函式呼叫

```python
import math

numbers = [1, 4, 9, 16, 25]

# 呼叫函式
roots = [math.sqrt(x) for x in numbers]
print(roots)  # [1.0, 2.0, 3.0, 4.0, 5.0]

# 呼叫方法
words = ["  hello  ", "  world  "]
cleaned = [w.strip() for w in words]
print(cleaned)  # ["hello", "world"]

# 鏈式呼叫
cleaned_upper = [w.strip().upper() for w in words]
print(cleaned_upper)  # ["HELLO", "WORLD"]
```

### 推導式 vs map/filter

```python
numbers = [1, 2, 3, 4, 5]

# 推導式（推薦）
result = [x ** 2 for x in numbers if x % 2 == 0]

# map + filter
result = list(map(lambda x: x ** 2, filter(lambda x: x % 2 == 0, numbers)))

# 推導式優點：
# 1. 可讀性更高
# 2. 通常效能更好
# 3. 不需要 lambda
# 4. 可以直接處理多重迴圈

# map/filter 優點：
# 1. 可以使用現有函式
# 2. 惰性求值（使用 map/filter 物件時）

# 建議：大多數情況使用推導式
```

---

## 11.2 字典推導式（Dict Comprehension）

### 基本語法

```python
# 基本結構
# {鍵: 值 for 項目 in 可迭代物件}

# 從列表建立字典
names = ["alice", "bob", "charlie"]
name_lengths = {name: len(name) for name in names}
print(name_lengths)  # {"alice": 5, "bob": 3, "charlie": 7}

# 從兩個列表建立字典
keys = ["a", "b", "c"]
values = [1, 2, 3]
my_dict = {k: v for k, v in zip(keys, values)}
print(my_dict)  # {"a": 1, "b": 2, "c": 3}

# JavaScript 對照
# const obj = Object.fromEntries(keys.map((k, i) => [k, values[i]]));
```

### 帶條件的字典推導式

```python
# 過濾
numbers = {"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}
evens = {k: v for k, v in numbers.items() if v % 2 == 0}
print(evens)  # {"b": 2, "d": 4}

# 轉換鍵
original = {"name": "Alice", "age": 30}
upper_keys = {k.upper(): v for k, v in original.items()}
print(upper_keys)  # {"NAME": "Alice", "AGE": 30}

# 轉換值
prices = {"apple": 100, "banana": 50, "cherry": 200}
discounted = {k: v * 0.8 for k, v in prices.items()}
print(discounted)  # {"apple": 80.0, "banana": 40.0, "cherry": 160.0}
```

### 實際應用範例

```python
# 反轉字典
original = {"a": 1, "b": 2, "c": 3}
reversed_dict = {v: k for k, v in original.items()}
print(reversed_dict)  # {1: "a", 2: "b", 3: "c"}

# 計算單字長度
words = ["apple", "banana", "cherry"]
word_lengths = {word: len(word) for word in words}
print(word_lengths)  # {"apple": 5, "banana": 6, "cherry": 6}

# 分組計數
from collections import Counter
text = "hello world"
char_count = {char: text.count(char) for char in set(text)}
# 或直接用 Counter
char_count = dict(Counter(text))

# 篩選字典
config = {
    "debug": True,
    "host": "localhost",
    "port": 8080,
    "secret": "password123",
    "api_key": "abc123"
}

# 排除敏感資訊
safe_config = {
    k: v for k, v in config.items()
    if k not in ["secret", "api_key"]
}

# 只保留字串值
string_values = {k: v for k, v in config.items() if isinstance(v, str)}
```

### 巢狀字典推導式

```python
# 建立巢狀字典
matrix_dict = {
    i: {j: i * j for j in range(1, 4)}
    for i in range(1, 4)
}
print(matrix_dict)
# {1: {1: 1, 2: 2, 3: 3}, 2: {1: 2, 2: 4, 3: 6}, 3: {1: 3, 2: 6, 3: 9}}

# 扁平化巢狀字典
nested = {"a": {"x": 1, "y": 2}, "b": {"x": 3, "y": 4}}
flattened = {
    f"{outer}_{inner}": value
    for outer, inner_dict in nested.items()
    for inner, value in inner_dict.items()
}
print(flattened)  # {"a_x": 1, "a_y": 2, "b_x": 3, "b_y": 4}
```

---

## 11.3 集合推導式（Set Comprehension）

```python
# 基本結構
# {表達式 for 項目 in 可迭代物件}

# 建立集合（自動去除重複）
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unique_squares = {x ** 2 for x in numbers}
print(unique_squares)  # {1, 4, 9, 16}

# 從字串取得唯一字元
text = "hello world"
unique_chars = {char for char in text if char != " "}
print(unique_chars)  # {"h", "e", "l", "o", "w", "r", "d"}

# 帶條件的集合推導式
numbers = range(-5, 6)
positive_evens = {x for x in numbers if x > 0 and x % 2 == 0}
print(positive_evens)  # {2, 4}
```

---

## 11.4 生成器表達式（Generator Expression）

生成器表達式使用小括號，產生的是生成器物件而非列表。

### 基本語法

```python
# 列表推導式：立即產生所有元素
squares_list = [x ** 2 for x in range(1000000)]

# 生成器表達式：惰性求值，一次產生一個元素
squares_gen = (x ** 2 for x in range(1000000))

print(type(squares_list))  # <class 'list'>
print(type(squares_gen))   # <class 'generator'>

# 記憶體使用差異
import sys
print(sys.getsizeof(squares_list))  # 約 8 MB
print(sys.getsizeof(squares_gen))   # 約 200 bytes（生成器物件本身很小）
```

### 使用生成器

```python
# 建立生成器
gen = (x ** 2 for x in range(5))

# 逐一取得值
print(next(gen))  # 0
print(next(gen))  # 1
print(next(gen))  # 4

# 使用 for 迴圈
for value in gen:
    print(value)  # 9, 16（從上次停止的地方繼續）

# 注意：生成器只能迭代一次
gen = (x for x in range(3))
print(list(gen))  # [0, 1, 2]
print(list(gen))  # []（已經耗盡）
```

### 生成器與內建函式

```python
# 很多內建函式接受生成器
numbers = range(1, 101)

# sum - 不需要先建立列表
total = sum(x ** 2 for x in numbers)
print(total)  # 338350

# max / min
max_square = max(x ** 2 for x in range(-10, 11))
print(max_square)  # 100

# any / all
has_even = any(x % 2 == 0 for x in [1, 3, 5, 7, 8])
print(has_even)  # True

all_positive = all(x > 0 for x in [1, 2, 3, 4, 5])
print(all_positive)  # True

# join（需要字串生成器）
result = ", ".join(str(x) for x in range(5))
print(result)  # "0, 1, 2, 3, 4"

# 注意：傳給函式時可以省略外層括號
sum(x ** 2 for x in range(10))  # 不需要 sum((x ** 2 for x in range(10)))
```

### 何時使用生成器表達式

```python
# 使用列表推導式：
# - 需要多次遍歷結果
# - 需要索引存取
# - 結果集較小

# 使用生成器表達式：
# - 只需要遍歷一次
# - 資料量很大
# - 作為函式參數

# 範例：處理大檔案
def process_large_file(filename):
    with open(filename) as f:
        # 使用生成器，不會一次載入整個檔案
        lines = (line.strip() for line in f)
        non_empty = (line for line in lines if line)
        for line in non_empty:
            process(line)
```

---

## 11.5 生成器函式（Generator Function）

使用 `yield` 關鍵字定義的函式。

### 基本語法

```python
# 生成器函式
def count_up_to(n):
    """產生 1 到 n 的數字"""
    i = 1
    while i <= n:
        yield i  # 暫停並回傳值
        i += 1

# 呼叫生成器函式會回傳生成器物件
counter = count_up_to(5)
print(type(counter))  # <class 'generator'>

# 使用 next() 取得值
print(next(counter))  # 1
print(next(counter))  # 2

# 使用 for 迴圈
for num in count_up_to(5):
    print(num)  # 1, 2, 3, 4, 5
```

### yield vs return

```python
# return：結束函式，回傳一個值
def get_squares_list(n):
    result = []
    for i in range(n):
        result.append(i ** 2)
    return result  # 一次回傳所有值

# yield：暫停函式，回傳一個值，下次呼叫從暫停處繼續
def get_squares_generator(n):
    for i in range(n):
        yield i ** 2  # 每次回傳一個值

# 使用
squares_list = get_squares_list(5)      # 立即計算所有值
squares_gen = get_squares_generator(5)  # 惰性計算
```

### 生成器的狀態

```python
def stateful_generator():
    print("開始")
    yield 1
    print("繼續")
    yield 2
    print("再繼續")
    yield 3
    print("結束")

gen = stateful_generator()

print(next(gen))
# 開始
# 1

print(next(gen))
# 繼續
# 2

print(next(gen))
# 再繼續
# 3

# print(next(gen))  # StopIteration 並印出「結束」
```

### 無限生成器

```python
def infinite_counter(start=0):
    """無限計數器"""
    n = start
    while True:
        yield n
        n += 1

# 使用（需要手動停止）
counter = infinite_counter()
for i, num in enumerate(counter):
    print(num)
    if i >= 9:
        break

# 費氏數列生成器
def fibonacci():
    """無限費氏數列"""
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# 取前 10 個費氏數
fib = fibonacci()
first_10 = [next(fib) for _ in range(10)]
print(first_10)  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### yield from（委派生成器）

```python
# 將生成任務委派給另一個生成器
def chain(*iterables):
    """串接多個可迭代物件"""
    for iterable in iterables:
        yield from iterable

# 使用
result = list(chain([1, 2], [3, 4], [5, 6]))
print(result)  # [1, 2, 3, 4, 5, 6]

# 等價於
def chain_manual(*iterables):
    for iterable in iterables:
        for item in iterable:
            yield item

# 遞迴扁平化
def flatten(nested):
    """遞迴扁平化巢狀列表"""
    for item in nested:
        if isinstance(item, list):
            yield from flatten(item)
        else:
            yield item

nested = [1, [2, 3, [4, 5]], 6, [7, [8, 9]]]
print(list(flatten(nested)))  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 生成器的方法

```python
def echo_generator():
    """可以接收值的生成器"""
    while True:
        received = yield
        print(f"收到：{received}")

gen = echo_generator()
next(gen)  # 啟動生成器（執行到第一個 yield）
gen.send("Hello")  # 收到：Hello
gen.send("World")  # 收到：World

# close() - 關閉生成器
gen.close()

# throw() - 在生成器中拋出例外
def handled_generator():
    try:
        while True:
            yield
    except GeneratorExit:
        print("生成器被關閉")
    except ValueError as e:
        print(f"捕捉到例外：{e}")

gen = handled_generator()
next(gen)
gen.throw(ValueError, "測試例外")  # 捕捉到例外：測試例外
```

---

## 11.6 實際應用範例

### 讀取大檔案

```python
def read_large_file(filename, chunk_size=1024):
    """分塊讀取大檔案"""
    with open(filename, 'rb') as f:
        while True:
            chunk = f.read(chunk_size)
            if not chunk:
                break
            yield chunk

# 使用
for chunk in read_large_file("large_file.bin"):
    process(chunk)

def read_lines(filename):
    """逐行讀取檔案"""
    with open(filename) as f:
        for line in f:
            yield line.strip()

# 處理 CSV 檔案（不使用 pandas）
def read_csv(filename):
    """簡單的 CSV 讀取器"""
    with open(filename) as f:
        headers = next(f).strip().split(",")
        for line in f:
            values = line.strip().split(",")
            yield dict(zip(headers, values))
```

### 資料管道

```python
def read_data(source):
    """讀取資料"""
    for item in source:
        yield item

def filter_valid(data):
    """過濾有效資料"""
    for item in data:
        if item.get("valid"):
            yield item

def transform(data):
    """轉換資料"""
    for item in data:
        yield {
            "id": item["id"],
            "value": item["value"] * 2
        }

def save_data(data):
    """儲存資料"""
    for item in data:
        print(f"儲存：{item}")
        yield item

# 建立管道
source = [
    {"id": 1, "value": 10, "valid": True},
    {"id": 2, "value": 20, "valid": False},
    {"id": 3, "value": 30, "valid": True},
]

pipeline = save_data(transform(filter_valid(read_data(source))))

# 執行管道
results = list(pipeline)
```

### 滑動視窗

```python
from collections import deque

def sliding_window(iterable, size):
    """產生滑動視窗"""
    iterator = iter(iterable)
    window = deque(maxlen=size)

    # 填滿第一個視窗
    for _ in range(size):
        window.append(next(iterator))
    yield tuple(window)

    # 滑動
    for item in iterator:
        window.append(item)
        yield tuple(window)

# 使用
data = [1, 2, 3, 4, 5, 6, 7]
for window in sliding_window(data, 3):
    print(window)
# (1, 2, 3)
# (2, 3, 4)
# (3, 4, 5)
# (4, 5, 6)
# (5, 6, 7)

# 計算移動平均
def moving_average(iterable, size):
    """計算移動平均"""
    for window in sliding_window(iterable, size):
        yield sum(window) / len(window)

data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
averages = list(moving_average(data, 3))
print(averages)  # [2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0]
```

### 批次處理

```python
def batch(iterable, size):
    """將可迭代物件分成批次"""
    batch = []
    for item in iterable:
        batch.append(item)
        if len(batch) == size:
            yield batch
            batch = []
    if batch:  # 最後一批（可能不滿）
        yield batch

# 使用
items = range(10)
for b in batch(items, 3):
    print(b)
# [0, 1, 2]
# [3, 4, 5]
# [6, 7, 8]
# [9]

# 批次處理 API 請求
async def batch_api_calls(items, batch_size=10):
    """批次呼叫 API"""
    for b in batch(items, batch_size):
        results = await asyncio.gather(*[
            api_call(item) for item in b
        ])
        for result in results:
            yield result
```

---

## 11.7 效能比較

```python
import sys
import time

# 記憶體比較
n = 1000000

# 列表：佔用大量記憶體
list_comp = [x ** 2 for x in range(n)]
print(f"列表：{sys.getsizeof(list_comp):,} bytes")

# 生成器：只佔用少量記憶體
gen_exp = (x ** 2 for x in range(n))
print(f"生成器：{sys.getsizeof(gen_exp):,} bytes")

# 速度比較（取決於使用方式）

# 需要所有元素時，列表可能更快（一次性分配記憶體）
start = time.time()
result = sum([x ** 2 for x in range(n)])
print(f"列表推導式：{time.time() - start:.4f} 秒")

# 不需要儲存時，生成器更好
start = time.time()
result = sum(x ** 2 for x in range(n))
print(f"生成器表達式：{time.time() - start:.4f} 秒")

# 選擇指南：
# - 小資料集 + 需要多次存取 → 列表推導式
# - 大資料集 + 只遍歷一次 → 生成器
# - 作為函式參數 → 生成器
# - 需要索引存取 → 列表推導式
```

---

## 練習題

### 練習 1：列表推導式

將以下 JavaScript 程式碼轉換為 Python 列表推導式：

```javascript
// 1. 取得偶數的平方
const squares = [1,2,3,4,5,6,7,8,9,10]
    .filter(x => x % 2 === 0)
    .map(x => x ** 2);

// 2. 扁平化二維陣列
const flat = [[1,2],[3,4],[5,6]].flat();

// 3. 取得物件陣列中特定屬性
const names = [{name: 'Alice'}, {name: 'Bob'}]
    .map(x => x.name);
```

### 練習 2：生成器

寫一個生成器函式，產生質數：

```python
def primes():
    """
    無限質數生成器

    使用範例：
    >>> gen = primes()
    >>> [next(gen) for _ in range(10)]
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    """
    # 你的程式碼
    pass
```

### 練習 3：資料處理管道

使用生成器建立資料處理管道：

```python
# 讀取 → 過濾 → 轉換 → 聚合
# 處理一個大型日誌檔案，計算每個 IP 的請求次數
```

---

## 小結

### 推導式語法對照

| 類型         | 語法                          | 結果        |
| ------------ | ----------------------------- | ----------- |
| 列表推導式   | `[x for x in iterable]`       | `list`      |
| 字典推導式   | `{k: v for k, v in iterable}` | `dict`      |
| 集合推導式   | `{x for x in iterable}`       | `set`       |
| 生成器表達式 | `(x for x in iterable)`       | `generator` |

### JavaScript 對照

| JavaScript              | Python                            |
| ----------------------- | --------------------------------- |
| `arr.map(x => f(x))`    | `[f(x) for x in arr]`             |
| `arr.filter(x => cond)` | `[x for x in arr if cond]`        |
| `arr.map().filter()`    | `[f(x) for x in arr if cond]`     |
| `arr.flat()`            | `[x for sub in arr for x in sub]` |
| `arr.reduce()`          | `functools.reduce()` 或迴圈       |

### 生成器 vs 列表

| 特性     | 列表       | 生成器   |
| -------- | ---------- | -------- |
| 記憶體   | 一次性載入 | 惰性求值 |
| 遍歷次數 | 多次       | 一次     |
| 索引存取 | 支援       | 不支援   |
| 長度     | `len()`    | 不支援   |
| 適用場景 | 小資料集   | 大資料集 |

### 最佳實踐

1. **優先使用推導式**而非 `map()`/`filter()`
2. **保持推導式簡單**，過於複雜時改用迴圈
3. **大資料集使用生成器**節省記憶體
4. **作為函式參數時使用生成器**
5. **避免巢狀超過兩層**的推導式
