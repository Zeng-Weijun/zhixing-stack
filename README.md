# Zhixing Stack

## 从指令模型到可靠智能体的全链路开源栈

Zhixing Stack（知行智能体开源栈）是一个面向 Agent 的全链路开源项目。我们从 Coding Agent 开始，逐步扩展到工具调用、MCP、Web Search、Multi-Agent 和 Computer-Use Agent。

“知”代表 instruction、模型、数据和推理；“行”代表环境、工具、运行时和可执行动作。项目的目标是把模型知识稳定地转化为可复现、可评测的智能体行为。

项目不把模型、数据、环境、运行时、训练和评测混在一个仓库里，而是通过稳定的协议和 artifact contract 连接成一条完整链路：

```text
Instruction Model
        │
        ▼
┌──────────────────┐
│  Zhixing Env     │  Repo → executable environment
└────────┬─────────┘
         │ EnvironmentManifest / TaskSpec
         ▼
┌──────────────────┐
│ Zhixing Runtime  │  Agent loop / tools / traces / replay
└────────┬─────────┘
         │ Trace / Patch / Verdict
         ▼
┌──────────────────┐
│  Zhixing Data    │  Trajectory and dataset factory
└────────┬─────────┘
         │ DatasetRelease / TrainingRecord
         ▼
┌──────────────────┐
│  Zhixing Train   │  SFT / RL / GRPO
└────────┬─────────┘
         │ CheckpointManifest
         ▼
┌──────────────────┐
│  Zhixing Bench   │  Agent and model evaluation
└──────────────────┘

All shared contracts are defined by Zhixing Spec.
```

## 仓库组成

| Repository | 中文职责 | English role |
| --- | --- | --- |
| [zhixing-spec](https://github.com/Zeng-Weijun/zhixing-spec) | 公共 schema、协议和 artifact contract | Shared schemas, protocols, and artifact contracts |
| [zhixing-env](https://github.com/Zeng-Weijun/zhixing-env) | 将真实仓库构建成可执行环境和任务 | Build executable environments and tasks from repositories |
| [zhixing-data](https://github.com/Zeng-Weijun/zhixing-data) | 生成、清洗和发布训练数据 | Produce, validate, and publish training data |
| [zhixing-runtime](https://github.com/Zeng-Weijun/zhixing-runtime) | 执行 Agent、工具调用、轨迹和回放 | Execute agents, tools, traces, and replay |
| [zhixing-train](https://github.com/Zeng-Weijun/zhixing-train) | SFT、RL、GRPO 和训练编排 | SFT, RL, GRPO, and training orchestration |
| [zhixing-bench](https://github.com/Zeng-Weijun/zhixing-bench) | Benchmark、评测和排行榜 | Benchmark suites, evaluation, and leaderboards |

当前仓库对应关系：

```text
repo2env                         → Zhixing Env
datafactory / swe-data-harness  → Zhixing Data
swe-agent-runtime               → Zhixing Runtime
slime-infra                     → Zhixing Train
Agentic-foundation-model-bench- → Zhixing Bench
```

旧仓库先保留，迁移期间不破坏旧的 Python package、CLI、schema ID 和历史链接。

## 第一版：Zhixing Coder

第一版产品组合是 `Zhixing Coder`。它是一个 profile/distribution，不是另一个重复实现底层能力的仓库。

```text
Zhixing Coder
├── Zhixing Env
├── Zhixing Runtime
├── Zhixing Data
├── Zhixing Train
└── Zhixing Bench
```

第一版覆盖：

- repository-to-environment 构建
- terminal/code editing agent runtime
- verifier-backed trajectory
- SFT 数据导出
- RL/GRPO 训练入口
- SWE 和 terminal benchmark

后续可以增加：

```text
Zhixing MCP
Zhixing Web
Zhixing Search
Zhixing Multi-Agent
Zhixing CUA
```

## 统一对接原则

1. 跨仓库只依赖 `zhixing-spec` 的协议，不直接 import 其他仓库的内部模块。
2. 通过带有 `schema_version`、`artifact_id`、`producer_version` 和 `sha256` 的 artifact 交换数据。
3. 环境、任务、轨迹、verdict、数据集、checkpoint 和评测结果都可独立存储和复现。
4. 每个仓库既可以独立安装和使用，也可以被 `zhixing-stack` 组合入口调用。
5. Slime、vLLM、TRL 等是可替换 backend，不是 Zhixing Stack 的公共协议。

## English

Zhixing Stack is an open-source stack for building reliable agents from instruction models. We start with coding agents and will expand to tool use, MCP, web search, multi-agent systems, and computer-use agents.

The project separates environments, runtime, data, training, and evaluation. Stable schemas and artifact contracts connect the repositories without requiring them to import each other's internal implementation.

The first product profile is **Zhixing Coder**. It combines repository-to-environment construction, a training-native agent runtime, verifier-backed data production, SFT/RL training, and benchmark evaluation.

## Status

The repositories are being bootstrapped as public alpha foundations under the Zhixing Stack working name. APIs and schema versions are expected to evolve; production guarantees should not be inferred from this initial scaffold.

## Contributing

Please keep changes within the ownership boundary of the target repository. Cross-repository changes must update the corresponding contract and compatibility fixtures in `zhixing-spec`.
