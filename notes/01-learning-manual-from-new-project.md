# Isaac Lab 学习手册 v0.1：从新项目开始，到简单机器人、关节定义与最小训练

> 这份手册严格以本地 `IsaacLab` 仓库中的 docs 为一手资料整理，目标不是泛泛介绍，而是给后续实际动手提供一条清晰学习路径。

---

## 0. 这份手册依赖的本地一手资料

本手册主要基于以下本地文档整理：

| 本地文档 | 用途 |
|---|---|
| `docs/source/setup/quickstart.rst` | 快速理解 Isaac Lab 的安装、训练、环境、模板生成 |
| `docs/source/overview/own-project/index.rst` | 新项目 / 新任务入口 |
| `docs/source/overview/own-project/template.rst` | 如何用模板生成新项目 |
| `docs/source/overview/own-project/project_structure.rst` | 理解项目结构 |
| `docs/source/tutorials/index.rst` | 官方教程顺序 |
| `docs/source/tutorials/01_assets/add_new_robot.rst` | 添加新机器人、理解 articulation / actuator |
| `docs/source/tutorials/03_envs/create_direct_rl_env.rst` | 如何定义最小 RL 环境 |

---

## 1. 你现在最该怎么学

根据 Isaac Lab 本地 docs，最合理的学习路径不是一上来造复杂机器人，而是：

1. **先理解 Isaac Lab 的工作流和项目生成方式**
2. **先用官方教程学会：启动仿真、加载 asset、运行 articulation**
3. **再学怎么把一个简单机器人定义成 `ArticulationCfg`**
4. **再学怎么创建一个最小 `DirectRLEnv`**
5. **最后再把简单机器人做成可训练任务**

这和你现在的目标是匹配的：

- 先从简单机器人开始
- 先理解 joint / articulation / task
- 再开始训练

---

## 2. 先明确 Isaac Lab 的三个关键概念

### 2.1 Project

根据 `project_structure.rst`：

- **Project** 是根目录
- 里面通常有：
  - `source/`
  - `scripts/`
  - `README.md`

这就是你自己的 Isaac Lab 学习 / 开发项目容器。

### 2.2 Extension / Module

根据 `project_structure.rst`：

- 生成的新项目会被当成 Python package + Isaac Sim extension
- `source/<project-name>/...` 里放你的代码
- 最后靠模块路径作为 task 的 entry point

这意味着：

**你不是随便写个脚本就结束，而是要把项目安装成一个 package，让 Isaac Lab 能通过 gym entry point 找到你的环境。**

### 2.3 Task

根据 `project_structure.rst` 与 `quickstart.rst`：

- Task 才是训练的核心
- 一个 task 至少要完成：
  - 环境定义
  - config
  - `gym.register(...)`

也就是说：

**“训练一个机器人”在 Isaac Lab 里，本质上是在定义一个可注册的 task。**

---

## 3. 先创建新项目，而不是直接在大仓库里乱写

根据 `template.rst`，官方推荐：

- 优先使用 **External project**
- 不建议一开始做 internal task

### 官方命令

在 IsaacLab 仓库根目录运行：

```bash
./isaaclab.sh --new
```

文档说明这个模板生成器会让你选择：

| 选项 | 含义 |
|---|---|
| External vs Internal | 建独立项目还是仓库内任务 |
| Direct vs Manager | 选择工作流 |
| RL Framework | 选择训练框架 |

### 你当前最适合的选择

基于本地 docs 和你的目标，我建议第一轮这样选：

| 项目 | 建议 |
|---|---|
| Project type | **External project** |
| Workflow | **Direct** |
| RL framework | 先选一个最简单、最常见的即可 |

原因：
- `quickstart.rst` 明确说 **Direct workflow** 是到可工作自定义环境的最短路径
- 你当前目标是理解任务怎么建，不是先追求 manager 的模块化设计

---

## 4. 生成项目后，先理解项目结构

根据 `project_structure.rst`，你生成项目后重点看这几个位置：

| 位置 | 作用 |
|---|---|
| `README.md` | 模板生成后的使用说明 |
| `scripts/` | train / play 等脚本 |
| `source/<project-name>/` | 你的项目代码 |
| `__init__.py` | 里面通常会有 `gym.register(...)` |
| task 目录 | 环境定义和配置的核心位置 |

文档特别强调：

- `gym.register(...)` 必须在模块初始化时被调用
- 环境之所以能被 Isaac Lab 训练脚本找到，是因为它被注册成了 gym task

所以你后面学习时，要始终记住：

**不是“写一个环境类”就够了，而是要把它注册成可运行 task。**

---

## 5. 学习顺序：先看 tutorials，不要先碰 RL

根据 `docs/source/tutorials/index.rst`，官方教程顺序本身就很合理，建议按顺序学。

### 你当前最值得先看的教程组

#### 第一组：仿真基础

| 文档 | 目的 |
|---|---|
| `tutorials/00_sim/create_empty.rst` | 理解最简单仿真入口 |
| `tutorials/00_sim/spawn_prims.rst` | 学会往场景里放东西 |
| `tutorials/00_sim/launch_app.rst` | 理解 AppLauncher |

#### 第二组：资产与 articulation

| 文档 | 目的 |
|---|---|
| `tutorials/01_assets/run_articulation.rst` | 学会与 articulation 交互 |
| `tutorials/01_assets/add_new_robot.rst` | 学会定义新机器人 |

#### 第三组：环境与训练

| 文档 | 目的 |
|---|---|
| `tutorials/03_envs/create_direct_rl_env.rst` | 学会最小 Direct RL 环境 |
| `tutorials/03_envs/register_rl_env_gym.rst` | 学会注册成 gym 任务 |
| `tutorials/03_envs/run_rl_training.rst` | 学会启动训练 |

---

## 6. 你说的“从简单机器人开始”在 Isaac Lab 里应该怎么落地

### 6.1 不一定先自己画机器人

根据 `quickstart.rst` 和 `add_new_robot.rst`：

- Isaac Lab / Isaac Sim 本身就可以用现成资产
- 机器人最终在 Isaac Lab 里是以 **`ArticulationCfg`** 来定义其接口

所以你当前有两种切入方式：

| 方式 | 适合什么时候 |
|---|---|
| 先用现成机器人资产 | 先学框架和 task |
| 再做自己的简单机器人 | 学会 robot → articulation → task 的闭环 |

我建议你现在先：

**现成机器人熟悉 Isaac Lab → 再做简单机器人**

---

## 7. 在 Isaac Lab 里，机器人是怎么定义的

根据 `add_new_robot.rst`：

机器人在 Isaac Lab 中，本质上被当成一个 **articulation with joint drives**。

关键接口是：

- `ArticulationCfg`
- `spawn=UsdFileCfg(...)`
- `actuators={...}`
- 可选的 `init_state`

### 一个机器人最小要素

`add_new_robot.rst` 明确说，最小配置里最关键的两个参数是：

| 参数 | 含义 |
|---|---|
| `spawn` | 机器人 USD 资产怎么放进仿真 |
| `actuators` | 你打算控制哪些 joints |

也就是说，你以后理解“如何在 Isaac 中定义关节”，最核心不是先想 RL，而是先看这三件事：

1. **这个机器人从哪个 USD 进来**
2. **有哪些 joints**
3. **哪些 joints 被 actuator 接管**

### `init_state` 很重要

文档特别强调：

- `init_state` 定义机器人在 spawn / reset 时的状态
- `joint_pos` 里用的是 **USD joint names**，不是 actuator 名字

这一步很关键，因为你后面学训练时，reset 逻辑会反复碰到它。

---

## 8. 先学“关节控制”，再学 RL

你前面问单摆 / 双关节是不是直接走 RL。按本地 docs，我建议：**不要先上 RL**。

### 为什么

从 `add_new_robot.rst` 和 `create_direct_rl_env.rst` 可以看出来，RL 前面至少还有这些层：

- app launch
- simulation context
- articulation config
- scene setup
- action application
- reset
- observation

如果你前面这些没吃透，RL 只会把错误放大。

### 所以你当前真正该学的是：

| 阶段 | 目标 |
|---|---|
| 1 | 机器人能正确加载 |
| 2 | 关节能被正确驱动 |
| 3 | reset 正常 |
| 4 | 能读出 joint state |
| 5 | 再把它包装成 task |
| 6 | 最后才训练 |

---

## 9. 当你开始做最小训练时，推荐走 Direct workflow

根据 `quickstart.rst`：

- Isaac Lab 有两条主要工作流：
  - `Direct`
  - `ManagerBased`
- quickstart 明确说：
  - **Direct workflow = 到可工作的自定义环境的最短路径**

所以你当前最该学的是：

**先做 Direct workflow task**

而不是一开始就上 manager-based。

---

## 10. 一个最小 Direct RL 环境需要什么

根据 `create_direct_rl_env.rst`，一个最小 Direct RL 环境至少要理解这些部分：

### 10.1 配置类

要定义一个继承自 `DirectRLEnvCfg` 的 config，比如文档里的 cartpole：

- `action_space`
- `observation_space`
- `state_space`
- `sim`
- `scene`
- robot config
- reset 条件
- reward 参数

### 10.2 环境类

要定义一个继承自 `DirectRLEnv` 的类。

### 10.3 必须实现的关键逻辑

根据文档，核心函数包括：

| 函数 | 作用 |
|---|---|
| `_setup_scene()` | 创建 scene |
| `_pre_physics_step(actions)` | 处理动作 |
| `_apply_action()` | 把动作施加到物理仿真 |
| `_get_observations()` | 构造 observation |
| `_get_rewards()` | 定义 reward |
| `_get_dones()` | 定义终止 / reset 条件 |
| `_reset_idx(env_ids)` | 重置环境 |

这是你以后做单摆、双关节、简单臂训练任务的固定骨架。

---

## 11. 所以你接下来最合理的学习路径

### Phase A：先把项目生成出来

目标：知道一个 Isaac Lab 外部项目长什么样。

操作：
1. 在本地 IsaacLab 仓库根目录运行：
   ```bash
   ./isaaclab.sh --new
   ```
2. 选 **External project**
3. 选 **Direct workflow**
4. 安装生成的项目：
   ```bash
   python -m pip install -e source/<project-name>
   ```

### Phase B：先跑通教程里的 articulation

重点看：
- `tutorials/01_assets/run_articulation.rst`
- `tutorials/01_assets/add_new_robot.rst`

目标：
- 学会加载 robot asset
- 学会理解 `ArticulationCfg`
- 学会 actuator / joint / init_state 的关系

### Phase C：先做一个最小“可控”机器人

不一定立刻自定义 URDF。

你可以先：
- 用现成 robot asset
- 做一个只关心少量 joints 的小任务

目标：
- 先把 joint control 走通
- 先不要急着训练

### Phase D：再做最小 Direct RL task

重点看：
- `tutorials/03_envs/create_direct_rl_env.rst`
- `tutorials/03_envs/register_rl_env_gym.rst`
- `tutorials/03_envs/run_rl_training.rst`

目标：
- 做一个最简单任务
- 学 observation / reward / reset / action 的全流程

---

## 12. 你当前第一阶段最值得达到的里程碑

不是“训练复杂机器人”，而是：

| 里程碑 | 标准 |
|---|---|
| M1 | 能生成一个 Isaac Lab 外部项目 |
| M2 | 能安装并注册自己的 task |
| M3 | 能加载一个简单 articulation |
| M4 | 能控制关节并正确 reset |
| M5 | 能跑一个最小 Direct RL 训练 |

只要这五步打通，你后面再谈更复杂机器人就稳很多。

---

## 13. 当前最重要的学习原则

| 原则 | 解释 |
|---|---|
| 先文档、后猜测 | 优先跟本地 Isaac Lab docs |
| 先 project，再 task | 先理解项目结构 |
| 先 articulation，再 RL | 先把 robot 和 joint 吃透 |
| 先 Direct，再 Manager | 先走最短路径 |
| 先最小成功，再复杂扩展 | 先打通一个小闭环 |

---

## 14. 下一份最值得继续整理的文档

基于当前 docs，下一步最适合继续写的是：

1. `notes/02-new-project-walkthrough.md`
   - 专门写“创建新项目”的动手手册

2. `notes/03-how-articulation-and-joints-work.md`
   - 专门写 articulation、joint、actuator、init_state 的关系

3. `notes/04-direct-rl-minimal-task.md`
   - 专门写最小 Direct RL task 的骨架

---

## 15. 一句话总结

如果你现在要照着 Isaac Lab 的本地 docs 走，最好的主线不是：

> 先做复杂机器人 → 直接训练

而是：

> **先用模板生成外部项目 → 学会 articulation / joint / actuator → 再做最小 Direct RL task → 最后才训练复杂机器人**

