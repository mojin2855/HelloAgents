# Poetry 介绍与使用指南

## 什么是 Poetry？

Poetry 是 Python 生态中现代化的**依赖管理**与**项目打包**工具。它统一了 `pip` + `virtualenv` + `setup.py` 等传统工具链，使用一个 `pyproject.toml` 文件管理项目所有配置。

### 对比传统工具

| 传统方式 | Poetry |
|---------|--------|
| `pip install` + `pip freeze > requirements.txt` | `poetry add`（自动更新 `pyproject.toml` + 锁文件） |
| 手动 `python -m venv venv` + `source venv/bin/activate` | `poetry shell`（自动创建/激活虚拟环境） |
| `requirements.txt`（无依赖解析，版本冲突常见） | `poetry.lock`（确定性依赖解析，可复现） |
| 手动编写 `setup.py` / `setup.cfg` | `pyproject.toml` 一站式配置 |

---

## 安装

### macOS / Linux

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

### 验证安装

```bash
poetry --version
# 输出示例: Poetry (version 2.3.2)
```

### Poetry 2.x 注意

Poetry 2.0+ 不再内置 `shell` 命令，需单独安装插件：

```bash
poetry self add poetry-plugin-shell
```

安装后即可使用 `poetry shell` 进入虚拟环境。

---

## 核心概念

```
pyproject.toml    →  你手动编辑的依赖声明（"我需要 openai>=1.0"）
poetry.lock       →  Poetry 自动生成的精确版本锁定（"openai==2.43.0 + 它的60个传递依赖的精确版本"）
```

- **`pyproject.toml`**：声明项目元信息 + 依赖范围，可以手动编辑
- **`poetry.lock`**：锁定所有依赖（包括传递依赖）的精确版本，确保任何机器上安装的结果完全一致。**不要手动编辑**

---

## 每日必用命令

### 1. 创建/克隆项目后

```bash
# 安装所有依赖（含开发依赖）
poetry install

# 仅安装生产依赖（跳过 pytest/black 等）
poetry install --only main
```

### 2. 管理依赖

```bash
# 添加生产依赖
poetry add requests

# 添加开发依赖（只在本地开发时需要）
poetry add --group dev pytest black ruff

# 移除依赖
poetry remove requests

# 查看已安装的依赖树
poetry show --tree

# 查看哪些依赖有更新
poetry show --outdated

# 更新依赖到兼容的最新版本
poetry update
```

### 3. 运行代码

```bash
# 进入虚拟环境（推荐）
poetry shell
python chapters/chapter4/hello_agent.py

# 或一行命令（不进入 shell）
poetry run python chapters/chapter4/hello_agent.py

# 运行测试
poetry run pytest

# 格式化代码
poetry run black .
```

### 4. 退出虚拟环境

```bash
exit   # 或按 Ctrl+D
```

---

## 本项目已配置的依赖

### 生产依赖（`poetry install` 会安装）

| 包名 | 用途 |
|------|------|
| `openai` | OpenAI API 客户端，调用 GPT/DeepSeek 等模型 |
| `requests` | HTTP 请求库，调用各类 API |
| `tavily-python` | Tavily 搜索 API，为 Agent 提供联网检索能力 |
| `python-dotenv` | 从 `.env` 文件读取 API Key 等敏感配置 |

### 开发依赖（`poetry install --with dev` 会额外安装）

| 包名 | 用途 |
|------|------|
| `pytest` | 单元测试框架 |
| `black` | 代码格式化工具 |
| `ruff` | 代码检查（lint），替代 flake8 |
| `ipykernel` | 在 Jupyter Notebook 中使用此虚拟环境 |

---

## 典型工作流

```bash
# 1. 克隆项目后，安装所有依赖
poetry install

# 2. 进入虚拟环境
poetry shell

# 3. 编辑 .env 文件，填入真实 Key

# 4. 开始写代码 / 运行代码
python chapters/chapter4/hello_agent.py

# 5. 学习过程中需要新依赖时
poetry add 新包名

# 6. 提交代码时，记得提交 pyproject.toml 和 poetry.lock
git add pyproject.toml poetry.lock
git commit -m "feat: 添加 xxx 依赖"
```

---

## 常见问题

### Q: 虚拟环境在哪里？

```bash
# 查看虚拟环境路径
poetry env info --path
# 通常在 ~/Library/Caches/pypoetry/virtualenvs/ 下
```

### Q: 如何删除并重建虚拟环境？

```bash
poetry env remove --all   # 删除所有虚拟环境
poetry install            # 重新创建并安装
```

### Q: 报错 "command not found: poetry"？

确保 Poetry 的 bin 目录在 `PATH` 中：

```bash
# zsh 用户
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Q: `poetry.lock` 要不要提交到 Git？

**要。** `poetry.lock` 记录的是精确依赖版本，提交后其他协作者或 CI 环境执行 `poetry install` 能获得和你完全一致的环境。

### Q: 和 `pip + requirements.txt` 比，Poetry 优势在哪？

Poetry 的依赖解析器会检查所有包的版本兼容性，避免"装了 A 的 v2.0 但它要求 B < 3.0 而你已经装了 B v4.0"这类版本冲突。`requirements.txt` 不会做这种检查。

---

## 延伸阅读

- [Poetry 官方文档](https://python-poetry.org/docs/)
- [pyproject.toml 规范 (PEP 621)](https://peps.python.org/pep-0621/)
