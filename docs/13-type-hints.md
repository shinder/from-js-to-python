# 第 13 章：型別提示（Type Hints）

## 概述

對於熟悉 TypeScript 的 JavaScript 開發者來說，Python 的型別提示會感到非常親切。兩者都是在動態型別語言上添加靜態型別檢查的解決方案。Python 3.5 開始引入型別提示，並在後續版本中持續強化。

### JavaScript/TypeScript vs Python 型別系統對比

| 特性       | TypeScript         | Python Type Hints        |
| ---------- | ------------------ | ------------------------ |
| 引入時間   | 2012 年            | Python 3.5（2015）       |
| 強制性     | 可選（但建議使用） | 完全可選                 |
| 執行時檢查 | 編譯時移除         | 執行時可存取（但不強制） |
| 檢查工具   | tsc                | mypy, pyright, pytype    |
| 泛型支援   | 完整               | 完整                     |
| 推斷能力   | 強大               | 較弱（依賴工具）         |

---

## 13.1 基本型別提示

### 變數型別註解

```python
# Python 3.6+ 變數註解
name: str = "Alice"
age: int = 30
height: float = 1.75
is_active: bool = True
data: bytes = b"hello"

# 可以只宣告型別而不賦值
username: str  # 宣告但未初始化
```

對比 TypeScript：

```typescript
// TypeScript
let name: string = "Alice";
let age: number = 30;
let height: number = 1.75;
let isActive: boolean = true;

let username: string;  // 宣告但未初始化
```

### 函式型別提示

```python
# 參數和回傳值型別
def greet(name: str) -> str:
    return f"Hello, {name}"

# 無回傳值使用 None
def print_message(message: str) -> None:
    print(message)

# 多個參數
def add(a: int, b: int) -> int:
    return a + b

# 預設參數
def greet_with_default(name: str = "World") -> str:
    return f"Hello, {name}"
```

對比 TypeScript：

```typescript
// TypeScript
function greet(name: string): string {
    return `Hello, ${name}`;
}

function printMessage(message: string): void {
    console.log(message);
}

function add(a: number, b: number): number {
    return a + b;
}
```

### 容器型別

Python 3.9+ 可以直接使用內建型別作為泛型：

```python
# Python 3.9+ 寫法（推薦）
names: list[str] = ["Alice", "Bob"]
scores: dict[str, int] = {"Alice": 100, "Bob": 95}
coordinates: tuple[float, float] = (10.5, 20.3)
unique_ids: set[int] = {1, 2, 3}

# 巢狀容器
matrix: list[list[int]] = [[1, 2], [3, 4]]
user_data: dict[str, list[int]] = {"scores": [90, 85, 88]}
```

Python 3.8 及更早版本需要從 `typing` 匯入：

```python
# Python 3.8 及更早版本
from typing import List, Dict, Tuple, Set

names: List[str] = ["Alice", "Bob"]
scores: Dict[str, int] = {"Alice": 100}
coordinates: Tuple[float, float] = (10.5, 20.3)
unique_ids: Set[int] = {1, 2, 3}
```

對比 TypeScript：

```typescript
// TypeScript
let names: string[] = ["Alice", "Bob"];
let scores: { [key: string]: number } = { "Alice": 100 };
// 或使用 Record
let scores2: Record<string, number> = { "Alice": 100 };
let coordinates: [number, number] = [10.5, 20.3];
let uniqueIds: Set<number> = new Set([1, 2, 3]);
```

### Tuple 的特殊用法

```python
# 固定長度和型別的 tuple
point_2d: tuple[int, int] = (10, 20)
point_3d: tuple[int, int, int] = (10, 20, 30)

# 可變長度 tuple（同質元素）
numbers: tuple[int, ...] = (1, 2, 3, 4, 5)

# 命名 tuple（類似 TypeScript 的 interface）
from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float
    label: str = "origin"  # 預設值

p = Point(1.0, 2.0, "A")
print(p.x, p.y, p.label)
```

---

## 13.2 進階型別

### Optional - 可能為 None

```python
from typing import Optional

# Optional[X] 等同於 Union[X, None]
def find_user(user_id: int) -> Optional[str]:
    if user_id == 1:
        return "Alice"
    return None

# Python 3.10+ 可以用更簡潔的語法
def find_user_v2(user_id: int) -> str | None:
    if user_id == 1:
        return "Alice"
    return None
```

對比 TypeScript：

```typescript
// TypeScript
function findUser(userId: number): string | null {
    if (userId === 1) {
        return "Alice";
    }
    return null;
}
```

### Union - 多種型別

```python
from typing import Union

# 接受多種型別
def process(data: Union[str, bytes]) -> str:
    if isinstance(data, bytes):
        return data.decode("utf-8")
    return data

# Python 3.10+ 語法
def process_v2(data: str | bytes) -> str:
    if isinstance(data, bytes):
        return data.decode("utf-8")
    return data

# 多於兩種型別
def flexible_input(value: Union[int, float, str]) -> str:
    return str(value)

# Python 3.10+
def flexible_input_v2(value: int | float | str) -> str:
    return str(value)
```

對比 TypeScript：

```typescript
// TypeScript
function process(data: string | Uint8Array): string {
    if (data instanceof Uint8Array) {
        return new TextDecoder().decode(data);
    }
    return data;
}
```

### Callable - 函式型別

```python
from typing import Callable

# 定義函式型別：接受參數型別和回傳型別
def apply_twice(func: Callable[[int], int], value: int) -> int:
    return func(func(value))

def double(x: int) -> int:
    return x * 2

result = apply_twice(double, 5)  # 20

# 更複雜的函式型別
Handler = Callable[[str, int], bool]

def process_with_handler(handler: Handler, name: str, count: int) -> bool:
    return handler(name, count)

# 無參數函式
NoArgFunc = Callable[[], str]

# 任意參數
AnyArgsFunc = Callable[..., str]  # ... 表示任意參數
```

對比 TypeScript：

```typescript
// TypeScript
function applyTwice(func: (x: number) => number, value: number): number {
    return func(func(value));
}

type Handler = (name: string, count: number) => boolean;
```

### Any 與 object

```python
from typing import Any

# Any：任何型別，跳過型別檢查
def process_anything(data: Any) -> Any:
    return data

# object：所有型別的基底類別（比 Any 嚴格）
def print_object(obj: object) -> None:
    print(obj)
```

**Any vs object 的差異：**

```python
from typing import Any

def use_any(x: Any) -> None:
    x.foo()  # 型別檢查器不會報錯
    x + 1    # 型別檢查器不會報錯

def use_object(x: object) -> None:
    x.foo()  # 型別檢查器會報錯！object 沒有 foo 方法
    x + 1    # 型別檢查器會報錯！
```

### Literal - 字面值型別

```python
from typing import Literal

# 只接受特定值
def set_direction(direction: Literal["left", "right", "up", "down"]) -> None:
    print(f"Moving {direction}")

set_direction("left")    # OK
set_direction("center")  # 型別錯誤！

# 結合使用
Mode = Literal["r", "w", "a", "rb", "wb"]

def open_file(filename: str, mode: Mode) -> None:
    pass
```

對比 TypeScript：

```typescript
// TypeScript
type Direction = "left" | "right" | "up" | "down";

function setDirection(direction: Direction): void {
    console.log(`Moving ${direction}`);
}
```

### Final - 常數

```python
from typing import Final

# 宣告常數（不應被重新賦值）
MAX_SIZE: Final[int] = 100
API_URL: Final = "https://api.example.com"  # 型別會被推斷

# 類別中的常數
class Config:
    DEBUG: Final[bool] = False
    VERSION: Final = "1.0.0"
```

對比 TypeScript：

```typescript
// TypeScript
const MAX_SIZE: number = 100;
const API_URL = "https://api.example.com" as const;
```

### TypeVar - 泛型型別變數

```python
from typing import TypeVar, List

# 定義泛型型別變數
T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]

# 使用
first([1, 2, 3])      # 回傳 int
first(["a", "b"])     # 回傳 str

# 限制型別變數的範圍
Number = TypeVar("Number", int, float)

def add_numbers(a: Number, b: Number) -> Number:
    return a + b

# 有上界的型別變數
from typing import Sequence

S = TypeVar("S", bound=Sequence)

def get_length(seq: S) -> int:
    return len(seq)
```

對比 TypeScript：

```typescript
// TypeScript
function first<T>(items: T[]): T {
    return items[0];
}
```

### Generic - 泛型類別

```python
from typing import TypeVar, Generic

T = TypeVar("T")

class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: list[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        return self._items.pop()

    def is_empty(self) -> bool:
        return len(self._items) == 0

# 使用
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)

str_stack: Stack[str] = Stack()
str_stack.push("hello")
```

對比 TypeScript：

```typescript
// TypeScript
class Stack<T> {
    private items: T[] = [];

    push(item: T): void {
        this.items.push(item);
    }

    pop(): T | undefined {
        return this.items.pop();
    }
}
```

### Protocol - 結構化型別（鴨子型別）

Python 的 Protocol 類似 TypeScript 的 interface，實現結構化子型別：

```python
from typing import Protocol

# 定義 Protocol（類似 interface）
class Drawable(Protocol):
    def draw(self) -> None: ...

class Circle:
    def draw(self) -> None:
        print("Drawing circle")

class Square:
    def draw(self) -> None:
        print("Drawing square")

# 不需要顯式繼承，只要有相同的方法簽名即可
def render(shape: Drawable) -> None:
    shape.draw()

render(Circle())  # OK
render(Square())  # OK

# 帶屬性的 Protocol
class Named(Protocol):
    name: str

class Person:
    def __init__(self, name: str):
        self.name = name

def greet(obj: Named) -> str:
    return f"Hello, {obj.name}"
```

對比 TypeScript：

```typescript
// TypeScript
interface Drawable {
    draw(): void;
}

class Circle {
    draw(): void {
        console.log("Drawing circle");
    }
}

function render(shape: Drawable): void {
    shape.draw();
}
```

### TypedDict - 型別化字典

```python
from typing import TypedDict

# 定義字典結構（類似 TypeScript 的 interface）
class UserDict(TypedDict):
    name: str
    age: int
    email: str

user: UserDict = {
    "name": "Alice",
    "age": 30,
    "email": "alice@example.com"
}

# 可選欄位（Python 3.11+）
class UserDictV2(TypedDict, total=False):
    name: str      # 必填
    age: int       # 必填
    email: str     # 選填（因為 total=False）

# 混合必填和選填
from typing import Required, NotRequired

class UserDictV3(TypedDict):
    name: str                      # 必填
    age: int                       # 必填
    email: NotRequired[str]        # 選填
    nickname: NotRequired[str]     # 選填
```

對比 TypeScript：

```typescript
// TypeScript
interface UserDict {
    name: string;
    age: number;
    email: string;
}

interface UserDictV2 {
    name: string;
    age: number;
    email?: string;  // 選填
}
```

---

## 13.3 特殊型別工具

### cast - 型別轉換

```python
from typing import cast

# 告訴型別檢查器這是特定型別（不會在執行時轉換）
def get_config() -> dict:
    return {"debug": True}

config = cast(dict[str, bool], get_config())
```

### TYPE_CHECKING - 只在型別檢查時匯入

```python
from typing import TYPE_CHECKING

# 避免循環匯入
if TYPE_CHECKING:
    from .models import User

class UserService:
    def get_user(self) -> "User":  # 使用字串形式的前向參照
        pass
```

### 前向參照（Forward Reference）

```python
from __future__ import annotations  # Python 3.7+ 啟用延遲評估

class Node:
    def __init__(self, value: int):
        self.value = value
        self.next: Node | None = None  # 自我參照

    def set_next(self, node: Node) -> None:
        self.next = node

# 或使用字串形式
class TreeNode:
    def __init__(self, value: int):
        self.value = value
        self.left: "TreeNode | None" = None
        self.right: "TreeNode | None" = None
```

### @overload - 函式重載

```python
from typing import overload

# 定義多個函式簽名
@overload
def process(x: int) -> int: ...

@overload
def process(x: str) -> str: ...

@overload
def process(x: list[int]) -> list[int]: ...

# 實際實作
def process(x: int | str | list[int]) -> int | str | list[int]:
    if isinstance(x, int):
        return x * 2
    elif isinstance(x, str):
        return x.upper()
    else:
        return [i * 2 for i in x]
```

對比 TypeScript：

```typescript
// TypeScript
function process(x: number): number;
function process(x: string): string;
function process(x: number[]): number[];
function process(x: number | string | number[]): number | string | number[] {
    if (typeof x === "number") {
        return x * 2;
    } else if (typeof x === "string") {
        return x.toUpperCase();
    } else {
        return x.map(i => i * 2);
    }
}
```

### ParamSpec 與 Concatenate - 裝飾器型別

Python 3.10+ 提供更精確的裝飾器型別：

```python
from typing import ParamSpec, Concatenate, Callable, TypeVar

P = ParamSpec("P")
R = TypeVar("R")

# 保留原函式的參數型別
def logged(func: Callable[P, R]) -> Callable[P, R]:
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@logged
def add(a: int, b: int) -> int:
    return a + b

# add 保留了 (int, int) -> int 的型別
```

---

## 13.4 型別檢查工具

### mypy

mypy 是最廣泛使用的 Python 型別檢查器。

**安裝：**

```bash
pip install mypy
```

**基本使用：**

```bash
# 檢查單一檔案
mypy script.py

# 檢查整個專案
mypy src/

# 嚴格模式
mypy --strict script.py
```

**設定檔 (mypy.ini 或 pyproject.toml)：**

```ini
# mypy.ini
[mypy]
python_version = 3.11
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True

# 忽略特定模組
[mypy-requests.*]
ignore_missing_imports = True
```

```toml
# pyproject.toml
[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true

[[tool.mypy.overrides]]
module = "requests.*"
ignore_missing_imports = true
```

### pyright

Microsoft 開發的型別檢查器，速度快且功能強大。

**安裝：**

```bash
pip install pyright
# 或使用 npm
npm install -g pyright
```

**基本使用：**

```bash
pyright script.py
pyright src/
```

**設定檔 (pyrightconfig.json)：**

```json
{
    "include": ["src"],
    "exclude": ["**/node_modules", "**/__pycache__"],
    "typeCheckingMode": "strict",
    "pythonVersion": "3.11",
    "reportMissingImports": true,
    "reportMissingTypeStubs": false
}
```

### 工具比較

| 特性     | mypy                  | pyright         |
| -------- | --------------------- | --------------- |
| 開發者   | Dropbox / Python 社群 | Microsoft       |
| 速度     | 中等                  | 快              |
| IDE 整合 | 良好                  | 優秀（VS Code） |
| 嚴格程度 | 可配置                | 可配置          |
| 增量檢查 | 支援                  | 支援            |
| 語言     | Python                | TypeScript      |

---

## 13.5 dataclass 與型別提示

`dataclass` 是 Python 3.7+ 引入的裝飾器，與型別提示完美配合：

```python
from dataclasses import dataclass, field
from typing import Optional

@dataclass
class User:
    name: str
    age: int
    email: Optional[str] = None
    tags: list[str] = field(default_factory=list)

# 自動生成 __init__, __repr__, __eq__ 等方法
user = User(name="Alice", age=30)
print(user)  # User(name='Alice', age=30, email=None, tags=[])

# 不可變版本
@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
# p.x = 3.0  # 會拋出 FrozenInstanceError
```

對比 TypeScript：

```typescript
// TypeScript - 需要手動定義
interface User {
    name: string;
    age: number;
    email?: string;
    tags: string[];
}

class UserClass {
    constructor(
        public name: string,
        public age: number,
        public email?: string,
        public tags: string[] = []
    ) {}
}
```

### Pydantic - 資料驗證

Pydantic 是流行的資料驗證庫，結合型別提示和執行時驗證：

```python
from pydantic import BaseModel, EmailStr, validator
from typing import Optional

class User(BaseModel):
    name: str
    age: int
    email: EmailStr
    tags: list[str] = []

    @validator("age")
    def age_must_be_positive(cls, v):
        if v < 0:
            raise ValueError("Age must be positive")
        return v

# 自動驗證和轉換
user = User(name="Alice", age="30", email="alice@example.com")
print(user.age)  # 30 (int，自動轉換)

# 驗證失敗會拋出錯誤
try:
    invalid_user = User(name="Bob", age=-5, email="invalid")
except Exception as e:
    print(e)
```

---

## 13.6 最佳實踐

### 1. 漸進式採用

不需要一次為所有程式碼加上型別，可以漸進式採用：

```python
# 先從公開 API 開始
def public_api(data: dict[str, Any]) -> list[str]:
    # 內部實作可以暫時不加型別
    result = []
    for key, value in data.items():
        result.append(str(value))
    return result
```

### 2. 避免過度使用 Any

```python
# 不好的做法
def process(data: Any) -> Any:
    return data

# 好的做法
from typing import TypeVar

T = TypeVar("T")

def process(data: T) -> T:
    return data
```

### 3. 使用型別別名提高可讀性

```python
from typing import TypeAlias

# 型別別名
UserId: TypeAlias = int
UserName: TypeAlias = str
UserDict: TypeAlias = dict[UserId, UserName]

def get_users() -> UserDict:
    return {1: "Alice", 2: "Bob"}

# Python 3.12+ 新語法
type UserId = int
type UserName = str
type UserDict = dict[UserId, UserName]
```

### 4. 使用 Protocol 而非具體類別

```python
from typing import Protocol

# 好的做法：使用 Protocol 定義介面
class Reader(Protocol):
    def read(self) -> str: ...

def process_input(reader: Reader) -> str:
    return reader.read().upper()

# 任何有 read() 方法的物件都可以使用
```

### 5. 正確處理可變預設值

```python
from typing import Optional

# 不好的做法
def bad_func(items: list[int] = []) -> list[int]:  # 可變預設值！
    items.append(1)
    return items

# 好的做法
def good_func(items: Optional[list[int]] = None) -> list[int]:
    if items is None:
        items = []
    items.append(1)
    return items
```

---

## 練習題

### 練習 1：基本型別提示

為以下函式添加型別提示：

```python
def calculate_average(numbers):
    if not numbers:
        return None
    return sum(numbers) / len(numbers)

def merge_dicts(dict1, dict2):
    result = dict1.copy()
    result.update(dict2)
    return result
```

**參考答案：**

```python
def calculate_average(numbers: list[float]) -> float | None:
    if not numbers:
        return None
    return sum(numbers) / len(numbers)

def merge_dicts(
    dict1: dict[str, Any],
    dict2: dict[str, Any]
) -> dict[str, Any]:
    result = dict1.copy()
    result.update(dict2)
    return result
```

### 練習 2：泛型函式

實作一個泛型的 `find_first` 函式：

```python
# 找到列表中第一個符合條件的元素，若無則回傳 None
def find_first(items, predicate):
    for item in items:
        if predicate(item):
            return item
    return None
```

**參考答案：**

```python
from typing import TypeVar, Callable, Optional

T = TypeVar("T")

def find_first(
    items: list[T],
    predicate: Callable[[T], bool]
) -> Optional[T]:
    for item in items:
        if predicate(item):
            return item
    return None
```

### 練習 3：Protocol 定義

定義一個 `Comparable` Protocol，讓任何可比較的物件都能使用：

**參考答案：**

```python
from typing import Protocol, TypeVar

T = TypeVar("T")

class Comparable(Protocol):
    def __lt__(self, other: "Comparable") -> bool: ...
    def __gt__(self, other: "Comparable") -> bool: ...
    def __eq__(self, other: object) -> bool: ...

C = TypeVar("C", bound=Comparable)

def find_max(items: list[C]) -> C | None:
    if not items:
        return None
    return max(items)
```

### 練習 4：TypedDict 使用

定義一個 API 回應的型別：

**參考答案：**

```python
from typing import TypedDict, NotRequired

class ApiResponse(TypedDict):
    status: int
    message: str
    data: NotRequired[dict[str, Any]]
    errors: NotRequired[list[str]]

def handle_response(response: ApiResponse) -> None:
    print(f"Status: {response['status']}")
    print(f"Message: {response['message']}")
    if "data" in response:
        print(f"Data: {response['data']}")
    if "errors" in response:
        for error in response["errors"]:
            print(f"Error: {error}")
```

---

## 總結

### JavaScript/TypeScript vs Python 型別提示對照表

| 概念       | TypeScript                    | Python                                |
| ---------- | ----------------------------- | ------------------------------------- |
| 基本型別   | `string`, `number`, `boolean` | `str`, `int`, `float`, `bool`         |
| 陣列       | `string[]` 或 `Array<string>` | `list[str]`                           |
| 物件/字典  | `{ [key: string]: number }`   | `dict[str, int]`                      |
| 元組       | `[string, number]`            | `tuple[str, int]`                     |
| 可選       | `string \| null`              | `Optional[str]` 或 `str \| None`      |
| 聯合型別   | `string \| number`            | `Union[str, int]` 或 `str \| int`     |
| 函式型別   | `(x: number) => string`       | `Callable[[int], str]`                |
| 泛型       | `<T>`                         | `TypeVar("T")`                        |
| 介面       | `interface`                   | `Protocol` 或 `TypedDict`             |
| 字面值型別 | `"a" \| "b" \| "c"`           | `Literal["a", "b", "c"]`              |
| 任意型別   | `any`                         | `Any`                                 |
| 未知型別   | `unknown`                     | `object`                              |
| 常數       | `const` / `as const`          | `Final`                               |
| 型別別名   | `type X = ...`                | `TypeAlias` 或 `type X = ...` (3.12+) |
| 重載       | 函式重載                      | `@overload`                           |

### 重點回顧

1. **型別提示是可選的**：Python 不強制使用型別提示，但使用它能提高程式碼品質
2. **執行時不檢查**：型別提示在執行時不會進行驗證，需要使用工具如 mypy 或 pyright
3. **Python 3.9+ 簡化語法**：可直接使用 `list[str]` 而非 `List[str]`
4. **Python 3.10+ Union 語法**：可使用 `X | Y` 而非 `Union[X, Y]`
5. **Protocol 實現鴨子型別**：不需要顯式繼承，只要結構相符即可
6. **TypedDict 定義字典結構**：類似 TypeScript 的 interface
7. **使用型別檢查工具**：mypy 和 pyright 是主流選擇
