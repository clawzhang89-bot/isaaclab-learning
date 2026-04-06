# Isaac Lab 学习手册 v0.2：新项目生成与第一步操作手册

> 这份文档继续严格基于本地 `IsaacLab` docs 整理，目标是把“从创建新项目开始”这一步写得更可执行。适合你边看边做。

---

## 0. 这份手册主要参考的本地 docs

| 本地文档 | 用途 |
|---|---|
| `docs/source/setup/quickstart.rst` | Quickstart 与项目生成入口 |
| `docs/source/overview/own-project/template.rst` | 模板生成器用法 |
| `docs/source/overview/own-project/project_structure.rst` | 生成后的项目结构 |
| `docs/source/tutorials/03_envs/register_rl_env_gym.rst` | 为什么要 `gym.register(...)` |
| `docs/source/tutorials/03_envs/run_rl_training.rst` | 训练脚本怎么接 task |

---

## 1. 你这一步的目标，不是“马上训练”，而是先打通项目骨架

当前阶段的正确目标应该是：

1. 生成一个 **Isaac Lab 外部项目**
2. 安装它
3. 理解它的目录结构
4. 确认 task 已被注册
5. 确认后续可以往里面塞简单机器人与最小训练任务

如果这一步没打通，后面所有机器人、关节、训练都会乱掉。

---

## 2. 官方文档推荐的方式：用模板生成器

根据 `template.rst`：

官方推荐用 **template generator** 来创建新项目 / 新任务。

### 本地命令

在 `IsaacLab` 仓库根目录运行：

```bash
./isaaclab.sh --new
```

文档说这个命令会引导你做选择，包括：

| 问题 | 你会选什么 |
|---|---|
| External or Internal | 项目类型 |
| Workflow | Direct or Manager |
| RL framework | 训练框架 |

---

## 3. 你现在第一轮建议怎么选

基于你当前的学习目标，我建议这样选：

| 选项 | 建议选择 | 原因 |
|---|---|---|
| Project type | **External project** | `template.rst` 明确说 external 是推荐方式 |
| Workflow | **Direct** | `quickstart.rst` 明确说 Direct 是通向可工作自定义环境的最短路径 |
| RL framework | 先选一个最常见 / 最顺手的 | 当前重点不是比框架，而是先打通骨架 |

### 为什么不是 internal

根据 `template.rst`：
- internal task 更适合给 Isaac Lab 仓库本体贡献任务
- external project 更适合自己的独立学习 / 开发项目

这和你当前目标完全一致。

---

## 4. 生成完成后，文档要求你做什么

根据 `template.rst`：

生成项目后，文档给出的通用下一步是：

### 安装项目（editable mode）

```bash
python -m pip install -e source/<given-project-name>
```

这一步很关键，因为：

- `project_structure.rst` 解释了 task 的 entry point 其实依赖 Python module path
- 也就是说，不安装成 package，Isaac Lab 的 gym entry point 很难正确找到你的环境

你可以把这一步理解成：

**“让 Isaac Lab 能认出你的项目和 task。”**

---

## 5. 生成项目后，第一时间该看哪些目录

根据 `project_structure.rst`，建议你生成后先看这几个位置：

| 位置 | 为什么先看它 |
|---|---|
| 项目根目录 `README.md` | 模板生成后的项目说明 |
| `scripts/` | 后续 train / play / list_envs 从这里走 |
| `source/<project-name>/` | 你自己的主代码区 |
| task 目录 | 环境定义和 config 的主战场 |
| `__init__.py` | 通常包含 `gym.register(...)` |

---

## 6. 这一步最重要的概念：task 不是“文件”，而是“被注册的环境”

根据 `register_rl_env_gym.rst`：

一个环境之所以能被 Isaac Lab 训练脚本使用，是因为它通过 `gymnasium.register(...)` 被注册进了 gym registry。

### 这意味着什么

你后面每次看一个新 task，都要优先确认三件事：

| 项目 | 意义 |
|---|---|
| `id=` | 这个 task 的名字是什么 |
| `entry_point=` | 环境类在哪里 |
| `env_cfg_entry_point=` | 默认配置从哪里来 |

### Direct workflow 的命名特点

根据 `register_rl_env_gym.rst`：

- Direct 环境通常会加 `-Direct` 后缀
- 用来和 manager-based 环境区分

所以如果你后面看到：
- `Isaac-Cartpole-v0`
- `Isaac-Cartpole-Direct-v0`

别混了，它们是不同工作流下的版本。

---

## 7. 为什么你现在先不该急着写复杂机器人

因为在 Isaac Lab 里，生成项目后的第一步不是“写机器人”，而是确认这条链已经通了：

```text
项目生成 → 安装为 package → task 被注册 → 能被脚本找到 → 能运行基础任务
```

如果这一条没通，后面写再多 robot / RL 代码都只是堆风险。

---

## 8. 文档里建议你先怎么验证环境是否可用

根据 `template.rst`：

### 8.1 列出任务

外部项目生成后，可以运行：

```bash
python scripts/list_envs.py
```

目的：
- 看你的项目任务是否真的被注册出来了

### 8.2 跑任务

文档给出的通用形式是：

```bash
python scripts/<specific-rl-library>/train.py --task=<Task-Name>
```

这一步说明：
- 训练脚本不是直接找某个 `.py`
- 它是通过 task name 去找 registry

所以：

**能不能 list 出 task，本质上是第一层健康检查。**

---

## 9. 如果你生成的是 Direct workflow 项目，第一阶段最该检查什么

### 检查 1：项目能安装

成功运行：
```bash
python -m pip install -e source/<project-name>
```

### 检查 2：task 已注册

成功运行：
```bash
python scripts/list_envs.py
```

并且能看到你自己的 task。

### 检查 3：能用 task name 创建环境

根据 `register_rl_env_gym.rst` 的思路：
- 训练 / 随机 agent / play 脚本本质上都会走 `gym.make(...)`

所以这层本质上是在确认：

- `__init__.py` 是否执行了
- `gym.register(...)` 是否写对了
- `entry_point` 和 `env_cfg_entry_point` 是否写对了

---

## 10. 生成项目后，你真正该读的文件顺序

我建议你按这个顺序读：

| 顺序 | 读哪里 | 目的 |
|---:|---|---|
| 1 | 生成项目自带 `README.md` | 看项目怎么安装和运行 |
| 2 | task 对应的 `__init__.py` | 看 `gym.register(...)` |
| 3 | env config 文件 | 看 `DirectRLEnvCfg` 怎么写 |
| 4 | env 实现文件 | 看 `_setup_scene / _get_rewards / _reset_idx ...` |
| 5 | 对应 framework 的 `train.py` | 看 task 怎么被调用 |

---

## 11. 训练脚本这一步，你只需要先理解“怎么接”，不用马上学透算法

根据 `run_rl_training.rst`：

训练脚本核心逻辑是：

1. 创建环境
2. 包上对应 RL framework 的 wrapper
3. 跑训练
4. 记录日志、视频、checkpoint

这里你当前最需要理解的不是 PPO 细节，而是：

| 你要先搞懂什么 | 为什么 |
|---|---|
| task 是怎么被创建的 | 这是你自己后面写 task 的入口 |
| wrapper 是在后面包上的 | 说明 env 本身和 RL 框架是分离的 |
| logs / checkpoints 在哪 | 方便后续调试 |

---

## 12. 你现在第一轮最建议达到的最小成功标准

### 第一层成功

| 标准 | 是否必须 |
|---|---|
| 成功运行 `./isaaclab.sh --new` | 是 |
| 成功生成 external project | 是 |
| 成功安装项目 | 是 |
| 成功 list 出 task | 是 |

### 第二层成功

| 标准 | 是否必须 |
|---|---|
| 能找到 `gym.register(...)` 在哪 | 是 |
| 能说清 env class 和 cfg class 在哪 | 是 |
| 能跑通项目自带 task | 是 |

只要做到这里，你再进“简单机器人 / articulation / RL task”就会顺很多。

---

## 13. 这一阶段最容易犯的错

| 错误 | 为什么错 |
|---|---|
| 还没看懂项目结构就改 env | 很容易改乱 entry point |
| 还没确认 task 注册成功就上训练 | 训练报错时很难定位 |
| 还没理解 Direct / Manager 差异就来回切 | 增加不必要复杂度 |
| 一开始就追复杂机器人 | 容易被 asset / joint / reset 问题拖死 |

---

## 14. 当前最推荐的动手顺序

### Step 1
在本地 IsaacLab 根目录运行：
```bash
./isaaclab.sh --new
```

### Step 2
选择：
- External
- Direct
- 一个 RL framework

### Step 3
安装生成项目：
```bash
python -m pip install -e source/<project-name>
```

### Step 4
列出项目 task：
```bash
python scripts/list_envs.py
```

### Step 5
打开 task 的：
- `__init__.py`
- env cfg
- env implementation

先读，不急着改。

### Step 6
确认它能跑通，再进入下一阶段：
- articulation
- joint
- 简单机器人
- 最小训练任务

---

## 15. 下一份最值得继续写的文档

在这份 walkthrough 之后，最合适接着写的是：

1. `notes/03-how-articulation-and-joints-work.md`
   - 专门从本地 docs 把 articulation / actuators / init_state 讲清楚

2. `notes/04-first-minimal-training-task.md`
   - 专门讲最小 Direct RL 环境骨架

---

## 16. 一句话总结

你现在这一步，不是“训练机器人”，而是：

> **先用官方 template generator 生成一个 external + direct 的 Isaac Lab 项目，确认 task 注册、目录结构、脚本入口都打通。**

这一步走顺了，后面再学关节、机器人和训练，才不会乱。

