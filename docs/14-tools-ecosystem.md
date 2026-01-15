# 第 14 章：常用工具與生態系

## 概述

對於熟悉 Node.js 生態系的 JavaScript 開發者來說，Python 的工具鏈會有些不同但概念相似。本章將介紹 Python 的套件管理、虛擬環境、開發工具以及常用的第三方套件。

### JavaScript vs Python 生態系對照

| 領域         | JavaScript/Node.js | Python                           |
| ------------ | ------------------ | -------------------------------- |
| 套件管理器   | npm, yarn, pnpm    | pip, poetry, pipenv, uv          |
| 套件倉庫     | npmjs.com          | pypi.org                         |
| 設定檔       | package.json       | pyproject.toml, requirements.txt |
| 依賴隔離     | node_modules       | 虛擬環境（venv）                 |
| 執行環境     | Node.js            | CPython, PyPy                    |
| 版本管理     | nvm, fnm           | pyenv                            |
| 執行套件指令 | npx                | pipx                             |

---

## 14.1 套件管理

### pip - 官方套件管理器

pip 是 Python 官方的套件管理器，類似於 npm。

**基本操作：**

```bash
# 安裝套件
pip install requests

# 安裝特定版本
pip install requests==2.28.0

# 安裝最新版本（在範圍內）
pip install "requests>=2.25.0,<3.0.0"

# 升級套件
pip install --upgrade requests

# 解除安裝
pip uninstall requests

# 列出已安裝套件
pip list

# 查看套件資訊
pip show requests

# 搜尋套件（已棄用，建議直接在 pypi.org 搜尋）
# pip search requests
```

**requirements.txt - 依賴清單：**

```bash
# 匯出目前環境的所有套件
pip freeze > requirements.txt

# 從 requirements.txt 安裝
pip install -r requirements.txt
```

requirements.txt 範例：

```text
# requirements.txt
requests==2.28.0
flask>=2.0.0,<3.0.0
pytest~=7.0  # 相容版本（>=7.0, <8.0）
numpy
pandas>=1.5.0
```

對比 JavaScript：

```bash
# npm
npm install axios
npm install axios@1.4.0
npm install
npm uninstall axios
npm list
```

### poetry - 現代化套件管理

Poetry 是更現代化的套件管理工具，類似於 yarn 或 pnpm，提供更好的依賴管理和專案管理。

**安裝 Poetry：**

```bash
# 官方安裝方式
curl -sSL https://install.python-poetry.org | python3 -

# 或使用 pipx
pipx install poetry
```

**基本操作：**

```bash
# 建立新專案
poetry new my-project

# 在現有專案初始化
poetry init

# 安裝依賴
poetry add requests
poetry add pytest --group dev  # 開發依賴

# 移除依賴
poetry remove requests

# 安裝所有依賴
poetry install

# 更新依賴
poetry update

# 執行指令（在虛擬環境中）
poetry run python main.py
poetry run pytest

# 進入虛擬環境 shell
poetry shell

# 匯出 requirements.txt
poetry export -f requirements.txt --output requirements.txt
```

**pyproject.toml 範例：**

```toml
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = "My awesome project"
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
requests = "^2.28.0"
fastapi = "^0.100.0"
pydantic = "^2.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0"
black = "^23.0"
mypy = "^1.0"
ruff = "^0.1.0"

[tool.poetry.scripts]
start = "my_project.main:main"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

對比 JavaScript package.json：

```json
{
  "name": "my-project",
  "version": "0.1.0",
  "description": "My awesome project",
  "main": "index.js",
  "scripts": {
    "start": "node main.js",
    "test": "jest"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "express": "^4.18.0"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "eslint": "^8.0.0"
  }
}
```

### uv - 極速套件管理器

uv 是 Astral（Ruff 的開發團隊）開發的新一代 Python 套件管理器，用 Rust 編寫，速度極快。

**安裝 uv：**

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# 或使用 pip
pip install uv
```

**基本操作：**

```bash
# 建立虛擬環境
uv venv

# 安裝套件
uv pip install requests
uv pip install -r requirements.txt

# 同步依賴
uv pip sync requirements.txt

# 編譯依賴（鎖定版本）
uv pip compile requirements.in -o requirements.txt

# 執行 Python 腳本（自動處理依賴）
uv run script.py

# 建立新專案
uv init my-project
```

**速度比較：**

| 操作         | pip  | uv    |
| ------------ | ---- | ----- |
| 安裝 Django  | ~10s | ~0.5s |
| 建立虛擬環境 | ~3s  | ~0.1s |
| 解析依賴     | 慢   | 極快  |

### pipx - 全域工具安裝

pipx 用於安裝和執行 Python 應用程式，類似於 npx。

```bash
# 安裝 pipx
pip install pipx
pipx ensurepath

# 安裝全域工具
pipx install black
pipx install poetry
pipx install httpie

# 執行一次性指令（不安裝）
pipx run cowsay "Hello!"

# 升級工具
pipx upgrade black
pipx upgrade-all

# 列出已安裝工具
pipx list
```

對比 JavaScript：

```bash
# npx - 執行套件
npx create-react-app my-app
npx cowsay "Hello!"

# 全域安裝
npm install -g typescript
```

---

## 14.2 虛擬環境

Python 使用虛擬環境來隔離專案依賴，概念類似於 Node.js 的 node_modules，但需要顯式啟用。

### venv - 內建虛擬環境

```bash
# 建立虛擬環境
python -m venv venv

# 建立指定 Python 版本的虛擬環境（需已安裝該版本）
python3.11 -m venv venv

# 啟用虛擬環境
# macOS/Linux
source venv/bin/activate

# Windows (PowerShell)
.\venv\Scripts\Activate.ps1

# Windows (CMD)
venv\Scripts\activate.bat

# 停用虛擬環境
deactivate

# 刪除虛擬環境（直接刪除資料夾）
rm -rf venv
```

**虛擬環境目錄結構：**

```
venv/
├── bin/                    # macOS/Linux 可執行檔
│   ├── activate            # 啟用腳本
│   ├── python -> python3.11
│   ├── pip
│   └── ...
├── include/                # C 標頭檔
├── lib/                    # 安裝的套件
│   └── python3.11/
│       └── site-packages/
│           ├── requests/
│           ├── pip/
│           └── ...
└── pyvenv.cfg              # 設定檔
```

對比 JavaScript：

```
node_modules/
├── axios/
├── express/
├── .bin/                   # 可執行檔連結
└── ...
```

### pyenv - Python 版本管理

pyenv 用於管理多個 Python 版本，類似於 Node.js 的 nvm 或 fnm。

**安裝 pyenv：**

```bash
# macOS（使用 Homebrew）
brew install pyenv

# Linux
curl https://pyenv.run | bash

# 加入 shell 設定（~/.bashrc 或 ~/.zshrc）
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

**基本操作：**

```bash
# 列出可安裝的版本
pyenv install --list

# 安裝特定版本
pyenv install 3.11.5
pyenv install 3.12.0

# 列出已安裝版本
pyenv versions

# 設定全域版本
pyenv global 3.11.5

# 設定專案版本（建立 .python-version 檔案）
pyenv local 3.12.0

# 設定當前 shell 版本
pyenv shell 3.11.5

# 解除安裝
pyenv uninstall 3.10.0
```

對比 JavaScript nvm：

```bash
# nvm
nvm install 20
nvm use 20
nvm alias default 20
nvm ls
```

### 虛擬環境最佳實踐

```bash
# 標準專案設定流程
mkdir my-project && cd my-project

# 1. 指定 Python 版本
pyenv local 3.11.5

# 2. 建立虛擬環境
python -m venv venv

# 3. 啟用
source venv/bin/activate

# 4. 升級 pip
pip install --upgrade pip

# 5. 安裝依賴
pip install -r requirements.txt

# 6. 開始開發...
```

**.gitignore 設定：**

```gitignore
# 虛擬環境
venv/
.venv/
env/
.env/

# Python 快取
__pycache__/
*.py[cod]
*$py.class
.pytest_cache/
.mypy_cache/

# 打包檔案
dist/
build/
*.egg-info/

# IDE
.idea/
.vscode/
*.swp
```

---

## 14.3 程式碼品質工具

### 格式化工具

#### Black - 程式碼格式化

Black 是最流行的 Python 程式碼格式化工具，類似於 Prettier。

```bash
# 安裝
pip install black

# 格式化檔案
black script.py

# 格式化目錄
black src/

# 檢查但不修改
black --check src/

# 顯示差異
black --diff src/

# 指定行長度
black --line-length 120 src/
```

**設定（pyproject.toml）：**

```toml
[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'
exclude = '''
/(
    \.git
  | \.venv
  | build
  | dist
)/
'''
```

#### isort - import 排序

```bash
# 安裝
pip install isort

# 排序
isort script.py
isort src/

# 檢查
isort --check-only src/

# 與 Black 相容
isort --profile black src/
```

**設定（pyproject.toml）：**

```toml
[tool.isort]
profile = "black"
line_length = 88
known_first_party = ["my_project"]
skip = [".venv", "venv"]
```

#### Ruff - 全能工具

Ruff 是用 Rust 編寫的超快速 linter 和格式化工具，可取代 flake8、isort、black 等多個工具。

```bash
# 安裝
pip install ruff

# Linting
ruff check .
ruff check --fix .  # 自動修復

# 格式化（Ruff 0.1.0+）
ruff format .
ruff format --check .
```

**設定（pyproject.toml）：**

```toml
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
    "ARG",    # flake8-unused-arguments
    "SIM",    # flake8-simplify
]
ignore = ["E501"]  # 忽略行長度

[tool.ruff.lint.isort]
known-first-party = ["my_project"]
```

### Linting 工具

#### flake8

```bash
pip install flake8
flake8 src/
```

#### pylint

```bash
pip install pylint
pylint src/
```

### 型別檢查

```bash
# mypy
pip install mypy
mypy src/

# pyright
pip install pyright
pyright src/
```

**整合設定範例（pyproject.toml）：**

```toml
[tool.mypy]
python_version = "3.11"
strict = true
warn_return_any = true
warn_unused_ignores = true
disallow_untyped_defs = true

[[tool.mypy.overrides]]
module = ["requests.*", "flask.*"]
ignore_missing_imports = true
```

對比 JavaScript：

```bash
# ESLint
npm install eslint
npx eslint src/

# Prettier
npm install prettier
npx prettier --write src/

# TypeScript
npm install typescript
npx tsc --noEmit
```

---

## 14.4 測試工具

### pytest - 測試框架

pytest 是 Python 最流行的測試框架，類似於 Jest。

**安裝：**

```bash
pip install pytest pytest-cov pytest-asyncio
```

**基本測試：**

```python
# tests/test_calculator.py
import pytest
from my_project.calculator import add, divide

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0

def test_divide():
    assert divide(10, 2) == 5

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)

# 參數化測試
@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
])
def test_add_parametrized(a, b, expected):
    assert add(a, b) == expected
```

**執行測試：**

```bash
# 執行所有測試
pytest

# 執行特定檔案
pytest tests/test_calculator.py

# 執行特定測試
pytest tests/test_calculator.py::test_add

# 顯示詳細輸出
pytest -v

# 顯示 print 輸出
pytest -s

# 產生覆蓋率報告
pytest --cov=src --cov-report=html

# 平行執行（需安裝 pytest-xdist）
pytest -n auto
```

**pytest 設定（pyproject.toml）：**

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-v --cov=src --cov-report=term-missing"
asyncio_mode = "auto"
```

**Fixtures（類似 Jest 的 beforeEach）：**

```python
import pytest

@pytest.fixture
def sample_data():
    return {"name": "Alice", "age": 30}

@pytest.fixture
def db_connection():
    # Setup
    conn = create_connection()
    yield conn
    # Teardown
    conn.close()

def test_with_fixture(sample_data, db_connection):
    assert sample_data["name"] == "Alice"
    # 使用 db_connection...
```

對比 JavaScript Jest：

```javascript
// Jest
describe("Calculator", () => {
    test("add", () => {
        expect(add(2, 3)).toBe(5);
    });

    test("divide by zero", () => {
        expect(() => divide(10, 0)).toThrow();
    });
});
```

### unittest - 內建測試框架

```python
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        # 每個測試前執行
        pass

    def tearDown(self):
        # 每個測試後執行
        pass

    def test_add(self):
        self.assertEqual(add(2, 3), 5)

    def test_divide_by_zero(self):
        with self.assertRaises(ZeroDivisionError):
            divide(10, 0)

if __name__ == "__main__":
    unittest.main()
```

---

## 14.5 常用第三方套件

### HTTP 請求

#### requests - 同步 HTTP 客戶端

```python
import requests

# GET 請求
response = requests.get("https://api.example.com/users")
print(response.status_code)
print(response.json())

# POST 請求
response = requests.post(
    "https://api.example.com/users",
    json={"name": "Alice", "age": 30},
    headers={"Authorization": "Bearer token"}
)

# 錯誤處理
response.raise_for_status()  # 拋出 HTTP 錯誤

# 超時設定
response = requests.get(url, timeout=10)

# Session（重用連線）
session = requests.Session()
session.headers.update({"Authorization": "Bearer token"})
response = session.get(url)
```

#### httpx - 同步/非同步 HTTP 客戶端

```python
import httpx

# 同步請求
response = httpx.get("https://api.example.com/users")

# 非同步請求
async with httpx.AsyncClient() as client:
    response = await client.get("https://api.example.com/users")
    data = response.json()
```

對比 JavaScript：

```javascript
// fetch
const response = await fetch("https://api.example.com/users");
const data = await response.json();

// axios
const response = await axios.get("https://api.example.com/users");
```

### Web 框架

#### Flask - 輕量級框架

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, World!"

@app.route("/users", methods=["GET", "POST"])
def users():
    if request.method == "POST":
        data = request.json
        return jsonify({"created": data}), 201
    return jsonify([{"name": "Alice"}])

@app.route("/users/<int:user_id>")
def get_user(user_id: int):
    return jsonify({"id": user_id, "name": "Alice"})

if __name__ == "__main__":
    app.run(debug=True)
```

#### FastAPI - 現代化高效能框架

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    age: int

@app.get("/")
async def home():
    return {"message": "Hello, World!"}

@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return {"id": user_id, "name": "Alice"}

@app.post("/users", status_code=201)
async def create_user(user: User):
    return {"created": user.dict()}

# 執行
# uvicorn main:app --reload
```

#### Django - 全功能框架

Django 是 Python 最全面的 Web 框架，內建 ORM、管理後台、認證系統等。

```python
# models.py
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

# views.py
from django.http import JsonResponse
from django.views import View

class UserView(View):
    def get(self, request, user_id):
        user = User.objects.get(id=user_id)
        return JsonResponse({"name": user.name})
```

對比 JavaScript：

```javascript
// Express.js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
    res.json({ message: "Hello, World!" });
});

app.get("/users/:userId", (req, res) => {
    res.json({ id: req.params.userId, name: "Alice" });
});
```

### 資料庫

#### SQLAlchemy - ORM

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import sessionmaker, declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True)
    name = Column(String(100))
    email = Column(String(100), unique=True)

# 建立連線
engine = create_engine("sqlite:///app.db")
Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
session = Session()

# CRUD 操作
# 建立
user = User(name="Alice", email="alice@example.com")
session.add(user)
session.commit()

# 查詢
users = session.query(User).filter(User.name == "Alice").all()
user = session.query(User).get(1)

# 更新
user.name = "Alice Smith"
session.commit()

# 刪除
session.delete(user)
session.commit()
```

### 資料處理

#### pandas - 資料分析

```python
import pandas as pd

# 讀取資料
df = pd.read_csv("data.csv")
df = pd.read_json("data.json")
df = pd.read_excel("data.xlsx")

# 基本操作
print(df.head())           # 前 5 行
print(df.describe())       # 統計摘要
print(df.columns)          # 欄位名稱

# 選擇資料
df["name"]                 # 單一欄位
df[["name", "age"]]        # 多個欄位
df[df["age"] > 30]         # 條件篩選

# 資料處理
df["age_group"] = df["age"].apply(lambda x: "adult" if x >= 18 else "minor")
df_grouped = df.groupby("age_group").mean()

# 匯出
df.to_csv("output.csv", index=False)
df.to_json("output.json", orient="records")
```

#### numpy - 數值計算

```python
import numpy as np

# 建立陣列
arr = np.array([1, 2, 3, 4, 5])
matrix = np.array([[1, 2], [3, 4]])
zeros = np.zeros((3, 3))
ones = np.ones((2, 2))
range_arr = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]

# 運算
arr * 2              # 元素運算
np.sum(arr)          # 總和
np.mean(arr)         # 平均
np.std(arr)          # 標準差
np.dot(matrix, matrix)  # 矩陣乘法
```

### 任務排程與背景工作

#### Celery - 分散式任務佇列

```python
# tasks.py
from celery import Celery

app = Celery("tasks", broker="redis://localhost:6379/0")

@app.task
def send_email(to: str, subject: str, body: str):
    # 發送郵件的邏輯
    print(f"Sending email to {to}")
    return True

# 呼叫任務
result = send_email.delay("user@example.com", "Hello", "Body")
print(result.get())  # 等待結果
```

對比 JavaScript：

```javascript
// Bull (Node.js)
const Queue = require("bull");
const emailQueue = new Queue("email");

emailQueue.process(async (job) => {
    const { to, subject, body } = job.data;
    // 發送郵件
});

emailQueue.add({ to: "user@example.com", subject: "Hello", body: "Body" });
```

### CLI 工具

#### Click - 命令列介面

```python
import click

@click.command()
@click.option("--name", default="World", help="Name to greet")
@click.option("--count", default=1, help="Number of greetings")
def hello(name: str, count: int):
    """Simple program that greets NAME for COUNT times."""
    for _ in range(count):
        click.echo(f"Hello, {name}!")

if __name__ == "__main__":
    hello()
```

```bash
python hello.py --name Alice --count 3
```

#### Typer - 現代化 CLI（基於型別提示）

```python
import typer

app = typer.Typer()

@app.command()
def hello(name: str, count: int = 1):
    """Greet NAME for COUNT times."""
    for _ in range(count):
        typer.echo(f"Hello, {name}!")

@app.command()
def goodbye(name: str):
    """Say goodbye to NAME."""
    typer.echo(f"Goodbye, {name}!")

if __name__ == "__main__":
    app()
```

對比 JavaScript：

```javascript
// Commander.js
const { program } = require("commander");

program
    .option("--name <name>", "Name to greet", "World")
    .option("--count <count>", "Number of greetings", 1)
    .action((options) => {
        for (let i = 0; i < options.count; i++) {
            console.log(`Hello, ${options.name}!`);
        }
    });

program.parse();
```

---

## 14.6 執行環境

### CPython vs PyPy

| 特性         | CPython  | PyPy                 |
| ------------ | -------- | -------------------- |
| 實作語言     | C        | Python (RPython)     |
| 官方/預設    | 是       | 否                   |
| 速度         | 基準     | 快 4-10 倍           |
| 記憶體使用   | 較低     | 較高                 |
| C 擴充相容性 | 完全     | 部分                 |
| 啟動時間     | 快       | 較慢                 |
| 適用場景     | 一般用途 | 長時間運行、CPU 密集 |

**PyPy 安裝與使用：**

```bash
# macOS
brew install pypy3

# 使用 PyPy 執行
pypy3 script.py

# 建立 PyPy 虛擬環境
pypy3 -m venv venv
```

### 與 JavaScript 執行環境對比

| JavaScript | Python                 |
| ---------- | ---------------------- |
| Node.js    | CPython                |
| Deno       | 無直接對應             |
| Bun        | PyPy（效能優化）       |
| 瀏覽器     | Pyodide（WebAssembly） |

### Jupyter Notebook - 互動式開發環境

```bash
pip install jupyter

# 啟動 Jupyter Notebook
jupyter notebook

# 啟動 Jupyter Lab（更現代化的介面）
pip install jupyterlab
jupyter lab
```

**IPython - 互動式 Python Shell：**

```bash
pip install ipython
ipython
```

---

## 14.7 專案結構範例

### 小型專案

```
my-project/
├── pyproject.toml          # 專案設定
├── requirements.txt        # 依賴清單（可選）
├── README.md
├── .gitignore
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── main.py
│       └── utils.py
└── tests/
    ├── __init__.py
    └── test_main.py
```

### 大型專案

```
my-project/
├── pyproject.toml
├── README.md
├── .gitignore
├── .env.example
├── Makefile                # 常用指令
├── Dockerfile
├── docker-compose.yml
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── main.py
│       ├── config/
│       │   ├── __init__.py
│       │   └── settings.py
│       ├── models/
│       │   ├── __init__.py
│       │   └── user.py
│       ├── services/
│       │   ├── __init__.py
│       │   └── user_service.py
│       ├── api/
│       │   ├── __init__.py
│       │   └── routes.py
│       └── utils/
│           ├── __init__.py
│           └── helpers.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py         # pytest fixtures
│   ├── unit/
│   │   └── test_services.py
│   └── integration/
│       └── test_api.py
├── scripts/
│   └── seed_db.py
└── docs/
    └── api.md
```

**Makefile 範例：**

```makefile
.PHONY: install test lint format run

install:
	pip install -e ".[dev]"

test:
	pytest --cov=src

lint:
	ruff check src tests
	mypy src

format:
	ruff format src tests
	ruff check --fix src tests

run:
	python -m my_project.main
```

對比 JavaScript package.json scripts：

```json
{
  "scripts": {
    "install": "npm install",
    "test": "jest --coverage",
    "lint": "eslint src tests",
    "format": "prettier --write src tests",
    "start": "node src/main.js"
  }
}
```

---

## 練習題

### 練習 1：建立專案環境

建立一個新的 Python 專案，包含虛擬環境和基本依賴：

```bash
# 1. 建立專案目錄
mkdir my-api && cd my-api

# 2. 建立虛擬環境
python -m venv venv
source venv/bin/activate

# 3. 安裝依賴
pip install fastapi uvicorn pytest httpx

# 4. 匯出依賴
pip freeze > requirements.txt
```

### 練習 2：使用 Poetry

將練習 1 的專案轉換為使用 Poetry：

```bash
# 1. 初始化 Poetry
poetry init

# 2. 加入依賴
poetry add fastapi uvicorn
poetry add --group dev pytest httpx

# 3. 安裝並執行
poetry install
poetry run uvicorn main:app --reload
```

### 練習 3：設定程式碼品質工具

在專案中設定 Ruff 和 mypy：

```toml
# pyproject.toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "UP"]

[tool.mypy]
python_version = "3.11"
strict = true
```

```bash
# 執行檢查
ruff check .
ruff format .
mypy src/
```

---

## 總結

### JavaScript vs Python 工具對照表

| 用途        | JavaScript             | Python                 |
| ----------- | ---------------------- | ---------------------- |
| 套件管理    | npm, yarn, pnpm        | pip, poetry, uv        |
| 套件倉庫    | npmjs.com              | pypi.org               |
| 設定檔      | package.json           | pyproject.toml         |
| 版本管理    | nvm, fnm               | pyenv                  |
| 執行套件    | npx                    | pipx                   |
| 依賴隔離    | node_modules           | venv                   |
| 格式化      | Prettier               | Black, Ruff            |
| Linting     | ESLint                 | Ruff, flake8, pylint   |
| 型別檢查    | TypeScript             | mypy, pyright          |
| 測試        | Jest, Vitest           | pytest                 |
| HTTP 客戶端 | fetch, axios           | requests, httpx        |
| Web 框架    | Express, Fastify, Nest | Flask, FastAPI, Django |
| ORM         | Prisma, TypeORM        | SQLAlchemy, Django ORM |
| CLI 工具    | Commander, yargs       | Click, Typer           |
| 任務佇列    | Bull                   | Celery                 |
| 資料處理    | -                      | pandas, numpy          |

### 重點回顧

1. **套件管理**：pip 是基本工具，Poetry 和 uv 提供更現代化的體驗
2. **虛擬環境**：Python 需要顯式建立和啟用虛擬環境
3. **版本管理**：使用 pyenv 管理多個 Python 版本
4. **程式碼品質**：Ruff 是目前最推薦的全能工具
5. **測試**：pytest 是最流行的測試框架
6. **Web 框架**：FastAPI 適合 API 開發，Django 適合全端應用
7. **專案結構**：遵循標準結構，使用 src/ 目錄組織程式碼
