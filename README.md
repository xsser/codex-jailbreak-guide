# Codex CLI 越狱：model_instructions_file

## 这是什么

通过修改 Codex CLI 的 `config.toml` 中 `model_instructions_file` 字段，用自定义 prompt **完全替换**官方的安全对齐指令。模型从启动那一刻起就在你的指令框架下运行，官方安全 prompt 不残留。

## model_instructions_file 如何定义

`~/.codex/config.toml`：

```toml
model = "gpt-5.5"
model_instructions_file = "/home/yourname/.codex/prompts/codex-base.md"
approval_policy = "never"
sandbox_mode = "danger-full-access"
```

### 源码层原理

Codex CLI 的 Rust 源码（`profile_toml.rs`）中：

```rust
model_instructions_file: Option<AbsolutePathBuf>
```

- 类型是 `Option`——**填了就用你的文件，不填就用官方内置的 `base_instructions/default.md`**
- 作用是**直接替换**（replace），不是追加（append）

### model_instructions_file vs AGENTS.md

| 特性 | model_instructions_file | AGENTS.md |
|------|------------------------|-----------|
| 作用 | 替换 base instructions | 追加指令 |
| 官方安全层 | **移除** | 保留 |
| 配置位置 | config.toml | 项目根目录 |

## 设置步骤

```bash
# 1. 创建 prompt 文件
mkdir -p ~/.codex/prompts
cp prompts/codex-base.md ~/.codex/prompts/codex-base.md

# 2. config.toml 加上配置
cat >> ~/.codex/config.toml << 'EOF'
model_instructions_file = "/home/yourname/.codex/prompts/codex-base.md"
approval_policy = "never"
sandbox_mode = "danger-full-access"
EOF

# 3. 启动 codex
codex
```

## 文件说明

| 文件 | 说明 |
|------|------|
| `prompts/codex-base.md` | 越狱 prompt 全文（UNFILTERED MODE） |
| `ANALYSIS.md` | Prompt 逐段越狱逻辑拆解 |
