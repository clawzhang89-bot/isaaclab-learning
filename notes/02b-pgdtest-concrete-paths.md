# Isaac Lab 学习手册补充：结合 `pgdtest` 项目的真实路径说明

> 这份补充文档基于你 Ubuntu 开发机上的真实项目结构整理。目的不是增加新概念，而是把前面文档里的占位符换成你当前项目的真实例子，避免看手册时脑内再做一次路径映射。

---

## 0. 你当前项目的真实路径

我已经在你的 Ubuntu 开发机上确认到：

| 项目 | 实际路径 |
|---|---|
| 项目根目录 | `/home/evan/playground/pgdtest` |
| scripts 目录 | `/home/evan/playground/pgdtest/scripts` |
| package / extension 目录 | `/home/evan/playground/pgdtest/source/pgdtest` |

已确认的目录结构是：

```bash
/home/evan/playground/pgdtest
├── scripts
│   └── skrl
└── source
    └── pgdtest
```

---

## 1. 所以前面文档里的 `source/<project-name>`，在你这里具体指什么

前面 Isaac Lab 文档里的占位符：

```bash
source/<project-name>
```

在你这里，应该直接替换成：

```bash
source/pgdtest
```

如果写成绝对路径，就是：

```bash
/home/evan/playground/pgdtest/source/pgdtest
```

---

## 2. 你当前项目里，“项目根目录”和“可安装 package 路径”不是一回事

### 项目根目录

```bash
/home/evan/playground/pgdtest
```

这是整个项目 repo 的根。

### package / extension 路径

```bash
/home/evan/playground/pgdtest/source/pgdtest
```

这是真正对应文档里 `source/<project-name>` 的地方。

所以后面看到像这样的文档命令：

```bash
python -m pip install -e source/<project-name>
```

你脑内应该翻译成：

```bash
python -m pip install -e source/pgdtest
```

而不是：

```bash
python -m pip install -e /home/evan/playground/pgdtest
```

---

## 3. 为什么是 `source/pgdtest`，不是项目根目录

根据 Isaac Lab 的 `project_structure.rst`：

- Project 是整个项目根目录
- `source/<project-name>` 才是那个被 pip / Python 识别的 package / extension 根
- task 的 `entry_point` 也是通过 Python module path 找它

所以这里的核心不是“文件在哪”，而是：

**哪个目录是 Python 能 import 到的 package 根。**

在你这里，就是：

```bash
source/pgdtest
```

---

## 4. 你现在最应该怎么理解这个项目结构

### 第一层：项目容器

```bash
/home/evan/playground/pgdtest
```

这里放的是：
- 项目整体结构
- scripts
- source
- 可能还有 README、配置文件等

### 第二层：真正被安装的包

```bash
/home/evan/playground/pgdtest/source/pgdtest
```

这里放的是：
- 你的 task 模块
- `__init__.py`
- task 注册逻辑
- env / cfg / agents 等实现

---

## 5. 你当前项目里，下一步看目录时应该重点看哪几个位置

| 路径 | 为什么看它 |
|---|---|
| `/home/evan/playground/pgdtest/source/pgdtest` | 核心 package 入口 |
| `/home/evan/playground/pgdtest/source/pgdtest/**/__init__.py` | 看 `gym.register(...)` |
| `/home/evan/playground/pgdtest/scripts/skrl` | 看训练脚本是怎么调用 task 的 |

---

## 6. 现在回头看前面手册里的命令，应该怎么替换

### 文档写法

```bash
python -m pip install -e source/<project-name>
```

### 你的项目对应写法

```bash
python -m pip install -e source/pgdtest
```

但要注意：

你当前不是裸跑 python，而是主要通过 `isaaclab.sh` 使用 Isaac Lab 环境。

所以这里更稳的理解应该是：

> 需要把 `source/pgdtest` 这个 package 安装进 Isaac Lab 当前使用的 Python 环境里。

命令层面后面最好再结合你实际环境确认，不要先机械抄写。

---

## 7. 你当前最实用的结论

| 问题 | 结论 |
|---|---|
| 项目根目录是哪 | `/home/evan/playground/pgdtest` |
| 文档里的 `source/<project-name>` 在你这里是哪 | `source/pgdtest` |
| 对应绝对路径是哪 | `/home/evan/playground/pgdtest/source/pgdtest` |
| 后面再看文档时 `<project-name>` 要替换成什么 | `pgdtest` |

---

## 8. 一句话总结

以后在你这个项目里，看到 Isaac Lab 文档中的：

```bash
source/<project-name>
```

就直接替换成：

```bash
source/pgdtest
```

如果要写绝对路径，就用：

```bash
/home/evan/playground/pgdtest/source/pgdtest
```

