# Agenesis

## 从指令模型到可靠智能体的全链路开源栈

Agenesis 是一个面向 Agent 的全链路开源项目。我们从 Coding Agent 开始，逐步扩展到工具调用、MCP、Web Search、Multi-Agent 和 Computer-Use Agent。

项目不把模型、数据、环境、运行时、训练和评测混在一个仓库里，而是通过稳定的协议和 artifact contract 连接成一条完整链路：

```text
Instruction Model
        │
        ▼
┌──────────────────┐
│  Agenesis Env    │  Repo → executable environment
└────────┬─────────┘
         │ EnvironmentManifest / TaskSpec
         ▼
┌──────────────────┐
│ Agenesis Runtime │  Agent loop / tools / traces / replay
└────────┬─────────┘
         │ Trace / Patch / Verdict
         ▼
┌──────────────────┐
│  Agenesis Data   │  Trajectory and dataset factory
└────────┬─────────┘
         │ DatasetRelease / TrainingRecord
         ▼
┌──────────────────┐
│  Agenesis Train  │  SFT / RL / GRPO
└────────┬─────────┘
         │ CheckpointManifest
         ▼
┌──────────────────┐
│  Agenesis Bench  │  Agent and model evaluation
└──────────────────┘

All shared contracts are defined by Agenesis Spec.
```

## 仓库组成

| Repository | 中文职责 | English role |
| --- | --- | --- |
| [agenesis-spec](https://github.com/Zeng-Weijun/agenesis-spec) | 公共 schema、协议和 artifact contract | Shared schemas, protocols, and artifact contracts |
| [agenesis-env](https://github.com/Zeng-Weijun/agenesis-env) | 将真实仓库构建成可执行环境和任务 | Build executable environments and tasks from repositories |
| [agenesis-data](https://github.com/Zeng-Weijun/agenesis-data) | 生成、清洗和发布训练数据 | Produce, validate, and publish training data |
| [agenesis-runtime](https://github.com/Zeng-Weijun/agenesis-runtime) | 执行 Agent、工具调用、轨迹和回放 | Execute agents, tools, traces, and replay |
| [agenesis-train](https://github.com/Zeng-Weijun/agenesis-train) | SFT、RL、GRPO 和训练编排 | SFT, RL, GRPO, and training orchestration |
| [agenesis-bench](https://github.com/Zeng-Weijun/agenesis-bench) | Benchmark、评测和排行榜 | Benchmark suites, evaluation, and leaderboards |

当前仓库对应关系：

```text
repo2env                         → Agenesis Env
datafactory / swe-data-harness  → Agenesis Data
swe-agent-runtime               → Agenesis Runtime
slime-infra                     → Agenesis Train
Agentic-foundation-model-bench- → Agenesis Bench
```

旧仓库先保留，迁移期间不破坏旧的 Python package、CLI、schema ID 和历史链接。

## 第一版：Agenesis Coder

第一版产品组合是 `Agenesis Coder`。它是一个 profile/distribution，不是另一个重复实现底层能力的仓库。

```text
Agenesis Coder
├── Agenesis Env
├── Agenesis Runtime
├── Agenesis Data
├── Agenesis Train
└── Agenesis Bench
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
Agenesis MCP
Agenesis Web
Agenesis Search
Agenesis Multi-Agent
Agenesis CUA
```

## 统一对接原则

1. 跨仓库只依赖 `agenesis-spec` 的协议，不直接 import 其他仓库的内部模块。
2. 通过带有 `schema_version`、`artifact_id`、`producer_version` 和 `sha256` 的 artifact 交换数据。
3. 环境、任务、轨迹、verdict、数据集、checkpoint 和评测结果都可独立存储和复现。
4. 每个仓库既可以独立安装和使用，也可以被 `agenesis` 组合入口调用。
5. Slime、vLLM、TRL 等是可替换 backend，不是 Agenesis 的公共协议。

## English

Agenesis is an open-source stack for building reliable agents from instruction models. We start with coding agents and will expand to tool use, MCP, web search, multi-agent systems, and computer-use agents.

The project separates environments, runtime, data, training, and evaluation. Stable schemas and artifact contracts connect the repositories without requiring them to import each other's internal implementation.

The first product profile is **Agenesis Coder**. It combines repository-to-environment construction, a training-native agent runtime, verifier-backed data production, SFT/RL training, and benchmark evaluation.

## Status

The repositories are being bootstrapped as public alpha foundations. APIs and schema versions are expected to evolve; production guarantees should not be inferred from this initial scaffold.

## Contributing

Please keep changes within the ownership boundary of the target repository. Cross-repository changes must update the corresponding contract and compatibility fixtures in `agenesis-spec`.

