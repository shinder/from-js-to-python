# 第 15 章：重要慣例與風格

## 概述

Python 社群非常重視程式碼風格的一致性，有句名言：「程式碼被閱讀的次數遠比被編寫的次數多」。PEP 8 是 Python 官方的風格指南，幾乎所有 Python 專案都遵循它。對於從 JavaScript 轉過來的開發者，最大的改變是命名風格從 camelCase 轉為 snake_case。

### JavaScript vs Python 風格概覽

| 面向          | JavaScript               | Python         |
| ------------- | ------------------------ | -------------- |
| 風格指南      | Airbnb, Standard, Google | PEP 8          |
| 變數/函式命名 | camelCase                | snake_case     |
| 類別命名      | PascalCase               | PascalCase     |
| 常數命名      | UPPER_CASE               | UPPER_CASE     |
| 縮排          | 2 或 4 空格              | 4 空格（強制） |
| 行長度        | 80-120                   | 79-88          |
| 分號          | 可選                     | 無             |
| 引號          | 單引號或雙引號           | 單引號或雙引號 |

---

## 15.1 命名慣例

### 變數與函式：snake_case

Python 使用 snake_case（蛇形命名法）作為變數和函式的命名規範。

```python
# Python - snake_case
user_name = "Alice"
total_count = 100
is_active = True

def get_user_by_id(user_id):
    pass

def calculate_total_price(items, tax_rate):
    pass

def send_welcome_email(user_email):
    pass
```

對比 JavaScript：

```javascript
// JavaScript - camelCase
let userName = "Alice";
let totalCount = 100;
let isActive = true;

function getUserById(userId) {}
function calculateTotalPrice(items, taxRate) {}
function sendWelcomeEmail(userEmail) {}
```

### 類別：PascalCase

類別命名在 Python 和 JavaScript 中都使用 PascalCase。

```python
# Python - PascalCase
class UserAccount:
    pass

class HttpRequestHandler:
    pass

class DatabaseConnection:
    pass

class APIResponseParser:  # 縮寫保持大寫
    pass
```

對比 JavaScript：

```javascript
// JavaScript - PascalCase（相同）
class UserAccount {}
class HttpRequestHandler {}
class DatabaseConnection {}
class APIResponseParser {}
```

### 常數：UPPER_SNAKE_CASE

模組層級的常數使用全大寫加底線。

```python
# Python - 常數
MAX_CONNECTIONS = 100
DEFAULT_TIMEOUT = 30
API_BASE_URL = "https://api.example.com"
DATABASE_URL = "postgresql://localhost/mydb"

# 設定檔常用模式
DEBUG = False
SECRET_KEY = "your-secret-key"
ALLOWED_HOSTS = ["localhost", "example.com"]
```

對比 JavaScript：

```javascript
// JavaScript - 常數（相同風格）
const MAX_CONNECTIONS = 100;
const DEFAULT_TIMEOUT = 30;
const API_BASE_URL = "https://api.example.com";
```

### 私有成員：底線前綴

Python 沒有真正的 private 關鍵字，而是使用命名慣例表示存取層級。

```python
class User:
    def __init__(self, name, password):
        self.name = name           # 公開屬性
        self._email = None         # 受保護（慣例）
        self.__password = password # 名稱修飾（較私有）

    def _validate_email(self):
        """受保護方法 - 內部使用，但子類別可覆寫"""
        pass

    def __hash_password(self):
        """私有方法 - 名稱會被修飾為 _User__hash_password"""
        pass

    @property
    def password(self):
        """不直接暴露密碼"""
        return "********"
```

**底線命名規則：**

| 命名       | 意義                  | 存取性                      |
| ---------- | --------------------- | --------------------------- |
| `name`     | 公開                  | 完全公開                    |
| `_name`    | 受保護（protected）   | 可存取，但表示「內部使用」  |
| `__name`   | 私有（name mangling） | 被修飾為 `_ClassName__name` |
| `__name__` | 魔術方法/屬性         | 系統保留，不要自行定義      |
| `name_`    | 避免關鍵字衝突        | `class_`, `type_`, `id_`    |

對比 JavaScript：

```javascript
// JavaScript - 私有成員
class User {
    name;           // 公開
    _email;         // 受保護（慣例）
    #password;      // 真正的私有（ES2022+）

    constructor(name, password) {
        this.name = name;
        this.#password = password;
    }
}
```

### 模組與套件命名

```python
# 模組名稱：snake_case，簡短
# user_service.py
# database_helper.py
# api_utils.py

# 套件名稱：全小寫，無底線（如果可能）
# mypackage/
# utils/
# httphandler/  # 而非 http_handler/
```

### 特殊命名慣例

```python
# 單底線 _ ：表示「不關心」的變數
for _ in range(10):
    print("Hello")

# 解構時忽略某些值
first, _, third = (1, 2, 3)
name, *_ = ["Alice", "extra", "data"]

# 避免關鍵字衝突
class_ = "Math 101"  # 避免與 class 關鍵字衝突
type_ = "primary"    # 避免與 type 函式衝突
id_ = 123            # 避免與 id 函式衝突

# 國際化慣例
from gettext import gettext as _
message = _("Hello, World!")  # 可翻譯字串
```

---

## 15.2 PEP 8 風格指南

PEP 8 是 Python 官方的程式碼風格指南，以下是最重要的規則。

### 縮排

```python
# 正確：4 個空格
def function():
    if condition:
        do_something()
        for item in items:
            process(item)

# 錯誤：使用 Tab 或非 4 空格縮排
def function():
  if condition:  # 2 空格 - 錯誤！
      do_something()

# 續行對齊方式 1：與開啟括號對齊
result = some_function(arg1, arg2,
                       arg3, arg4)

# 續行對齊方式 2：懸掛縮排（推薦）
result = some_function(
    arg1, arg2,
    arg3, arg4,
)

# 條件式續行
if (condition1
        and condition2
        and condition3):
    do_something()

# 或使用括號讓運算子在行首
if (
    condition1
    and condition2
    and condition3
):
    do_something()
```

### 行長度

```python
# PEP 8 建議最大 79 字元（程式碼）和 72 字元（文件字串/註解）
# 許多現代專案使用 88（Black 預設）或 120 字元

# 長字串換行
message = (
    "This is a very long message that needs to be "
    "split across multiple lines for readability."
)

# 長串列
items = [
    "item1",
    "item2",
    "item3",
    "item4",
]

# 長字典
config = {
    "database_host": "localhost",
    "database_port": 5432,
    "database_name": "myapp",
    "debug_mode": True,
}

# 長函式呼叫
result = some_very_long_function_name(
    argument_one,
    argument_two,
    argument_three,
    keyword_arg=value,
)

# 長 import
from mypackage.subpackage.module import (
    ClassOne,
    ClassTwo,
    function_one,
    function_two,
)
```

### 空行

```python
# 頂層定義（函式、類別）之間空兩行
import os


def function_one():
    pass


def function_two():
    pass


class MyClass:
    """類別內部方法之間空一行"""

    def method_one(self):
        pass

    def method_two(self):
        pass

    def method_three(self):
        # 方法內部可用空行分隔邏輯區塊
        setup_data()
        process_data()

        cleanup()
        return result
```

### 空格

```python
# 正確的空格使用
x = 1
y = 2
result = x + y

# 函式呼叫：括號內無空格
function(arg1, arg2)  # 正確
function( arg1, arg2 )  # 錯誤

# 索引和切片：括號內無空格
items[0]  # 正確
items[ 0 ]  # 錯誤
items[1:3]  # 正確
items[1 : 3]  # 錯誤

# 逗號後有空格，前無空格
items = [1, 2, 3]  # 正確
items = [1 , 2 , 3]  # 錯誤

# 冒號後有空格（字典除外）
{key: value}  # 正確
{key : value}  # 錯誤

# 運算子前後有空格
x = y + z  # 正確
x=y+z  # 錯誤

# 例外：指定預設參數和關鍵字參數時無空格
def function(arg1, arg2=default):  # 正確
def function(arg1, arg2 = default):  # 錯誤

function(arg1, keyword=value)  # 正確
function(arg1, keyword = value)  # 錯誤

# 型別提示的冒號後有空格
def function(name: str) -> str:  # 正確

# 對齊賦值（不建議）
x      = 1  # 不建議
y      = 2
long_var = 3

# 應該這樣寫
x = 1
y = 2
long_var = 3
```

### import 順序

```python
# 標準順序：
# 1. 標準函式庫
# 2. 第三方套件
# 3. 本地模組
# 每組之間空一行

# 標準函式庫
import os
import sys
from datetime import datetime
from typing import Optional, List

# 第三方套件
import requests
from fastapi import FastAPI
from pydantic import BaseModel

# 本地模組
from myproject.config import settings
from myproject.models import User
from myproject.utils import helper

# import 風格
import os  # 整個模組
from os import path  # 特定項目
from os import path, environ  # 多個項目（同一行）
from os import (  # 多個項目（多行）
    path,
    environ,
    getcwd,
)

# 避免使用
from os import *  # 不建議：污染命名空間

# 別名（當名稱太長或有衝突時）
import numpy as np
import pandas as pd
from matplotlib import pyplot as plt
```

### 字串引號

```python
# Python 中單引號和雙引號無差異
# 選一種風格並保持一致（許多專案選擇雙引號）

name = "Alice"  # 雙引號
name = 'Alice'  # 單引號 - 也可以

# 當字串包含引號時，使用另一種引號避免跳脫
message = "She said 'Hello'"
message = 'She said "Hello"'

# 多行字串使用三引號
description = """
This is a multi-line
description that spans
multiple lines.
"""

# f-string 格式化
greeting = f"Hello, {name}!"

# 原始字串（用於正規表達式等）
pattern = r"\d+\.\d+"
```

### 註解風格

```python
# 單行註解：# 後空一格
x = 1  # 行內註解：至少兩個空格後

# 區塊註解
# 這是一段區塊註解
# 說明接下來的程式碼邏輯
# 每行都以 # 開頭
def complex_function():
    pass

# TODO 註解
# TODO: 加入錯誤處理
# FIXME: 這裡有已知問題
# NOTE: 注意這個邊界條件
# HACK: 暫時的解決方案
```

### 比較與布林值

```python
# 與 None 比較使用 is
if x is None:  # 正確
    pass
if x == None:  # 不建議
    pass

# 布林值比較不需要 == True/False
if is_valid:  # 正確
    pass
if is_valid == True:  # 不必要
    pass

if not is_valid:  # 正確
    pass
if is_valid == False:  # 不必要
    pass

# 空容器檢查：直接使用真值判斷
if items:  # 正確：非空
    pass
if len(items) > 0:  # 不必要
    pass

if not items:  # 正確：空
    pass
if len(items) == 0:  # 不必要
    pass

# 型別檢查使用 isinstance
if isinstance(x, int):  # 正確
    pass
if type(x) == int:  # 不建議（不處理繼承）
    pass
```

---

## 15.3 文件字串（Docstring）

Python 使用 docstring 來記錄模組、類別、函式的用途和用法。

### 基本格式

```python
def simple_function(x):
    """簡短的單行描述。"""
    return x * 2

def complex_function(x, y):
    """簡短的單行描述。

    更詳細的多行描述，說明函式的行為、
    演算法、注意事項等。

    Args:
        x: 第一個參數的說明
        y: 第二個參數的說明

    Returns:
        回傳值的說明

    Raises:
        ValueError: 當 x 為負數時
    """
    if x < 0:
        raise ValueError("x must be non-negative")
    return x + y
```

### Google 風格（推薦）

Google 風格是最常用的 docstring 格式之一。

```python
def fetch_user(user_id: int, include_deleted: bool = False) -> Optional[User]:
    """根據 ID 取得使用者資料。

    從資料庫中查詢指定 ID 的使用者，支援查詢已刪除的使用者。

    Args:
        user_id: 使用者的唯一識別碼。
        include_deleted: 是否包含已刪除的使用者。
            預設為 False。

    Returns:
        找到的 User 物件，若無則回傳 None。

        範例回傳值::

            User(id=1, name="Alice", email="alice@example.com")

    Raises:
        DatabaseError: 資料庫連線失敗時。
        ValueError: 當 user_id 為負數時。

    Examples:
        >>> user = fetch_user(1)
        >>> print(user.name)
        Alice

        >>> user = fetch_user(999)
        >>> print(user)
        None
    """
    pass


class UserService:
    """使用者服務類別。

    提供使用者相關的 CRUD 操作和業務邏輯。

    Attributes:
        db: 資料庫連線實例。
        cache: 快取服務實例。

    Examples:
        >>> service = UserService(db, cache)
        >>> user = service.get_user(1)
    """

    def __init__(self, db: Database, cache: Cache):
        """初始化 UserService。

        Args:
            db: 資料庫連線實例。
            cache: 快取服務實例。
        """
        self.db = db
        self.cache = cache
```

### NumPy 風格

NumPy 風格在科學計算社群中很流行。

```python
def calculate_statistics(data, weights=None):
    """
    計算資料的統計值。

    Parameters
    ----------
    data : array_like
        輸入資料陣列。
    weights : array_like, optional
        權重陣列，長度必須與 data 相同。

    Returns
    -------
    dict
        包含以下鍵值的字典：
        - mean : float
            加權平均值
        - std : float
            標準差
        - median : float
            中位數

    Raises
    ------
    ValueError
        當 weights 長度與 data 不符時。

    See Also
    --------
    numpy.mean : 計算平均值
    numpy.std : 計算標準差

    Notes
    -----
    使用 Welford 演算法計算標準差以提高數值穩定性。

    Examples
    --------
    >>> data = [1, 2, 3, 4, 5]
    >>> stats = calculate_statistics(data)
    >>> print(stats['mean'])
    3.0
    """
    pass
```

### Sphinx 風格

Sphinx 風格適合需要自動生成文件的專案。

```python
def process_data(input_path, output_path, format="json"):
    """
    處理資料檔案並輸出結果。

    :param input_path: 輸入檔案路徑
    :type input_path: str
    :param output_path: 輸出檔案路徑
    :type output_path: str
    :param format: 輸出格式，預設為 "json"
    :type format: str, optional
    :returns: 處理的資料筆數
    :rtype: int
    :raises FileNotFoundError: 當輸入檔案不存在時
    :raises ValueError: 當格式不支援時

    .. note::
        此函式會覆蓋既有的輸出檔案。

    .. warning::
        大型檔案可能需要較長處理時間。

    Example::

        >>> count = process_data("input.csv", "output.json")
        >>> print(f"Processed {count} records")
    """
    pass
```

### 模組 Docstring

```python
"""使用者管理模組。

此模組提供使用者相關的資料模型和服務。

Modules:
    models: 使用者資料模型
    services: 使用者業務邏輯
    utils: 工具函式

Example:
    from user_management import UserService

    service = UserService()
    user = service.create_user("Alice", "alice@example.com")

Attributes:
    VERSION (str): 模組版本號
    DEFAULT_TIMEOUT (int): 預設超時秒數

Todo:
    * 加入批次匯入功能
    * 支援更多認證方式
"""

VERSION = "1.0.0"
DEFAULT_TIMEOUT = 30
```

### 類別 Docstring

```python
class DatabaseConnection:
    """資料庫連線管理器。

    管理資料庫連線的生命週期，支援連線池和自動重連。

    Attributes:
        host (str): 資料庫主機位址。
        port (int): 資料庫連接埠。
        is_connected (bool): 目前連線狀態。

    Examples:
        使用 context manager：

        >>> with DatabaseConnection("localhost", 5432) as conn:
        ...     result = conn.execute("SELECT * FROM users")

        手動管理：

        >>> conn = DatabaseConnection("localhost", 5432)
        >>> conn.connect()
        >>> result = conn.execute("SELECT * FROM users")
        >>> conn.disconnect()

    Note:
        建議使用 context manager 確保連線正確關閉。
    """

    def __init__(self, host: str, port: int = 5432):
        """初始化資料庫連線。

        Args:
            host: 資料庫主機位址。
            port: 資料庫連接埠，預設為 5432。
        """
        self.host = host
        self.port = port
        self.is_connected = False
```

---

## 15.4 型別提示風格

### 基本型別提示

```python
from typing import Optional, Union, List, Dict

# 變數型別提示
name: str = "Alice"
age: int = 30
scores: list[int] = [90, 85, 88]

# 函式型別提示
def greet(name: str) -> str:
    return f"Hello, {name}"

def find_user(user_id: int) -> Optional[User]:
    """回傳 User 或 None"""
    pass

def process(data: Union[str, bytes]) -> str:
    """接受 str 或 bytes"""
    pass
```

### 複雜型別

```python
from typing import (
    Callable,
    TypeVar,
    Generic,
    Protocol,
)

# 函式型別
Handler = Callable[[str, int], bool]

def register_handler(handler: Handler) -> None:
    pass

# 泛型
T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]

# Protocol（結構化型別）
class Drawable(Protocol):
    def draw(self) -> None: ...

def render(obj: Drawable) -> None:
    obj.draw()
```

---

## 15.5 專案慣例

### __init__.py 的使用

```python
# mypackage/__init__.py

"""MyPackage - 我的套件。

提供各種實用功能。
"""

# 版本資訊
__version__ = "1.0.0"
__author__ = "Your Name"

# 匯出公開 API
from mypackage.core import MainClass
from mypackage.utils import helper_function

# 定義公開介面
__all__ = [
    "MainClass",
    "helper_function",
    "__version__",
]

# 套件初始化邏輯（如果需要）
def _initialize():
    """套件初始化"""
    pass

_initialize()
```

### __main__.py 的使用

```python
# mypackage/__main__.py
"""允許以 python -m mypackage 執行套件。"""

from mypackage.cli import main

if __name__ == "__main__":
    main()
```

### if __name__ == "__main__"

```python
# script.py
"""示範 __name__ == "__main__" 的用法。"""

def main():
    """程式進入點。"""
    print("Running as main program")

def helper():
    """工具函式。"""
    pass

# 只有直接執行此檔案時才會執行
# 被 import 時不會執行
if __name__ == "__main__":
    main()
```

對比 JavaScript：

```javascript
// JavaScript 沒有直接對應
// 可以用檢查 require.main 的方式

// CommonJS
if (require.main === module) {
    main();
}

// ESM
import { fileURLToPath } from 'url';
if (process.argv[1] === fileURLToPath(import.meta.url)) {
    main();
}
```

### 設定檔模式

```python
# config/settings.py
"""應用程式設定。"""

import os
from pathlib import Path

# 基本路徑
BASE_DIR = Path(__file__).resolve().parent.parent

# 從環境變數讀取
DEBUG = os.getenv("DEBUG", "False").lower() == "true"
SECRET_KEY = os.getenv("SECRET_KEY", "default-secret-key")

# 資料庫設定
DATABASE = {
    "host": os.getenv("DB_HOST", "localhost"),
    "port": int(os.getenv("DB_PORT", "5432")),
    "name": os.getenv("DB_NAME", "myapp"),
    "user": os.getenv("DB_USER", "postgres"),
    "password": os.getenv("DB_PASSWORD", ""),
}

# 功能開關
FEATURES = {
    "new_dashboard": os.getenv("FEATURE_NEW_DASHBOARD", "False").lower() == "true",
    "dark_mode": True,
}
```

### 例外類別定義

```python
# exceptions.py
"""自訂例外類別。"""


class MyAppError(Exception):
    """應用程式基底例外。"""
    pass


class ValidationError(MyAppError):
    """驗證錯誤。

    Attributes:
        field: 發生錯誤的欄位名稱
        message: 錯誤訊息
    """

    def __init__(self, field: str, message: str):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")


class NotFoundError(MyAppError):
    """資源未找到錯誤。"""

    def __init__(self, resource: str, identifier: any):
        self.resource = resource
        self.identifier = identifier
        super().__init__(f"{resource} with id {identifier} not found")


class AuthenticationError(MyAppError):
    """認證錯誤。"""
    pass


class AuthorizationError(MyAppError):
    """授權錯誤。"""
    pass
```

---

## 15.6 常見錯誤與修正

### 命名錯誤

```python
# 錯誤：使用 camelCase
def getUserById(userId):  # 錯誤
    pass

# 正確：使用 snake_case
def get_user_by_id(user_id):  # 正確
    pass

# 錯誤：類別使用 snake_case
class user_service:  # 錯誤
    pass

# 正確：類別使用 PascalCase
class UserService:  # 正確
    pass
```

### 縮排錯誤

```python
# 錯誤：混用 Tab 和空格
def function():
	if True:  # Tab
        pass   # 空格

# 正確：統一使用 4 空格
def function():
    if True:
        pass
```

### import 錯誤

```python
# 錯誤：import 順序混亂
from myproject import utils
import os
import requests
from typing import List

# 正確：按順序分組
import os
from typing import List

import requests

from myproject import utils
```

### 空格錯誤

```python
# 錯誤
x=1
y = 2+3
function( arg1,arg2 )
items[ 0 ]

# 正確
x = 1
y = 2 + 3
function(arg1, arg2)
items[0]
```

---

## 15.7 工具設定範例

### 完整的 pyproject.toml

```toml
[project]
name = "my-project"
version = "1.0.0"
description = "My awesome project"
readme = "README.md"
requires-python = ">=3.11"
license = {text = "MIT"}
authors = [
    {name = "Your Name", email = "you@example.com"}
]
dependencies = [
    "fastapi>=0.100.0",
    "pydantic>=2.0",
    "requests>=2.28.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "pytest-cov>=4.0",
    "black>=23.0",
    "ruff>=0.1.0",
    "mypy>=1.0",
]

[project.scripts]
myapp = "my_project.cli:main"

[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

# Black 設定
[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'

# Ruff 設定
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # Pyflakes
    "I",      # isort
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "UP",     # pyupgrade
]

[tool.ruff.lint.isort]
known-first-party = ["my_project"]

# mypy 設定
[tool.mypy]
python_version = "3.11"
strict = true
warn_return_any = true
warn_unused_ignores = true

[[tool.mypy.overrides]]
module = ["requests.*"]
ignore_missing_imports = true

# pytest 設定
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-v --cov=src --cov-report=term-missing"
```

### .editorconfig

```ini
# .editorconfig
root = true

[*]
indent_style = space
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.py]
indent_size = 4

[*.{js,ts,json,yaml,yml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

### pre-commit 設定

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-added-large-files

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.6
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.7.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests]
```

安裝和使用：

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

---

## 練習題

### 練習 1：修正命名風格

將以下 JavaScript 風格的程式碼轉換為 Python 風格：

```python
# 修正前
def getUserById(userId):
    userName = "Alice"
    isActive = True
    return {"userName": userName, "isActive": isActive}

class userService:
    def __init__(self):
        self.maxRetries = 3

    def fetchUserData(self, userId):
        pass
```

**參考答案：**

```python
# 修正後
def get_user_by_id(user_id):
    user_name = "Alice"
    is_active = True
    return {"user_name": user_name, "is_active": is_active}

class UserService:
    def __init__(self):
        self.max_retries = 3

    def fetch_user_data(self, user_id):
        pass
```

### 練習 2：撰寫 Docstring

為以下函式撰寫完整的 Google 風格 docstring：

```python
def calculate_discount(price, discount_percent, max_discount=None):
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")

    discount = price * (discount_percent / 100)

    if max_discount is not None and discount > max_discount:
        discount = max_discount

    return price - discount
```

**參考答案：**

```python
def calculate_discount(
    price: float,
    discount_percent: float,
    max_discount: float | None = None,
) -> float:
    """計算折扣後的價格。

    根據折扣百分比計算最終價格，可設定最大折扣金額上限。

    Args:
        price: 原始價格，必須為正數。
        discount_percent: 折扣百分比（0-100）。
        max_discount: 最大折扣金額上限。若為 None 則無上限。

    Returns:
        折扣後的價格。

    Raises:
        ValueError: 當 discount_percent 不在 0-100 範圍內。

    Examples:
        >>> calculate_discount(100, 20)
        80.0

        >>> calculate_discount(100, 50, max_discount=30)
        70.0
    """
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")

    discount = price * (discount_percent / 100)

    if max_discount is not None and discount > max_discount:
        discount = max_discount

    return price - discount
```

### 練習 3：修正 PEP 8 違規

修正以下程式碼中的 PEP 8 違規：

```python
import requests
from myproject import utils
import os,sys
from typing import List,Dict

x=1
y=2
def myFunction( arg1,arg2 ):
    if x==None:
        return arg1+arg2
    items=[ 1,2,3 ]
    result=items[ 0 ]+items[ 1 ]
    return result
```

**參考答案：**

```python
import os
import sys
from typing import Dict, List

import requests

from myproject import utils

x = 1
y = 2


def my_function(arg1, arg2):
    if x is None:
        return arg1 + arg2
    items = [1, 2, 3]
    result = items[0] + items[1]
    return result
```

---

## 總結

### JavaScript vs Python 風格對照表

| 項目     | JavaScript         | Python               |
| -------- | ------------------ | -------------------- |
| 變數命名 | `camelCase`        | `snake_case`         |
| 函式命名 | `camelCase`        | `snake_case`         |
| 類別命名 | `PascalCase`       | `PascalCase`         |
| 常數命名 | `UPPER_CASE`       | `UPPER_CASE`         |
| 私有成員 | `#name` 或 `_name` | `_name` 或 `__name`  |
| 縮排     | 2 或 4 空格        | 4 空格（強制）       |
| 分號     | 可選               | 無                   |
| 文件註解 | JSDoc (`/** */`)   | Docstring (`"""`)    |
| 風格檢查 | ESLint             | Ruff, flake8, pylint |
| 格式化   | Prettier           | Black, Ruff          |

### 重點回顧

1. **snake_case 是王道**：Python 使用 snake_case 命名變數和函式
2. **4 空格縮排**：Python 強制使用縮排，標準是 4 個空格
3. **PEP 8 是基準**：遵循 PEP 8 風格指南
4. **Docstring 很重要**：使用 docstring 記錄函式和類別
5. **import 要分組**：標準庫、第三方、本地模組分開
6. **使用工具輔助**：Black/Ruff 格式化、mypy 型別檢查
7. **is 比較 None**：使用 `is None` 而非 `== None`
8. **真值判斷**：直接用 `if items:` 而非 `if len(items) > 0:`
