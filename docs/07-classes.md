# 第 7 章：類別與物件導向

本章節詳細說明 Python 的類別定義、繼承機制、存取控制，以及特殊方法（Magic Methods）的使用。

---

## 7.1 類別定義

### 基本類別定義

#### JavaScript 的類別

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        return `Hello, I'm ${this.name}`;
    }
}

const person = new Person("Alice", 30);
console.log(person.greet());
```

#### Python 的類別

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, I'm {self.name}"

# 建立實例（不需要 new）
person = Person("Alice", 30)
print(person.greet())  # "Hello, I'm Alice"
```

### 主要語法差異

| 特性 | JavaScript | Python |
|------|------------|--------|
| 建構子 | `constructor()` | `__init__()` |
| 實例參考 | `this` | `self`（必須明確宣告） |
| 建立實例 | `new ClassName()` | `ClassName()` |
| 方法定義 | `method() {}` | `def method(self):` |

### __init__ 方法（建構子）

```python
class Person:
    def __init__(self, name, age, city="Unknown"):
        """初始化方法，建立實例時自動呼叫"""
        self.name = name      # 實例屬性
        self.age = age
        self.city = city

# 建立實例
alice = Person("Alice", 30)
bob = Person("Bob", 25, "Taipei")

print(alice.name)   # "Alice"
print(alice.city)   # "Unknown"
print(bob.city)     # "Taipei"

# 修改屬性
alice.age = 31
alice.email = "alice@example.com"  # 動態新增屬性
```

### self 參數

Python 中所有實例方法的第一個參數都是 `self`，代表實例本身：

```python
class Calculator:
    def __init__(self, value=0):
        self.value = value

    def add(self, n):
        self.value += n
        return self  # 回傳 self 可以實現鏈式呼叫

    def subtract(self, n):
        self.value -= n
        return self

    def multiply(self, n):
        self.value *= n
        return self

    def get_value(self):
        return self.value

# 鏈式呼叫
calc = Calculator(10)
result = calc.add(5).multiply(2).subtract(10).get_value()
print(result)  # (10 + 5) * 2 - 10 = 20

# self 必須明確寫出，不能省略
class Wrong:
    def method():  # 錯誤！缺少 self
        pass
```

### 類別屬性 vs 實例屬性

```python
class Dog:
    # 類別屬性（所有實例共享）
    species = "Canis familiaris"
    count = 0

    def __init__(self, name, age):
        # 實例屬性（每個實例獨立）
        self.name = name
        self.age = age
        Dog.count += 1  # 修改類別屬性

# 類別屬性可以透過類別或實例存取
print(Dog.species)         # "Canis familiaris"

dog1 = Dog("Buddy", 3)
dog2 = Dog("Max", 5)

print(dog1.species)        # "Canis familiaris"
print(dog2.species)        # "Canis familiaris"
print(Dog.count)           # 2

# 透過實例修改會建立實例屬性，不影響類別屬性
dog1.species = "Modified"
print(dog1.species)        # "Modified"（實例屬性）
print(dog2.species)        # "Canis familiaris"（類別屬性）
print(Dog.species)         # "Canis familiaris"（類別屬性）

# 注意：可變的類別屬性可能導致意外
class BadExample:
    items = []  # 危險！所有實例共享同一個列表

    def add_item(self, item):
        self.items.append(item)

a = BadExample()
b = BadExample()
a.add_item("x")
print(b.items)  # ["x"] - b 也受影響了！

# 正確做法：在 __init__ 中初始化可變屬性
class GoodExample:
    def __init__(self):
        self.items = []  # 每個實例有自己的列表
```

### 類別方法與靜態方法

```python
class MyClass:
    class_var = "類別變數"

    def __init__(self, value):
        self.instance_var = value

    # 實例方法：第一個參數是 self（實例）
    def instance_method(self):
        return f"實例方法，存取 {self.instance_var}"

    # 類別方法：第一個參數是 cls（類別）
    @classmethod
    def class_method(cls):
        return f"類別方法，存取 {cls.class_var}"

    # 靜態方法：沒有自動傳入的參數
    @staticmethod
    def static_method(x, y):
        return f"靜態方法，計算 {x} + {y} = {x + y}"

# 使用方式
obj = MyClass("實例值")

# 實例方法：透過實例呼叫
print(obj.instance_method())  # "實例方法，存取 實例值"

# 類別方法：透過類別或實例呼叫
print(MyClass.class_method())  # "類別方法，存取 類別變數"
print(obj.class_method())      # "類別方法，存取 類別變數"

# 靜態方法：透過類別或實例呼叫
print(MyClass.static_method(3, 5))  # "靜態方法，計算 3 + 5 = 8"
print(obj.static_method(3, 5))      # "靜態方法，計算 3 + 5 = 8"
```

### 類別方法的實際應用

```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    def __str__(self):
        return f"{self.year}-{self.month:02d}-{self.day:02d}"

    # 替代建構子（工廠方法）
    @classmethod
    def from_string(cls, date_string):
        """從字串建立 Date 物件"""
        year, month, day = map(int, date_string.split("-"))
        return cls(year, month, day)

    @classmethod
    def today(cls):
        """建立今天的 Date 物件"""
        import datetime
        today = datetime.date.today()
        return cls(today.year, today.month, today.day)

    @classmethod
    def from_timestamp(cls, timestamp):
        """從時間戳建立 Date 物件"""
        import datetime
        dt = datetime.datetime.fromtimestamp(timestamp)
        return cls(dt.year, dt.month, dt.day)

# 使用不同方式建立物件
date1 = Date(2024, 1, 15)
date2 = Date.from_string("2024-06-20")
date3 = Date.today()

print(date1)  # "2024-01-15"
print(date2)  # "2024-06-20"
print(date3)  # 今天的日期
```

### 靜態方法的實際應用

```python
class MathUtils:
    """數學工具類別"""

    @staticmethod
    def is_prime(n):
        """檢查是否為質數"""
        if n < 2:
            return False
        for i in range(2, int(n ** 0.5) + 1):
            if n % i == 0:
                return False
        return True

    @staticmethod
    def gcd(a, b):
        """計算最大公因數"""
        while b:
            a, b = b, a % b
        return a

    @staticmethod
    def lcm(a, b):
        """計算最小公倍數"""
        return abs(a * b) // MathUtils.gcd(a, b)

# 使用
print(MathUtils.is_prime(17))   # True
print(MathUtils.gcd(48, 18))    # 6
print(MathUtils.lcm(4, 6))      # 12
```

### @property 裝飾器

將方法包裝成屬性存取，提供 getter、setter 和 deleter：

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius  # 使用底線表示內部屬性

    @property
    def radius(self):
        """radius 的 getter"""
        return self._radius

    @radius.setter
    def radius(self, value):
        """radius 的 setter"""
        if value < 0:
            raise ValueError("半徑不能為負數")
        self._radius = value

    @radius.deleter
    def radius(self):
        """radius 的 deleter"""
        del self._radius

    @property
    def diameter(self):
        """直徑（唯讀屬性）"""
        return self._radius * 2

    @property
    def area(self):
        """面積（唯讀屬性）"""
        import math
        return math.pi * self._radius ** 2

# 使用
circle = Circle(5)

# 像屬性一樣存取（實際上呼叫 getter）
print(circle.radius)     # 5
print(circle.diameter)   # 10
print(circle.area)       # 78.54...

# 像屬性一樣設定（實際上呼叫 setter）
circle.radius = 10
print(circle.diameter)   # 20

# 設定無效值會觸發 setter 中的驗證
# circle.radius = -5     # ValueError: 半徑不能為負數

# 唯讀屬性不能設定
# circle.diameter = 100  # AttributeError

# JavaScript 對照
# class Circle {
#     get radius() { return this._radius; }
#     set radius(value) { this._radius = value; }
# }
```

### 計算屬性與快取

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
        self._area = None  # 快取

    @property
    def area(self):
        """帶快取的計算屬性"""
        if self._area is None:
            print("計算面積...")
            self._area = self.width * self.height
        return self._area

    def resize(self, width, height):
        """重設大小時清除快取"""
        self.width = width
        self.height = height
        self._area = None  # 清除快取

# 使用 functools.cached_property（Python 3.8+）
from functools import cached_property

class Square:
    def __init__(self, side):
        self.side = side

    @cached_property
    def area(self):
        """只計算一次的屬性"""
        print("計算面積...")
        return self.side ** 2

square = Square(5)
print(square.area)  # 計算面積... 25
print(square.area)  # 25（使用快取，不再印出「計算面積」）
```

---

## 7.2 繼承

### 基本繼承

```python
# 父類別（基礎類別）
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "Some sound"

    def info(self):
        return f"I'm {self.name}"

# 子類別（衍生類別）
class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # 呼叫父類別的 __init__
        self.breed = breed

    def speak(self):  # 覆寫父類別方法
        return "Woof!"

    def fetch(self):  # 新增方法
        return f"{self.name} is fetching!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

# 使用
dog = Dog("Buddy", "Golden Retriever")
cat = Cat("Whiskers")

print(dog.name)      # "Buddy"（繼承的屬性）
print(dog.breed)     # "Golden Retriever"（自己的屬性）
print(dog.speak())   # "Woof!"（覆寫的方法）
print(dog.info())    # "I'm Buddy"（繼承的方法）
print(dog.fetch())   # "Buddy is fetching!"（新增的方法）

print(cat.speak())   # "Meow!"
```

### super() 函式

```python
class Parent:
    def __init__(self, name):
        self.name = name
        print(f"Parent.__init__ called: {name}")

    def greet(self):
        return f"Hello from Parent, I'm {self.name}"

class Child(Parent):
    def __init__(self, name, age):
        # 呼叫父類別的 __init__
        super().__init__(name)
        self.age = age
        print(f"Child.__init__ called: {name}, {age}")

    def greet(self):
        # 呼叫父類別的方法並擴展
        parent_greeting = super().greet()
        return f"{parent_greeting}, and I'm {self.age} years old"

child = Child("Alice", 10)
# Parent.__init__ called: Alice
# Child.__init__ called: Alice, 10

print(child.greet())
# "Hello from Parent, I'm Alice, and I'm 10 years old"
```

### 多層繼承

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        return f"{self.name} is eating"

class Mammal(Animal):
    def __init__(self, name, warm_blooded=True):
        super().__init__(name)
        self.warm_blooded = warm_blooded

    def give_birth(self):
        return f"{self.name} gives birth to live young"

class Dog(Mammal):
    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed

    def bark(self):
        return f"{self.name} says Woof!"

# Dog 繼承了 Animal 和 Mammal 的所有方法
dog = Dog("Buddy", "Labrador")
print(dog.eat())         # 來自 Animal
print(dog.give_birth())  # 來自 Mammal
print(dog.bark())        # 來自 Dog

# 檢查繼承關係
print(isinstance(dog, Dog))      # True
print(isinstance(dog, Mammal))   # True
print(isinstance(dog, Animal))   # True

print(issubclass(Dog, Mammal))   # True
print(issubclass(Dog, Animal))   # True
```

### 多重繼承

Python 支援多重繼承（JavaScript 不支援）：

```python
class Flyable:
    def fly(self):
        return "Flying!"

class Swimmable:
    def swim(self):
        return "Swimming!"

class Walkable:
    def walk(self):
        return "Walking!"

# 多重繼承
class Duck(Flyable, Swimmable, Walkable):
    def __init__(self, name):
        self.name = name

    def quack(self):
        return "Quack!"

duck = Duck("Donald")
print(duck.fly())    # "Flying!"
print(duck.swim())   # "Swimming!"
print(duck.walk())   # "Walking!"
print(duck.quack())  # "Quack!"

# 檢查繼承
print(isinstance(duck, Flyable))    # True
print(isinstance(duck, Swimmable))  # True
```

### 方法解析順序（MRO）

多重繼承時，Python 使用 C3 線性化演算法決定方法搜尋順序：

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

# D 繼承 B 和 C，兩者都繼承 A
# 當呼叫 method() 時，搜尋順序是什麼？

d = D()
print(d.method())  # "B"（先找到 B）

# 查看方法解析順序
print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)

# 或使用 mro() 方法
print(D.mro())

# super() 按照 MRO 順序呼叫
class A:
    def method(self):
        print("A.method")

class B(A):
    def method(self):
        print("B.method")
        super().method()

class C(A):
    def method(self):
        print("C.method")
        super().method()

class D(B, C):
    def method(self):
        print("D.method")
        super().method()

D().method()
# D.method
# B.method
# C.method
# A.method
```

### Mixin 模式

使用多重繼承實現功能組合：

```python
# Mixin 類別：提供特定功能
class JSONMixin:
    """提供 JSON 序列化功能"""
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

    @classmethod
    def from_json(cls, json_str):
        import json
        data = json.loads(json_str)
        return cls(**data)

class ComparableMixin:
    """提供比較功能"""
    def __eq__(self, other):
        return self.__dict__ == other.__dict__

    def __ne__(self, other):
        return not self.__eq__(other)

class PrintableMixin:
    """提供友善的字串表示"""
    def __str__(self):
        attrs = ", ".join(f"{k}={v!r}" for k, v in self.__dict__.items())
        return f"{self.__class__.__name__}({attrs})"

    def __repr__(self):
        return self.__str__()

# 組合使用
class Person(JSONMixin, ComparableMixin, PrintableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

person1 = Person("Alice", 30)
person2 = Person("Alice", 30)
person3 = Person("Bob", 25)

print(person1)                    # Person(name='Alice', age=30)
print(person1.to_json())          # {"name": "Alice", "age": 30}
print(person1 == person2)         # True
print(person1 == person3)         # False

# 從 JSON 還原
json_str = '{"name": "Charlie", "age": 35}'
person4 = Person.from_json(json_str)
print(person4)                    # Person(name='Charlie', age=35)
```

---

## 7.3 存取控制

Python 沒有真正的 private/protected 關鍵字，而是使用命名慣例：

### 命名慣例

```python
class MyClass:
    def __init__(self):
        self.public = "公開"           # 公開屬性
        self._protected = "保護"       # 慣例上的 protected
        self.__private = "私有"        # Name mangling

    def public_method(self):
        """公開方法"""
        return "public method"

    def _protected_method(self):
        """慣例上的 protected 方法"""
        return "protected method"

    def __private_method(self):
        """私有方法（name mangling）"""
        return "private method"

obj = MyClass()

# 公開：正常存取
print(obj.public)           # "公開"
print(obj.public_method())  # "public method"

# Protected：可以存取，但慣例上不應該
print(obj._protected)           # "保護"（可存取但不建議）
print(obj._protected_method())  # "protected method"

# Private：被 name mangling 處理
# print(obj.__private)       # AttributeError!
# print(obj.__private_method())  # AttributeError!

# 但實際上還是可以存取（透過 mangled 名稱）
print(obj._MyClass__private)           # "私有"
print(obj._MyClass__private_method())  # "private method"
```

### 存取控制對照表

| 命名 | 存取範圍 | 說明 |
|------|----------|------|
| `name` | 公開 | 任何地方都可存取 |
| `_name` | Protected（慣例） | 可存取，但慣例上只在類別內部和子類別使用 |
| `__name` | Private（name mangling） | 變成 `_ClassName__name`，較難存取 |
| `__name__` | 特殊方法 | Python 內部使用，如 `__init__`、`__str__` |

### Name Mangling 的用途

```python
class Parent:
    def __init__(self):
        self.__value = "parent"

    def get_value(self):
        return self.__value

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.__value = "child"  # 這是不同的屬性！

    def get_child_value(self):
        return self.__value

child = Child()
print(child.get_value())        # "parent"（來自 Parent.__value）
print(child.get_child_value())  # "child"（來自 Child.__value）

# 查看實際的屬性名稱
print(child.__dict__)
# {'_Parent__value': 'parent', '_Child__value': 'child'}

# Name mangling 主要用於避免子類別意外覆蓋父類別的屬性
```

### 使用 @property 實現封裝

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self._owner = owner
        self._balance = balance
        self._transactions = []

    @property
    def owner(self):
        """唯讀屬性"""
        return self._owner

    @property
    def balance(self):
        """唯讀屬性"""
        return self._balance

    @property
    def transactions(self):
        """回傳交易記錄的複本，防止外部修改"""
        return self._transactions.copy()

    def deposit(self, amount):
        """存款"""
        if amount <= 0:
            raise ValueError("存款金額必須為正數")
        self._balance += amount
        self._transactions.append(f"存款: +{amount}")

    def withdraw(self, amount):
        """提款"""
        if amount <= 0:
            raise ValueError("提款金額必須為正數")
        if amount > self._balance:
            raise ValueError("餘額不足")
        self._balance -= amount
        self._transactions.append(f"提款: -{amount}")

# 使用
account = BankAccount("Alice", 1000)
account.deposit(500)
account.withdraw(200)

print(account.owner)    # "Alice"
print(account.balance)  # 1300

# 無法直接修改
# account.balance = 9999  # AttributeError
# account._balance = 9999  # 技術上可以，但違反慣例
```

### __slots__ 限制屬性

```python
class Point:
    __slots__ = ['x', 'y']  # 只允許這些屬性

    def __init__(self, x, y):
        self.x = x
        self.y = y

point = Point(10, 20)
print(point.x)  # 10

# 無法新增其他屬性
# point.z = 30  # AttributeError: 'Point' object has no attribute 'z'

# __slots__ 的優點：
# 1. 節省記憶體（不需要 __dict__）
# 2. 存取速度更快
# 3. 防止意外新增屬性

# 注意：使用 __slots__ 後沒有 __dict__
# print(point.__dict__)  # AttributeError
```

---

## 7.4 特殊方法（Magic Methods）

特殊方法（也稱為 Dunder Methods）讓類別可以與 Python 的內建操作整合。

### 字串表示

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        """給使用者看的字串（print、str()）"""
        return f"{self.name}, {self.age} 歲"

    def __repr__(self):
        """給開發者看的字串（除錯、互動式環境）"""
        return f"Person(name={self.name!r}, age={self.age!r})"

person = Person("Alice", 30)

print(person)         # "Alice, 30 歲"（使用 __str__）
print(str(person))    # "Alice, 30 歲"
print(repr(person))   # "Person(name='Alice', age=30)"

# 在列表中會使用 __repr__
people = [Person("Alice", 30), Person("Bob", 25)]
print(people)  # [Person(name='Alice', age=30), Person(name='Bob', age=25)]

# JavaScript 對照
# toString() 類似 __str__
```

### 比較運算子

```python
class Money:
    def __init__(self, amount, currency="TWD"):
        self.amount = amount
        self.currency = currency

    def __eq__(self, other):
        """等於 =="""
        if not isinstance(other, Money):
            return NotImplemented
        return self.amount == other.amount and self.currency == other.currency

    def __ne__(self, other):
        """不等於 !="""
        result = self.__eq__(other)
        if result is NotImplemented:
            return result
        return not result

    def __lt__(self, other):
        """小於 <"""
        if not isinstance(other, Money) or self.currency != other.currency:
            return NotImplemented
        return self.amount < other.amount

    def __le__(self, other):
        """小於等於 <="""
        return self == other or self < other

    def __gt__(self, other):
        """大於 >"""
        if not isinstance(other, Money) or self.currency != other.currency:
            return NotImplemented
        return self.amount > other.amount

    def __ge__(self, other):
        """大於等於 >="""
        return self == other or self > other

    def __repr__(self):
        return f"Money({self.amount}, {self.currency!r})"

# 使用
m1 = Money(100)
m2 = Money(100)
m3 = Money(200)

print(m1 == m2)  # True
print(m1 < m3)   # True
print(m3 > m1)   # True

# 使用 functools.total_ordering 簡化
from functools import total_ordering

@total_ordering
class Money:
    def __init__(self, amount, currency="TWD"):
        self.amount = amount
        self.currency = currency

    def __eq__(self, other):
        if not isinstance(other, Money):
            return NotImplemented
        return self.amount == other.amount and self.currency == other.currency

    def __lt__(self, other):
        if not isinstance(other, Money) or self.currency != other.currency:
            return NotImplemented
        return self.amount < other.amount

    # @total_ordering 會自動生成 __le__, __gt__, __ge__
```

### 算術運算子

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        """加法 +"""
        if isinstance(other, Vector):
            return Vector(self.x + other.x, self.y + other.y)
        return NotImplemented

    def __sub__(self, other):
        """減法 -"""
        if isinstance(other, Vector):
            return Vector(self.x - other.x, self.y - other.y)
        return NotImplemented

    def __mul__(self, scalar):
        """乘法 *（向量 * 數字）"""
        if isinstance(scalar, (int, float)):
            return Vector(self.x * scalar, self.y * scalar)
        return NotImplemented

    def __rmul__(self, scalar):
        """反向乘法（數字 * 向量）"""
        return self.__mul__(scalar)

    def __truediv__(self, scalar):
        """除法 /"""
        if isinstance(scalar, (int, float)):
            return Vector(self.x / scalar, self.y / scalar)
        return NotImplemented

    def __neg__(self):
        """負號 -v"""
        return Vector(-self.x, -self.y)

    def __abs__(self):
        """絕對值 abs(v)"""
        return (self.x ** 2 + self.y ** 2) ** 0.5

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

# 使用
v1 = Vector(3, 4)
v2 = Vector(1, 2)

print(v1 + v2)      # Vector(4, 6)
print(v1 - v2)      # Vector(2, 2)
print(v1 * 2)       # Vector(6, 8)
print(3 * v1)       # Vector(9, 12)（使用 __rmul__）
print(v1 / 2)       # Vector(1.5, 2.0)
print(-v1)          # Vector(-3, -4)
print(abs(v1))      # 5.0
```

### 容器方法

```python
class Playlist:
    def __init__(self, name):
        self.name = name
        self._songs = []

    def __len__(self):
        """len(playlist)"""
        return len(self._songs)

    def __getitem__(self, index):
        """playlist[index] 或 playlist[start:end]"""
        return self._songs[index]

    def __setitem__(self, index, value):
        """playlist[index] = value"""
        self._songs[index] = value

    def __delitem__(self, index):
        """del playlist[index]"""
        del self._songs[index]

    def __contains__(self, item):
        """item in playlist"""
        return item in self._songs

    def __iter__(self):
        """for song in playlist"""
        return iter(self._songs)

    def __reversed__(self):
        """reversed(playlist)"""
        return reversed(self._songs)

    def add(self, song):
        self._songs.append(song)

    def __repr__(self):
        return f"Playlist({self.name!r}, {len(self)} songs)"

# 使用
playlist = Playlist("My Favorites")
playlist.add("Song A")
playlist.add("Song B")
playlist.add("Song C")

print(len(playlist))           # 3
print(playlist[0])             # "Song A"
print(playlist[1:])            # ["Song B", "Song C"]
print("Song A" in playlist)    # True

playlist[0] = "Song A (Remix)"
print(playlist[0])             # "Song A (Remix)"

for song in playlist:
    print(song)

for song in reversed(playlist):
    print(song)
```

### 可呼叫物件

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        """讓實例可以像函式一樣呼叫"""
        return value * self.factor

# 使用
double = Multiplier(2)
triple = Multiplier(3)

print(double(5))   # 10
print(triple(5))   # 15

# 檢查是否可呼叫
print(callable(double))  # True

# 實際應用：帶狀態的函式
class Counter:
    def __init__(self):
        self.count = 0

    def __call__(self):
        self.count += 1
        return self.count

counter = Counter()
print(counter())  # 1
print(counter())  # 2
print(counter())  # 3

# 實際應用：裝飾器類別
class Timer:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        import time
        start = time.time()
        result = self.func(*args, **kwargs)
        end = time.time()
        print(f"{self.func.__name__} 執行時間: {end - start:.4f} 秒")
        return result

@Timer
def slow_function():
    import time
    time.sleep(1)
    return "完成"

slow_function()  # slow_function 執行時間: 1.00xx 秒
```

### Context Manager（with 語句）

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        """進入 with 區塊時呼叫"""
        print(f"開啟檔案: {self.filename}")
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        """離開 with 區塊時呼叫"""
        print(f"關閉檔案: {self.filename}")
        if self.file:
            self.file.close()
        # 回傳 False 或 None 會繼續傳播例外
        # 回傳 True 會抑制例外
        return False

# 使用
with FileManager("test.txt", "w") as f:
    f.write("Hello, World!")
# 開啟檔案: test.txt
# 關閉檔案: test.txt

# 實際應用：資料庫連線
class DatabaseConnection:
    def __init__(self, connection_string):
        self.connection_string = connection_string
        self.connection = None

    def __enter__(self):
        print(f"連接資料庫...")
        # self.connection = connect(self.connection_string)
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("關閉資料庫連線")
        # if self.connection:
        #     self.connection.close()
        return False

    def query(self, sql):
        print(f"執行查詢: {sql}")

with DatabaseConnection("localhost:5432") as db:
    db.query("SELECT * FROM users")
```

### 特殊方法總覽

| 方法 | 觸發方式 | 說明 |
|------|----------|------|
| `__init__` | `ClassName()` | 初始化 |
| `__del__` | 物件被刪除時 | 解構子 |
| `__str__` | `str()`, `print()` | 使用者友善字串 |
| `__repr__` | `repr()` | 開發者字串 |
| `__len__` | `len()` | 長度 |
| `__getitem__` | `obj[key]` | 取得項目 |
| `__setitem__` | `obj[key] = value` | 設定項目 |
| `__delitem__` | `del obj[key]` | 刪除項目 |
| `__contains__` | `in` | 成員檢查 |
| `__iter__` | `iter()`, `for` | 迭代 |
| `__next__` | `next()` | 下一個元素 |
| `__call__` | `obj()` | 呼叫物件 |
| `__enter__` | `with` | 進入上下文 |
| `__exit__` | `with` | 離開上下文 |
| `__eq__` | `==` | 相等 |
| `__lt__` | `<` | 小於 |
| `__add__` | `+` | 加法 |
| `__sub__` | `-` | 減法 |
| `__mul__` | `*` | 乘法 |
| `__truediv__` | `/` | 除法 |
| `__hash__` | `hash()` | 雜湊值 |
| `__bool__` | `bool()` | 布林轉換 |

---

## 7.5 抽象基礎類別（ABC）

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    """抽象基礎類別"""

    @abstractmethod
    def area(self):
        """子類別必須實作"""
        pass

    @abstractmethod
    def perimeter(self):
        """子類別必須實作"""
        pass

    def description(self):
        """一般方法（可選擇覆寫）"""
        return f"這是一個 {self.__class__.__name__}"

# 不能直接實例化抽象類別
# shape = Shape()  # TypeError: Can't instantiate abstract class

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        import math
        return math.pi * self.radius ** 2

    def perimeter(self):
        import math
        return 2 * math.pi * self.radius

# 使用
rect = Rectangle(10, 5)
circle = Circle(7)

print(rect.area())          # 50
print(rect.perimeter())     # 30
print(rect.description())   # "這是一個 Rectangle"

print(circle.area())        # 153.94...
print(circle.perimeter())   # 43.98...
```

---

## 練習題

### 練習 1：銀行帳戶類別

建立一個 `BankAccount` 類別，具有以下功能：

```python
class BankAccount:
    # 你的程式碼
    pass

# 預期使用方式
account = BankAccount("Alice", 1000)
account.deposit(500)
account.withdraw(200)
print(account.balance)      # 1300
print(account.owner)        # "Alice"
print(account)              # "BankAccount(owner='Alice', balance=1300)"
```

### 練習 2：自訂串列類別

建立一個 `MyList` 類別，支援以下操作：

```python
class MyList:
    # 你的程式碼
    pass

# 預期使用方式
my_list = MyList([1, 2, 3])
print(len(my_list))         # 3
print(my_list[0])           # 1
print(2 in my_list)         # True
my_list.append(4)
print(list(my_list))        # [1, 2, 3, 4]

my_list2 = MyList([5, 6])
combined = my_list + my_list2
print(list(combined))       # [1, 2, 3, 4, 5, 6]
```

### 練習 3：形狀繼承

建立形狀類別階層：

```python
# Shape (抽象類別) -> Rectangle, Circle, Triangle
# 每個形狀都要有 area() 和 perimeter() 方法
```

---

## 小結

### JavaScript vs Python 類別對照

| 特性 | JavaScript | Python |
|------|------------|--------|
| 類別定義 | `class Name {}` | `class Name:` |
| 建構子 | `constructor()` | `__init__(self)` |
| 實例參考 | `this` | `self` |
| 建立實例 | `new Class()` | `Class()` |
| 繼承 | `extends` | `class Child(Parent):` |
| 呼叫父類別 | `super.method()` | `super().method()` |
| 靜態方法 | `static method()` | `@staticmethod` |
| Getter/Setter | `get/set` | `@property` |
| 私有 | `#field` | `__field`（name mangling） |

### 存取修飾符

| Python | 說明 | JavaScript 對應 |
|--------|------|-----------------|
| `name` | 公開 | `name` |
| `_name` | Protected（慣例） | 無直接對應 |
| `__name` | Private（name mangling） | `#name` |

### 常用特殊方法

| 類別 | 方法 |
|------|------|
| 基本 | `__init__`, `__str__`, `__repr__` |
| 比較 | `__eq__`, `__lt__`, `__gt__`, `__le__`, `__ge__` |
| 算術 | `__add__`, `__sub__`, `__mul__`, `__truediv__` |
| 容器 | `__len__`, `__getitem__`, `__setitem__`, `__contains__`, `__iter__` |
| 其他 | `__call__`, `__enter__`, `__exit__`, `__hash__` |
