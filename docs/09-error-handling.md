# 第 9 章：錯誤處理

本章節詳細說明 Python 的例外處理機制，包含 try-except 語法、拋出例外、自訂例外類別，以及最佳實踐。

---

## 9.1 try-except（對應 try-catch）

### JavaScript vs Python 語法對照

```javascript
// JavaScript
try {
    // 可能出錯的程式碼
    riskyOperation();
} catch (error) {
    // 處理錯誤
    console.error(error.message);
} finally {
    // 一定會執行
    cleanup();
}
```

```python
# Python
try:
    # 可能出錯的程式碼
    risky_operation()
except Exception as e:
    # 處理錯誤
    print(f"錯誤：{e}")
finally:
    # 一定會執行
    cleanup()
```

### 基本 try-except 結構

```python
# 基本用法
try:
    result = 10 / 0
except ZeroDivisionError:
    print("不能除以零！")

# 捕捉例外物件
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"錯誤類型：{type(e).__name__}")
    print(f"錯誤訊息：{e}")

# 輸出：
# 錯誤類型：ZeroDivisionError
# 錯誤訊息：division by zero
```

### 捕捉多種例外

```python
# 方式 1：多個 except 區塊
try:
    value = int(input("請輸入數字："))
    result = 10 / value
except ValueError:
    print("請輸入有效的數字！")
except ZeroDivisionError:
    print("不能輸入零！")

# 方式 2：同時捕捉多種例外（相同處理方式）
try:
    value = int(input("請輸入數字："))
    result = 10 / value
except (ValueError, ZeroDivisionError) as e:
    print(f"輸入錯誤：{e}")

# 方式 3：分別處理後捕捉所有其他例外
try:
    risky_operation()
except ValueError:
    print("值錯誤")
except TypeError:
    print("型別錯誤")
except Exception as e:
    # 捕捉所有其他例外
    print(f"未預期的錯誤：{e}")
```

### else 子句

`else` 在沒有發生例外時執行：

```python
try:
    value = int(input("請輸入數字："))
    result = 10 / value
except ValueError:
    print("請輸入有效的數字！")
except ZeroDivisionError:
    print("不能輸入零！")
else:
    # 只有在沒有例外時才執行
    print(f"計算結果：{result}")

# 實際應用：資料庫操作
try:
    connection = database.connect()
    data = connection.query("SELECT * FROM users")
except DatabaseError as e:
    print(f"資料庫錯誤：{e}")
else:
    # 查詢成功，處理資料
    process_data(data)
finally:
    connection.close()
```

### finally 子句

`finally` 無論是否發生例外都會執行：

```python
# 基本用法
try:
    file = open("data.txt", "r")
    content = file.read()
except FileNotFoundError:
    print("檔案不存在")
finally:
    # 確保檔案被關閉
    if 'file' in locals() and not file.closed:
        file.close()

# finally 即使有 return 也會執行
def read_file(filename):
    try:
        with open(filename, "r") as f:
            return f.read()
    except FileNotFoundError:
        return None
    finally:
        print("清理資源...")  # 一定會執行

# finally 常見用途
# 1. 關閉檔案
# 2. 關閉資料庫連線
# 3. 釋放鎖
# 4. 清理暫存資源
```

### 完整的 try 語句結構

```python
try:
    # 嘗試執行的程式碼
    result = risky_operation()
except SpecificError as e:
    # 處理特定錯誤
    handle_specific_error(e)
except (Error1, Error2) as e:
    # 處理多種錯誤
    handle_multiple_errors(e)
except Exception as e:
    # 處理所有其他錯誤
    handle_unexpected_error(e)
else:
    # 沒有錯誤時執行
    process_result(result)
finally:
    # 無論如何都執行
    cleanup()
```

### 巢狀的 try-except

```python
try:
    try:
        result = int("not a number")
    except ValueError:
        print("內層捕捉到 ValueError")
        raise  # 重新拋出，讓外層處理
except ValueError:
    print("外層捕捉到 ValueError")

# 實際應用：多層操作
def process_file(filename):
    try:
        with open(filename, "r") as f:
            try:
                data = json.load(f)
            except json.JSONDecodeError as e:
                raise ValueError(f"JSON 格式錯誤：{e}")
    except FileNotFoundError:
        raise ValueError(f"檔案不存在：{filename}")
    return data
```

---

## 9.2 拋出例外

### raise 語句

```python
# 拋出例外
raise ValueError("無效的值")

# 拋出帶有詳細訊息的例外
raise TypeError(f"期望 str，但收到 {type(value).__name__}")

# 拋出不帶訊息的例外
raise RuntimeError

# JavaScript 對照
# throw new Error("錯誤訊息");
# throw new TypeError("型別錯誤");
```

### 條件式拋出例外

```python
def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("除數不能為零")
    return a / b

def set_age(age):
    if not isinstance(age, int):
        raise TypeError(f"age 必須是整數，收到 {type(age).__name__}")
    if age < 0:
        raise ValueError("age 不能為負數")
    if age > 150:
        raise ValueError("age 不能超過 150")
    return age

# 使用
try:
    set_age(-5)
except ValueError as e:
    print(f"錯誤：{e}")  # 錯誤：age 不能為負數
```

### 重新拋出例外

```python
# 使用 raise 重新拋出當前例外
try:
    risky_operation()
except ValueError as e:
    print(f"記錄錯誤：{e}")
    raise  # 重新拋出原始例外

# 拋出新例外並保留原始例外資訊
try:
    data = json.loads(invalid_json)
except json.JSONDecodeError as e:
    raise ValueError("設定檔格式錯誤") from e

# 使用 from None 隱藏原始例外
try:
    data = json.loads(invalid_json)
except json.JSONDecodeError:
    raise ValueError("設定檔格式錯誤") from None
```

### 例外鏈（Exception Chaining）

```python
def load_config(filename):
    try:
        with open(filename) as f:
            return json.load(f)
    except FileNotFoundError as e:
        # 將原始例外作為新例外的原因
        raise ConfigError(f"找不到設定檔：{filename}") from e
    except json.JSONDecodeError as e:
        raise ConfigError(f"設定檔格式錯誤：{filename}") from e

# 使用
try:
    config = load_config("config.json")
except ConfigError as e:
    print(f"設定錯誤：{e}")
    print(f"原因：{e.__cause__}")  # 原始例外
```

### assert 斷言

```python
# assert 用於開發期間的檢查
def calculate_average(numbers):
    assert len(numbers) > 0, "列表不能為空"
    assert all(isinstance(n, (int, float)) for n in numbers), "所有元素必須是數字"
    return sum(numbers) / len(numbers)

# assert 在 python -O（優化模式）下會被忽略
# 所以不要用 assert 做重要的驗證

# 正確用法：開發期間的不變條件檢查
def binary_search(arr, target):
    assert arr == sorted(arr), "陣列必須已排序"
    # ...

# 錯誤用法：使用者輸入驗證（應該用 if + raise）
def set_age(age):
    # 不要這樣做！
    # assert age >= 0, "age 不能為負數"

    # 應該這樣做
    if age < 0:
        raise ValueError("age 不能為負數")
```

---

## 9.3 自訂例外

### 基本自訂例外

```python
# 最簡單的自訂例外
class MyError(Exception):
    """自訂例外類別"""
    pass

# 使用
raise MyError("發生錯誤")

# 捕捉
try:
    raise MyError("測試錯誤")
except MyError as e:
    print(f"捕捉到 MyError：{e}")
```

### 帶有額外資訊的例外

```python
class ValidationError(Exception):
    """驗證錯誤"""

    def __init__(self, message, field=None, value=None):
        super().__init__(message)
        self.field = field
        self.value = value
        self.message = message

    def __str__(self):
        if self.field:
            return f"{self.field}: {self.message} (收到: {self.value!r})"
        return self.message

# 使用
def validate_email(email):
    if "@" not in email:
        raise ValidationError(
            "無效的 email 格式",
            field="email",
            value=email
        )
    return email

try:
    validate_email("invalid-email")
except ValidationError as e:
    print(f"驗證失敗：{e}")
    print(f"欄位：{e.field}")
    print(f"值：{e.value}")

# 輸出：
# 驗證失敗：email: 無效的 email 格式 (收到: 'invalid-email')
# 欄位：email
# 值：invalid-email
```

### 例外階層

```python
# 定義例外階層
class AppError(Exception):
    """應用程式基礎例外"""
    pass

class ConfigError(AppError):
    """設定相關錯誤"""
    pass

class DatabaseError(AppError):
    """資料庫相關錯誤"""
    pass

class ConnectionError(DatabaseError):
    """資料庫連線錯誤"""
    pass

class QueryError(DatabaseError):
    """資料庫查詢錯誤"""
    pass

class AuthError(AppError):
    """認證相關錯誤"""
    pass

class AuthenticationError(AuthError):
    """身份驗證失敗"""
    pass

class AuthorizationError(AuthError):
    """授權失敗"""
    pass

# 使用例外階層
try:
    connect_to_database()
except ConnectionError:
    print("連線失敗，嘗試重新連線...")
except DatabaseError:
    print("資料庫錯誤")
except AppError:
    print("應用程式錯誤")
```

### 實際應用範例

```python
# HTTP API 例外階層
class APIError(Exception):
    """API 基礎例外"""

    def __init__(self, message, status_code=500, details=None):
        super().__init__(message)
        self.message = message
        self.status_code = status_code
        self.details = details or {}

    def to_dict(self):
        return {
            "error": self.__class__.__name__,
            "message": self.message,
            "status_code": self.status_code,
            "details": self.details
        }

class BadRequestError(APIError):
    """400 Bad Request"""

    def __init__(self, message, details=None):
        super().__init__(message, status_code=400, details=details)

class NotFoundError(APIError):
    """404 Not Found"""

    def __init__(self, resource, resource_id):
        message = f"{resource} with id '{resource_id}' not found"
        super().__init__(message, status_code=404)
        self.resource = resource
        self.resource_id = resource_id

class UnauthorizedError(APIError):
    """401 Unauthorized"""

    def __init__(self, message="Authentication required"):
        super().__init__(message, status_code=401)

class ForbiddenError(APIError):
    """403 Forbidden"""

    def __init__(self, message="Permission denied"):
        super().__init__(message, status_code=403)

# 使用
def get_user(user_id):
    user = database.find_user(user_id)
    if user is None:
        raise NotFoundError("User", user_id)
    return user

def update_user(user_id, data, current_user):
    if not current_user.is_authenticated:
        raise UnauthorizedError()
    if current_user.id != user_id and not current_user.is_admin:
        raise ForbiddenError("只能修改自己的資料")
    if "email" in data and not is_valid_email(data["email"]):
        raise BadRequestError("無效的 email", details={"field": "email"})
    # ...

# API 處理器
def api_handler(func):
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except APIError as e:
            return {"error": e.to_dict()}, e.status_code
        except Exception as e:
            return {"error": "Internal server error"}, 500
    return wrapper
```

---

## 9.4 常見內建例外

### 例外階層圖

```
BaseException
├── SystemExit                 # sys.exit() 呼叫
├── KeyboardInterrupt          # Ctrl+C 中斷
├── GeneratorExit              # 生成器關閉
└── Exception                  # 所有一般例外的基礎類別
    ├── StopIteration          # 迭代結束
    ├── ArithmeticError        # 算術錯誤基礎類別
    │   ├── ZeroDivisionError  # 除以零
    │   ├── OverflowError      # 數值溢位
    │   └── FloatingPointError # 浮點數錯誤
    ├── AssertionError         # assert 失敗
    ├── AttributeError         # 屬性不存在
    ├── BufferError            # 緩衝區錯誤
    ├── EOFError               # 讀取到檔案結尾
    ├── ImportError            # 匯入失敗
    │   └── ModuleNotFoundError # 模組找不到
    ├── LookupError            # 查找錯誤基礎類別
    │   ├── IndexError         # 索引超出範圍
    │   └── KeyError           # 字典鍵不存在
    ├── MemoryError            # 記憶體不足
    ├── NameError              # 名稱未定義
    │   └── UnboundLocalError  # 局部變數未綁定
    ├── OSError                # 作業系統錯誤
    │   ├── FileNotFoundError  # 檔案不存在
    │   ├── PermissionError    # 權限不足
    │   ├── TimeoutError       # 操作超時
    │   └── ConnectionError    # 連線錯誤
    ├── RuntimeError           # 執行時錯誤
    │   ├── NotImplementedError # 未實作
    │   └── RecursionError     # 遞迴過深
    ├── SyntaxError            # 語法錯誤
    ├── TypeError              # 型別錯誤
    ├── ValueError             # 值錯誤
    │   └── UnicodeError       # Unicode 錯誤
    └── Warning                # 警告基礎類別
```

### 常見例外與處理方式

```python
# ValueError - 值不正確
try:
    num = int("not a number")
except ValueError:
    print("無法轉換為數字")

# TypeError - 型別不正確
try:
    result = "hello" + 123
except TypeError:
    print("不能將字串和數字相加")

# KeyError - 字典鍵不存在
try:
    data = {"name": "Alice"}
    print(data["age"])
except KeyError as e:
    print(f"鍵 {e} 不存在")

# IndexError - 索引超出範圍
try:
    items = [1, 2, 3]
    print(items[10])
except IndexError:
    print("索引超出範圍")

# AttributeError - 屬性不存在
try:
    "hello".non_existent_method()
except AttributeError:
    print("方法不存在")

# FileNotFoundError - 檔案不存在
try:
    with open("non_existent.txt") as f:
        content = f.read()
except FileNotFoundError:
    print("檔案不存在")

# PermissionError - 權限不足
try:
    with open("/root/secret.txt") as f:
        content = f.read()
except PermissionError:
    print("沒有讀取權限")

# ImportError / ModuleNotFoundError
try:
    import non_existent_module
except ModuleNotFoundError:
    print("模組不存在")

# ZeroDivisionError - 除以零
try:
    result = 10 / 0
except ZeroDivisionError:
    print("不能除以零")

# RecursionError - 遞迴過深
def infinite_recursion():
    return infinite_recursion()

try:
    infinite_recursion()
except RecursionError:
    print("遞迴過深")
```

### 不應該捕捉的例外

```python
# 不要捕捉這些例外（除非有特殊需求）

# SystemExit - sys.exit() 會觸發
# 捕捉它會阻止程式正常退出
try:
    sys.exit(0)
except SystemExit:
    print("程式不會退出")  # 不好的做法

# KeyboardInterrupt - Ctrl+C 會觸發
# 捕捉它會讓使用者無法中斷程式
try:
    while True:
        pass
except KeyboardInterrupt:
    print("捕捉到 Ctrl+C")  # 通常應該讓程式退出

# 如果需要清理資源，使用 finally
try:
    while True:
        do_work()
except KeyboardInterrupt:
    print("程式被中斷")
finally:
    cleanup()
    # 不要在這裡吞掉例外
```

---

## 9.5 Context Manager 與例外處理

### with 語句自動處理例外

```python
# with 語句會自動處理資源釋放
with open("file.txt", "r") as f:
    content = f.read()
# 即使發生例外，檔案也會被關閉

# 等價於
f = open("file.txt", "r")
try:
    content = f.read()
finally:
    f.close()

# 多個 context manager
with open("input.txt") as fin, open("output.txt", "w") as fout:
    fout.write(fin.read())
```

### contextlib 模組

```python
from contextlib import contextmanager, suppress

# 使用 @contextmanager 建立 context manager
@contextmanager
def timer(name):
    import time
    start = time.time()
    try:
        yield  # 這裡執行 with 區塊的程式碼
    finally:
        elapsed = time.time() - start
        print(f"{name} 耗時：{elapsed:.4f} 秒")

with timer("資料處理"):
    # 耗時操作
    process_data()

# suppress - 忽略特定例外
from contextlib import suppress

# 傳統寫法
try:
    os.remove("file.txt")
except FileNotFoundError:
    pass

# 使用 suppress
with suppress(FileNotFoundError):
    os.remove("file.txt")

# 多個例外
with suppress(FileNotFoundError, PermissionError):
    os.remove("file.txt")
```

### 自訂 Context Manager 處理例外

```python
class DatabaseTransaction:
    def __init__(self, connection):
        self.connection = connection

    def __enter__(self):
        self.connection.begin_transaction()
        return self.connection

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is not None:
            # 發生例外，回滾
            self.connection.rollback()
            print(f"交易回滾：{exc_val}")
            # 回傳 False 會繼續傳播例外
            # 回傳 True 會抑制例外
            return False
        else:
            # 沒有例外，提交
            self.connection.commit()
            return True

# 使用
with DatabaseTransaction(connection) as conn:
    conn.execute("INSERT INTO users VALUES (...)")
    conn.execute("UPDATE accounts SET ...")
    # 如果任何操作失敗，會自動回滾
```

---

## 9.6 例外處理最佳實踐

### 1. 捕捉具體的例外

```python
# 不好：捕捉所有例外
try:
    do_something()
except:  # 或 except Exception:
    pass  # 吞掉所有錯誤

# 好：捕捉具體的例外
try:
    do_something()
except ValueError as e:
    handle_value_error(e)
except TypeError as e:
    handle_type_error(e)
```

### 2. 不要吞掉例外

```python
# 不好：吞掉例外
try:
    risky_operation()
except Exception:
    pass  # 錯誤被忽略，難以除錯

# 好：至少記錄例外
import logging

try:
    risky_operation()
except Exception as e:
    logging.exception("操作失敗")  # 記錄完整的堆疊追蹤
    # 或重新拋出
    raise
```

### 3. 使用 EAFP 原則

Python 傾向使用「請求寬恕比請求許可更容易」(EAFP) 的風格：

```python
# LBYL (Look Before You Leap) - 先檢查
# 較不 Pythonic
if key in dictionary:
    value = dictionary[key]
else:
    value = default

if hasattr(obj, "method"):
    obj.method()

# EAFP (Easier to Ask Forgiveness than Permission) - 直接做
# 較 Pythonic
try:
    value = dictionary[key]
except KeyError:
    value = default

# 或使用 get()
value = dictionary.get(key, default)

try:
    obj.method()
except AttributeError:
    pass
```

### 4. 保持 try 區塊精簡

```python
# 不好：try 區塊太大
try:
    data = load_data()
    processed = process_data(data)
    result = calculate_result(processed)
    save_result(result)
except Exception as e:
    print(f"錯誤：{e}")  # 不知道是哪一步出錯

# 好：精確捕捉
try:
    data = load_data()
except FileNotFoundError:
    print("資料檔案不存在")
    return

try:
    processed = process_data(data)
except ValueError as e:
    print(f"資料格式錯誤：{e}")
    return

# ...
```

### 5. 提供有用的錯誤訊息

```python
# 不好：訊息不明確
raise ValueError("錯誤")

# 好：訊息包含有用資訊
raise ValueError(
    f"無效的 email 格式：'{email}'，"
    f"email 必須包含 '@' 字元"
)

# 使用自訂例外提供結構化資訊
class ValidationError(Exception):
    def __init__(self, field, message, value=None):
        self.field = field
        self.message = message
        self.value = value
        super().__init__(f"{field}: {message}")
```

### 6. 使用例外而非回傳錯誤碼

```python
# 不好：使用錯誤碼
def divide(a, b):
    if b == 0:
        return None, "除數不能為零"
    return a / b, None

result, error = divide(10, 0)
if error:
    print(error)

# 好：使用例外
def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("除數不能為零")
    return a / b

try:
    result = divide(10, 0)
except ZeroDivisionError as e:
    print(e)
```

### 7. 清理資源使用 finally 或 with

```python
# 使用 finally
file = None
try:
    file = open("data.txt")
    process(file.read())
finally:
    if file:
        file.close()

# 更好：使用 with
with open("data.txt") as file:
    process(file.read())
```

---

## 9.7 除錯與追蹤

### 取得例外資訊

```python
import traceback
import sys

try:
    1 / 0
except ZeroDivisionError as e:
    # 例外類型和訊息
    print(f"類型：{type(e).__name__}")
    print(f"訊息：{e}")

    # 取得完整的堆疊追蹤
    print("\n堆疊追蹤：")
    traceback.print_exc()

    # 取得堆疊追蹤字串
    tb_str = traceback.format_exc()

    # 取得例外資訊元組
    exc_type, exc_value, exc_tb = sys.exc_info()
```

### logging 與例外

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

try:
    risky_operation()
except Exception as e:
    # 記錄例外（包含堆疊追蹤）
    logger.exception("操作失敗")

    # 或使用 error 加上 exc_info
    logger.error("操作失敗", exc_info=True)

    # 只記錄訊息
    logger.error(f"操作失敗：{e}")
```

### 自訂例外的堆疊追蹤

```python
class AppError(Exception):
    def __init__(self, message):
        super().__init__(message)
        # 儲存建立時的堆疊追蹤
        self.traceback = traceback.format_exc()

try:
    try:
        1 / 0
    except ZeroDivisionError:
        raise AppError("計算錯誤")
except AppError as e:
    print(f"錯誤：{e}")
    print(f"原始追蹤：\n{e.traceback}")
```

---

## 練習題

### 練習 1：安全的字典存取

寫一個函式，安全地從巢狀字典取值：

```python
def safe_get(data, *keys, default=None):
    """
    安全地從巢狀字典取值

    >>> data = {"a": {"b": {"c": 1}}}
    >>> safe_get(data, "a", "b", "c")
    1
    >>> safe_get(data, "a", "x", "y", default=0)
    0
    """
    # 你的程式碼
    pass
```

### 練習 2：重試裝飾器

寫一個裝飾器，在函式失敗時自動重試：

```python
def retry(max_attempts=3, exceptions=(Exception,)):
    """
    重試裝飾器

    @retry(max_attempts=3, exceptions=(ConnectionError,))
    def fetch_data():
        ...
    """
    # 你的程式碼
    pass
```

### 練習 3：自訂例外階層

為一個電商系統設計例外階層：

```python
# 設計以下例外類別：
# - OrderError (基礎)
#   - InvalidOrderError
#   - PaymentError
#     - InsufficientFundsError
#     - PaymentDeclinedError
#   - ShippingError
#     - AddressNotFoundError
#     - OutOfStockError
```

---

## 小結

### JavaScript vs Python 例外處理對照

| 特性 | JavaScript | Python |
|------|------------|--------|
| 捕捉例外 | `try...catch` | `try...except` |
| 最終執行 | `finally` | `finally` |
| 拋出例外 | `throw new Error()` | `raise Exception()` |
| 無例外時 | 無 | `else` |
| 例外類型 | `Error`, `TypeError`, ... | `Exception`, `TypeError`, ... |
| 自訂例外 | `class MyError extends Error` | `class MyError(Exception)` |

### try 語句結構

```python
try:
    # 嘗試執行
except SpecificError:
    # 處理特定錯誤
except (Error1, Error2) as e:
    # 處理多種錯誤
except Exception as e:
    # 處理所有其他錯誤
else:
    # 沒有錯誤時執行
finally:
    # 無論如何都執行
```

### 最佳實踐摘要

1. **捕捉具體的例外**，不要使用裸露的 `except:`
2. **不要吞掉例外**，至少要記錄
3. **使用 EAFP 原則**
4. **保持 try 區塊精簡**
5. **提供有用的錯誤訊息**
6. **使用例外而非錯誤碼**
7. **使用 with 語句管理資源**
8. **建立有意義的例外階層**
