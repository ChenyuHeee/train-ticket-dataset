# Train-Ticket Fault Injection Dataset (AIOps-style)

本仓库用于发布可直接用于实验的 Train-Ticket 故障注入数据集。数据来源于 k3s 集群上的 Train-Ticket 系统，在持续负载与 Chaos Mesh 故障注入下采集并转换为统一格式，便于模型训练/评测。

## 快速开始

### 1) 克隆（必须装 Git LFS）

本仓库使用 Git LFS 存储 `*.parquet` 等大文件。

```bash
git lfs install
git clone git@github.com:ChenyuHeee/train-ticket-dataset.git
cd train-ticket-dataset
```

如果你已经克隆过：

```bash
git lfs pull
git pull
```

### 2) 数据集目录结构

```text
.
├── dataset/
│   ├── YYYY-MM-DD/
│   │   ├── log-parquet/
│   │   ├── metric-parquet/
│   │   │   ├── apm/
│   │   │   ├── infra/
│   │   │   └── other/
│   │   └── trace-parquet/
│   └── ground_truth.jsonl
└── .gitattributes
```

- `dataset/ground_truth.jsonl`：故障/事件标注（JSON Lines，每行一个事件）
- `dataset/YYYY-MM-DD/log-parquet/`：日志（Parquet，按小时/桶分文件）
- `dataset/YYYY-MM-DD/metric-parquet/`：指标（Parquet，按指标类别与粒度分文件）
- `dataset/YYYY-MM-DD/trace-parquet/`：链路/trace（Parquet，按小时/桶分文件）

## Ground Truth（标注）

文件：`dataset/ground_truth.jsonl`

- 每一行是一个 JSON 对象
- 字段以实际文件为准；常见字段包括：
  - `uuid`：事件唯一标识
  - `ts`/`start_time`/`end_time`：事件时间（UTC 或带时区的 ISO8601）
  - `component`：受影响组件（服务/Pod/节点等）
  - `reason`：故障类型/原因（例如 network loss / pod kill / stress cpu 等）
  - `evidence_points`：证据点（例如 Chaos 对象、关键日志、相关指标等）

建议解析方式：逐行读取、`json.loads(line)`。

## 数据更新策略

本仓库会持续更新（2026.1.28开始）：
- 新的运行数据会周期性导出并合并到 `dataset/` 下
- 使用 `git pull` + `git lfs pull` 可获取最新数据

建议固定使用：
- 最新 commit 的 `dataset/` 目录
- 或者在你的实验日志中记录使用的数据集 commit hash（保证可复现）

## 复现与注意事项

- 单个 run 可能因为异常中断而不完整；发布数据中会尽量只保留完整可用的片段。
- 指标/trace/log 的粒度与覆盖范围以实际采集与导出为准。

## 许可与引用

- 方法部分注明：
  - Train-Ticket 系统 + Chaos Mesh 故障注入 + 统一 AIOps-style parquet 导出。
- citation （待定）
