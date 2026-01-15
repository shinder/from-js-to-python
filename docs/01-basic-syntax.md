# 第 1 章：基礎語法差異

本章節詳細說明 JavaScript 與 Python 在基礎語法上的主要差異，幫助 JS 開發者快速適應 Python 的寫法。

---

## 1.1 縮排與程式碼區塊

### JavaScript 的做法

JavaScript 使用大括號 `{}` 來定義程式碼區塊，縮排僅是為了可讀性，不影響程式執行：

```javascript
// JavaScript - 縮排不影響程式邏輯
if (condition) {
    console.log("條件成立");
    doSomething();
}

// 即使這樣寫也能執行（但不建議）
if (condition) {
console.log("條件成立");
doSomething();
}
```

### Python 的做法

Python 使用**縮排**來定義程式碼區塊，這是語法的一部分，不是選擇性的：

```python
# Python - 縮排決定程式區塊
if condition:
    print("條件成立")
    do_something()

# 錯誤！縮排不一致會導致 IndentationError
if condition:
    print("條件成立")
  do_something()  # IndentationError: unexpected indent
```

### 縮排規則

| 規則        | 說明                           |
| ----------- | ------------------------------ |
| 標準縮排    | **4 個空格**（PEP 8 官方建議） |
| Tab vs 空格 | 建議使用空格，避免混用         |
| 一致性      | 同一個區塊內的縮排必須完全一致 |

### 常見錯誤與解決方式

```python
# 錯誤 1：混用 Tab 和空格
def my_function():
    print("使用空格")
    print("使用 Tab")  # TabError: inconsistent use of tabs and spaces

# 錯誤 2：縮排層級錯誤
if True:
    if True:
        print("正確")
    print("這行在第一個 if 區塊內")
print("這行在 if 區塊外")

# 錯誤 3：忘記縮排
if True:
print("錯誤")  # IndentationError: expected an indented block
```

### 編輯器設定建議

大多數編輯器（VS Code、PyCharm 等）可以設定：

1. 將 Tab 自動轉換為 4 個空格
2. 顯示空白字元，方便檢查縮排
3. 設定 Python 檔案的預設縮排為 4 空格

VS Code 設定範例（`.vscode/settings.json`）：

```json
{
    "[python]": {
        "editor.tabSize": 4,
        "editor.insertSpaces": true,
        "editor.detectIndentation": false
    }
}
```

---

## 1.2 變數宣告

### JavaScript 的變數宣告

JavaScript 有三種宣告變數的方式：

```javascript
var oldWay = "舊式宣告，有提升（hoisting）問題";
let mutable = "可變變數，區塊作用域";
const immutable = "常數，不可重新賦值";

// const 的特性
const arr = [1, 2, 3];
arr.push(4);        // 可以修改內容
// arr = [5, 6];    // 錯誤！不能重新賦值
```

### Python 的變數宣告

Python 不需要任何關鍵字，直接賦值即可建立變數：

```python
# 直接賦值，無需關鍵字
name = "Alice"
age = 30
is_active = True

# Python 沒有 const 關鍵字
# 慣例使用全大寫表示「這是常數，請勿修改」
PI = 3.14159
MAX_SIZE = 100
API_KEY = "your-api-key"

# 但實際上仍可被修改（只是慣例）
PI = 3.14  # 不會報錯，但違反慣例
```

### 變數作用域差異

```javascript
// JavaScript - 區塊作用域
if (true) {
    let x = 10;
    const y = 20;
}
// console.log(x);  // ReferenceError: x is not defined
```

```python
# Python - 沒有區塊作用域，只有函式作用域
if True:
    x = 10
    y = 20

print(x)  # 10 - 可以存取！
print(y)  # 20 - 可以存取！

# 函式作用域
def my_func():
    local_var = "只在函式內有效"

# print(local_var)  # NameError: name 'local_var' is not defined
```

### 多重賦值

Python 支援多種便利的賦值方式：

```python
# 同時賦值多個變數
a, b, c = 1, 2, 3

# 交換變數值（不需要暫存變數）
a, b = b, a

# 連續賦值
x = y = z = 0

# 解構賦值
first, *rest = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4, 5]

first, *middle, last = [1, 2, 3, 4, 5]
# first = 1, middle = [2, 3, 4], last = 5
```

### 變數命名規則

| 規則     | JavaScript          | Python                    |
| -------- | ------------------- | ------------------------- |
| 一般變數 | `camelCase`         | `snake_case`              |
| 常數     | `UPPER_CASE`        | `UPPER_CASE`              |
| 類別     | `PascalCase`        | `PascalCase`              |
| 私有變數 | `_name` 或 `#name`  | `_name`                   |
| 強制私有 | `#name`（真正私有） | `__name`（name mangling） |

```python
# Python 命名範例
user_name = "alice"           # 一般變數：snake_case
MAX_CONNECTIONS = 100         # 常數：UPPER_CASE
class UserAccount:            # 類別：PascalCase
    _internal_id = None       # 慣例私有
    __secret_key = None       # Name mangling 私有
```

---

## 1.3 註解

### JavaScript 的註解

```javascript
// 單行註解

/*
 * 多行註解
 * 可以跨越多行
 */

/**
 * JSDoc 文件註解
 * @param {string} name - 使用者名稱
 * @returns {string} 問候語
 */
function greet(name) {
    return `Hello, ${name}`;
}
```

### Python 的註解

```python
# 單行註解使用井字號

# 多行註解方式一：連續的單行註解
# 這是第一行
# 這是第二行
# 這是第三行

"""
多行註解方式二：使用三引號字串
雖然這實際上是字串，但如果沒有賦值給變數
Python 會自動忽略它
"""

'''
單引號三引號也可以
用於多行註解
'''
```

### Docstring（文件字串）

Docstring 是 Python 特有的文件機制，放在函式、類別或模組的開頭：

```python
def calculate_area(radius: float) -> float:
    """計算圓的面積。

    這是一個計算圓面積的函式，接受半徑作為參數，
    回傳計算後的面積值。

    Args:
        radius: 圓的半徑，必須為正數

    Returns:
        圓的面積（float 型別）

    Raises:
        ValueError: 當半徑為負數時拋出

    Examples:
        >>> calculate_area(1)
        3.14159
        >>> calculate_area(2)
        12.56636
    """
    if radius < 0:
        raise ValueError("半徑不能為負數")
    return 3.14159 * radius ** 2


# 可以透過 __doc__ 屬性存取 docstring
print(calculate_area.__doc__)

# 或使用 help() 函式
help(calculate_area)
```

### Docstring 風格

Python 有幾種常見的 Docstring 風格：

#### Google Style（推薦）

```python
def fetch_data(url: str, timeout: int = 30) -> dict:
    """從指定 URL 取得資料。

    Args:
        url: 目標 URL 位址
        timeout: 請求超時秒數，預設為 30

    Returns:
        包含回應資料的字典

    Raises:
        ConnectionError: 無法連線到伺服器
        TimeoutError: 請求超時
    """
    pass
```

#### NumPy Style

```python
def fetch_data(url: str, timeout: int = 30) -> dict:
    """
    從指定 URL 取得資料。

    Parameters
    ----------
    url : str
        目標 URL 位址
    timeout : int, optional
        請求超時秒數，預設為 30

    Returns
    -------
    dict
        包含回應資料的字典

    Raises
    ------
    ConnectionError
        無法連線到伺服器
    TimeoutError
        請求超時
    """
    pass
```

### 類別與模組的 Docstring

```python
"""
這是模組層級的 docstring。

這個模組提供使用者管理相關的功能，
包含使用者的建立、查詢、更新和刪除操作。

Typical usage example:

    from user_manager import UserManager

    manager = UserManager()
    user = manager.create_user("alice@example.com")
"""


class UserManager:
    """管理使用者的類別。

    這個類別負責所有使用者相關的操作，
    包含 CRUD 功能以及權限管理。

    Attributes:
        db_connection: 資料庫連線物件
        cache: 快取物件，用於提升查詢效能
    """

    def __init__(self, db_url: str):
        """初始化 UserManager。

        Args:
            db_url: 資料庫連線字串
        """
        self.db_connection = self._connect(db_url)
        self.cache = {}
```

---

## 1.4 字串

### 基本字串宣告

```javascript
// JavaScript
const single = 'single quotes';
const double = "double quotes";
const template = `template literal with ${variable}`;
const multiline = `
    多行字串
    使用模板字串
`;
```

```python
# Python - 單引號和雙引號完全相同
single = 'single quotes'
double = "double quotes"

# f-string（格式化字串，Python 3.6+）
name = "Alice"
age = 30
greeting = f"Hello, {name}! You are {age} years old."

# 多行字串使用三引號
multiline = """
    這是多行字串
    可以包含換行
    不需要特殊符號
"""

# 或使用三單引號
another = '''
    這樣也可以
    建立多行字串
'''
```

### 字串格式化方式比較

Python 有多種字串格式化方式，以下從新到舊列出：

```python
name = "Alice"
age = 30
score = 95.5

# 方式 1：f-string（推薦，Python 3.6+）
result = f"Name: {name}, Age: {age}, Score: {score:.1f}"

# 方式 2：str.format()
result = "Name: {}, Age: {}, Score: {:.1f}".format(name, age, score)
result = "Name: {n}, Age: {a}".format(n=name, a=age)

# 方式 3：% 格式化（舊式，不推薦）
result = "Name: %s, Age: %d, Score: %.1f" % (name, age, score)

# 方式 4：字串串接（效能較差）
result = "Name: " + name + ", Age: " + str(age)
```

### f-string 進階用法

```python
# 運算式
x, y = 10, 20
print(f"Sum: {x + y}")           # Sum: 30
print(f"Max: {max(x, y)}")       # Max: 20

# 格式化數字
pi = 3.14159265359
print(f"Pi: {pi:.2f}")           # Pi: 3.14
print(f"Pi: {pi:10.2f}")         # Pi:       3.14（寬度 10）

# 千分位分隔
big_number = 1234567890
print(f"Number: {big_number:,}") # Number: 1,234,567,890

# 百分比
ratio = 0.756
print(f"Ratio: {ratio:.1%}")     # Ratio: 75.6%

# 進位制轉換
num = 255
print(f"Hex: {num:x}")           # Hex: ff
print(f"Hex: {num:#x}")          # Hex: 0xff
print(f"Binary: {num:b}")        # Binary: 11111111
print(f"Octal: {num:o}")         # Octal: 377

# 對齊
text = "hello"
print(f"|{text:>10}|")           # |     hello| 右對齊
print(f"|{text:<10}|")           # |hello     | 左對齊
print(f"|{text:^10}|")           # |  hello   | 置中
print(f"|{text:*^10}|")          # |**hello***| 置中並填充

# 除錯模式（Python 3.8+）
name = "Alice"
age = 30
print(f"{name=}, {age=}")        # name='Alice', age=30
```

### 字串常用方法對照

| 操作     | JavaScript              | Python                  |
| -------- | ----------------------- | ----------------------- |
| 長度     | `str.length`            | `len(str)`              |
| 轉大寫   | `str.toUpperCase()`     | `str.upper()`           |
| 轉小寫   | `str.toLowerCase()`     | `str.lower()`           |
| 去除空白 | `str.trim()`            | `str.strip()`           |
| 分割     | `str.split(',')`        | `str.split(',')`        |
| 合併     | `arr.join(',')`         | `','.join(arr)`         |
| 取代     | `str.replace('a', 'b')` | `str.replace('a', 'b')` |
| 包含     | `str.includes('sub')`   | `'sub' in str`          |
| 開頭     | `str.startsWith('x')`   | `str.startswith('x')`   |
| 結尾     | `str.endsWith('x')`     | `str.endswith('x')`     |
| 尋找     | `str.indexOf('x')`      | `str.find('x')`         |
| 切片     | `str.slice(1, 4)`       | `str[1:4]`              |

### 字串方法範例

```python
text = "  Hello, World!  "

# 基本操作
print(len(text))              # 17
print(text.upper())           # "  HELLO, WORLD!  "
print(text.lower())           # "  hello, world!  "
print(text.strip())           # "Hello, World!"
print(text.lstrip())          # "Hello, World!  "（只去左邊）
print(text.rstrip())          # "  Hello, World!"（只去右邊）

# 分割與合併
words = "apple,banana,cherry".split(",")
print(words)                  # ['apple', 'banana', 'cherry']

# 注意：Python 的 join 是字串方法，不是列表方法
result = "-".join(words)
print(result)                 # "apple-banana-cherry"

# 取代
text = "Hello, World!"
print(text.replace("World", "Python"))  # "Hello, Python!"
print(text.replace("l", "L", 1))        # "HeLlo, World!"（只取代第一個）

# 檢查
print("World" in text)        # True
print(text.startswith("Hello"))  # True
print(text.endswith("!"))     # True

# 尋找
print(text.find("World"))     # 7（回傳索引）
print(text.find("xyz"))       # -1（找不到回傳 -1）
print(text.index("World"))    # 7
# print(text.index("xyz"))    # ValueError（找不到會拋出錯誤）

# 計數
print("hello".count("l"))     # 2
```

### 字串切片（Slicing）

Python 的切片功能非常強大：

```python
text = "Hello, World!"

# 基本切片 [start:end]（不包含 end）
print(text[0:5])      # "Hello"
print(text[7:12])     # "World"

# 省略 start 或 end
print(text[:5])       # "Hello"（從開頭到索引 5）
print(text[7:])       # "World!"（從索引 7 到結尾）
print(text[:])        # "Hello, World!"（完整複製）

# 負數索引（從後面數）
print(text[-1])       # "!"（最後一個字元）
print(text[-6:])      # "World!"（最後 6 個字元）
print(text[:-1])      # "Hello, World"（除了最後一個）

# 步進值 [start:end:step]
print(text[::2])      # "Hlo ol!"（每隔一個字元）
print(text[::-1])     # "!dlroW ,olleH"（反轉字串）

# 組合使用
print(text[1:10:2])   # "el,Wr"
```

### 原始字串（Raw String）

```python
# 一般字串中的跳脫字元會被解析
path = "C:\new\folder"
print(path)           # C:
                      # ew
                      # older

# 使用原始字串（r 或 R 前綴）
path = r"C:\new\folder"
print(path)           # C:\new\folder

# 常用於正規表達式
import re
pattern = r"\d+\.\d+"  # 不用寫成 "\\d+\\.\\d+"
```

### 字串編碼

```python
# 字串與位元組轉換
text = "你好，世界"

# 編碼：str -> bytes
encoded = text.encode("utf-8")
print(encoded)        # b'\xe4\xbd\xa0\xe5\xa5\xbd...'
print(type(encoded))  # <class 'bytes'>

# 解碼：bytes -> str
decoded = encoded.decode("utf-8")
print(decoded)        # 你好，世界

# 指定其他編碼
big5_encoded = text.encode("big5")
```

---

## 練習題

### 練習 1：縮排修正

修正以下程式碼的縮排錯誤：

```python
def calculate_grade(score):
if score >= 90:
return "A"
elif score >= 80:
return "B"
elif score >= 70:
return "C"
else:
return "F"
```

### 練習 2：變數與字串格式化

將以下 JavaScript 程式碼轉換為 Python：

```javascript
const firstName = "John";
const lastName = "Doe";
const age = 25;
const message = `Hello, ${firstName} ${lastName}! You are ${age} years old.`;
console.log(message);
```

### 練習 3：字串操作

用 Python 完成以下操作：

1. 將 `"hello world"` 轉為 `"Hello World"`（每個單字首字母大寫）
2. 將 `"  trim me  "` 去除前後空白
3. 將 `"apple,banana,cherry"` 分割成列表，再用 ` - ` 重新組合
4. 反轉字串 `"Python"`

---

## 小結

| 主題       | JavaScript          | Python               |
| ---------- | ------------------- | -------------------- |
| 程式碼區塊 | 大括號 `{}`         | 縮排（4 空格）       |
| 變數宣告   | `let`/`const`/`var` | 直接賦值             |
| 常數       | `const`             | 慣例用大寫（無強制） |
| 單行註解   | `//`                | `#`                  |
| 多行註解   | `/* */`             | `"""` 或連續 `#`     |
| 文件註解   | JSDoc               | Docstring            |
| 字串模板   | `` `${var}` ``      | `f"{var}"`           |
| 多行字串   | `` ` ` ``           | `"""` 或 `'''`       |
| 字串方法   | `str.method()`      | `str.method()`       |
| 字串長度   | `str.length`        | `len(str)`           |
| 陣列合併   | `arr.join(',')`     | `','.join(arr)`      |

掌握這些基礎差異後，您就能更自然地閱讀和撰寫 Python 程式碼了！
