# JavaScript 工程師的 Python 學習指南

專為 JavaScript/TypeScript 開發者設計的 Python 學習教材，透過對比兩種語言的差異，幫助你快速掌握 Python。

## 專案特色

- **雙語對照**：每個概念都提供 JavaScript 和 Python 的對比範例
- **互動式學習**：提供 Jupyter Notebook 讓你邊學邊練
- **完整覆蓋**：從基礎語法到進階主題，共 17 章完整內容
- **正體中文**：全中文說明，降低學習門檻

## 目錄結構

```txt
js-to-python/
├── docs/           # Markdown 文件（完整說明）
├── notebooks/      # Jupyter Notebooks（互動式練習）
└── README.md
```

## 章節總覽

| 章節 | 主題           | 文件                                                                         | Notebook                                                                                     |
| ---- | -------------- | ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| 01   | 基本語法       | [docs/01-basic-syntax.md](docs/01-basic-syntax.md)                           | [notebooks/01-basic-syntax.ipynb](notebooks/01-basic-syntax.ipynb)                           |
| 02   | 資料型別       | [docs/02-data-types.md](docs/02-data-types.md)                               | [notebooks/02-data-types.ipynb](notebooks/02-data-types.ipynb)                               |
| 03   | 資料結構       | [docs/03-data-structures.md](docs/03-data-structures.md)                     | [notebooks/03-data-structures.ipynb](notebooks/03-data-structures.ipynb)                     |
| 04   | 運算子         | [docs/04-operators.md](docs/04-operators.md)                                 | [notebooks/04-operators.ipynb](notebooks/04-operators.ipynb)                                 |
| 05   | 流程控制       | [docs/05-control-flow.md](docs/05-control-flow.md)                           | [notebooks/05-control-flow.ipynb](notebooks/05-control-flow.ipynb)                           |
| 06   | 函式           | [docs/06-functions.md](docs/06-functions.md)                                 | [notebooks/06-functions.ipynb](notebooks/06-functions.ipynb)                                 |
| 07   | 類別與物件導向 | [docs/07-classes.md](docs/07-classes.md)                                     | [notebooks/07-classes.ipynb](notebooks/07-classes.ipynb)                                     |
| 08   | 模組與套件     | [docs/08-modules.md](docs/08-modules.md)                                     | [notebooks/08-modules.ipynb](notebooks/08-modules.ipynb)                                     |
| 09   | 錯誤處理       | [docs/09-error-handling.md](docs/09-error-handling.md)                       | [notebooks/09-error-handling.ipynb](notebooks/09-error-handling.ipynb)                       |
| 10   | 非同步程式設計 | [docs/10-async.md](docs/10-async.md)                                         | [notebooks/10-async.ipynb](notebooks/10-async.ipynb)                                         |
| 11   | 推導式與生成器 | [docs/11-comprehensions-generators.md](docs/11-comprehensions-generators.md) | [notebooks/11-comprehensions-generators.ipynb](notebooks/11-comprehensions-generators.ipynb) |
| 12   | 裝飾器         | [docs/12-decorators.md](docs/12-decorators.md)                               | [notebooks/12-decorators.ipynb](notebooks/12-decorators.ipynb)                               |
| 13   | 型別提示       | [docs/13-type-hints.md](docs/13-type-hints.md)                               | [notebooks/13-type-hints.ipynb](notebooks/13-type-hints.ipynb)                               |
| 14   | 工具與生態系統 | [docs/14-tools-ecosystem.md](docs/14-tools-ecosystem.md)                     | [notebooks/14-tools-ecosystem.ipynb](notebooks/14-tools-ecosystem.ipynb)                     |
| 15   | 慣例與風格     | [docs/15-conventions-style.md](docs/15-conventions-style.md)                 | [notebooks/15-conventions-style.ipynb](notebooks/15-conventions-style.ipynb)                 |
| 16   | 實用技巧       | [docs/16-practical-tips.md](docs/16-practical-tips.md)                       | [notebooks/16-practical-tips.ipynb](notebooks/16-practical-tips.ipynb)                       |
| 17   | 學習資源       | [docs/17-learning-resources.md](docs/17-learning-resources.md)               | [notebooks/17-learning-resources.ipynb](notebooks/17-learning-resources.ipynb)               |

## 快速開始

### 環境需求

- Python 3.9+
- Jupyter Notebook 或 JupyterLab

### 安裝步驟

```bash
# 建立虛擬環境
python -m venv venv
source venv/bin/activate  # macOS/Linux
# 或 venv\Scripts\activate  # Windows

# 安裝 Jupyter
pip install jupyter

# 啟動 Jupyter
jupyter notebook
```

### 使用方式

1. **閱讀文件**：在 `docs/` 目錄中閱讀 Markdown 文件，了解完整概念
2. **動手練習**：開啟 `notebooks/` 中對應的 Jupyter Notebook，執行範例程式碼
3. **完成練習**：每個 Notebook 都包含練習題和參考答案

## 學習建議

### 建議學習順序

**第一週：基礎語法**

- 第 1-5 章：基本語法、資料型別、資料結構、運算子、流程控制

**第二週：核心概念**

- 第 6-9 章：函式、類別、模組、錯誤處理

**第三週：進階特性**

- 第 10-13 章：非同步、推導式、裝飾器、型別提示

**第四週：實踐應用**

- 第 14-17 章：工具生態、慣例風格、實用技巧、學習資源

### JavaScript vs Python 快速對照

| 概念      | JavaScript            | Python              |
| --------- | --------------------- | ------------------- |
| 變數宣告  | `let x = 1`           | `x = 1`             |
| 常數      | `const X = 1`         | `X = 1`（慣例大寫） |
| 字串模板  | `` `Hello ${name}` `` | `f"Hello {name}"`   |
| 陣列/列表 | `[1, 2, 3]`           | `[1, 2, 3]`         |
| 物件/字典 | `{a: 1, b: 2}`        | `{"a": 1, "b": 2}`  |
| 箭頭函式  | `(x) => x * 2`        | `lambda x: x * 2`   |
| 類別      | `class Foo {}`        | `class Foo:`        |
| 匯入      | `import x from 'y'`   | `from y import x`   |
| 非同步    | `async/await`         | `async/await`       |
| 套件管理  | npm/yarn              | pip/poetry/uv       |

### 命名慣例差異

| 類型      | JavaScript   | Python       |
| --------- | ------------ | ------------ |
| 變數/函式 | `camelCase`  | `snake_case` |
| 類別      | `PascalCase` | `PascalCase` |
| 常數      | `UPPER_CASE` | `UPPER_CASE` |
| 私有成員  | `_private`   | `_private`   |

## 推薦工具

### 開發環境

- **VS Code** + Python 擴充套件
- **PyCharm** Community/Professional

### 必備套件

```bash
pip install ruff      # Linter + Formatter
pip install mypy      # 型別檢查
pip install pytest    # 測試框架
pip install ipython   # 互動式 Shell
```

### 推薦配置（VS Code）

```json
{
  "python.defaultInterpreterPath": "./venv/bin/python",
  "editor.formatOnSave": true,
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

## 延伸資源

### 官方文件

- [Python 官方文件](https://docs.python.org/3/)
- [Python 教學](https://docs.python.org/3/tutorial/)

### 推薦書籍

- **Fluent Python** - Luciano Ramalho
- **Effective Python** - Brett Slatkin

### 線上學習

- [Real Python](https://realpython.com/)
- [Python Module of the Week](https://pymotw.com/3/)

### 練習平台

- [LeetCode](https://leetcode.com/)
- [Exercism Python Track](https://exercism.org/tracks/python)

## 授權

MIT License
