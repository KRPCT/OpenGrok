<div align="center">

<h1>OpenGrok (<code>grok</code>)</h1>

[English](README.md) · **简体中文**

一个终端 AI 编码 agent —— 全屏 TUI,能理解你的代码库、编辑文件、执行 shell
命令、联网搜索,并管理长时任务。

</div>

---

> **关于本分叉。** OpenGrok 是
> [xAI **Grok Build**](https://github.com/xai-org/grok-build) 的独立改名分叉,
> 依据 Apache License 2.0 分发。本项目**与 SpaceXAI / xAI 无隶属关系,亦未获其
> 赞助或背书**。"Grok" 为其各自所有者的商标,Apache License 不授予该商标的使用
> 权。归属信息与本分叉的改动清单见 [`NOTICE`](NOTICE)。

## 特性

- 全屏终端界面:滚动回看、输入框、模态窗。
- 读取并编辑代码库;执行 shell 命令、代码搜索与 web fetch。
- 支持交互式、无头(`grok -p "…"`,用于脚本/CI),或经 Agent Client Protocol
  (ACP) 嵌入编辑器运行。
- 可插拔的模型后端,包括在 `~/.grok/config.toml` 中配置的自定义 / 第三方
  OpenAI 兼容端点。

## 从源码构建

依赖:

- **Rust** —— 工具链由 [`rust-toolchain.toml`](rust-toolchain.toml) 锁定;
  首次构建时 `rustup` 会自动安装。
- **protoc** —— proto 代码生成会解析 [`bin/protoc`](bin/protoc)(一个
  [dotslash](https://dotslash-cli.com) 启动器),或回退到 `PATH` / `$PROTOC`
  上的 `protoc`。
- 支持在 macOS 与 Linux 上构建;Windows 构建为尽力而为。

```sh
cargo run   -p xai-grok-pager-bin            # 构建并启动 TUI
cargo build -p xai-grok-pager-bin --release  # release 二进制:target/release/xai-grok-pager
cargo check -p xai-grok-pager-bin            # 快速校验
```

二进制产物名为 `xai-grok-pager`,启动命令为 `grok`。首次启动会打开浏览器进行
认证。自定义 / 第三方 OpenAI 兼容端点可在 `~/.grok/config.toml` 中配置(参见
`crates/codegen/xai-grok-pager/docs/user-guide/` 下的用户指南)。

## 配置模型端点

默认情况下,`grok` 首次启动会登录你的账户。若要改为接入任意 **OpenAI 兼容**
端点 —— 自建模型、企业网关或第三方中转 —— 在 `~/.grok/config.toml` 中加一个
模型块即可。把下面的占位符替换成你自己 provider 的值:

```toml
[model."my-model"]
model          = "MODEL_ID"                              # 发给 provider 的模型名
base_url       = "https://YOUR_PROVIDER.example.com/v1"  # OpenAI 兼容的 base URL(占位符)
name           = "My Model"                              # 模型选择器里显示的名称
env_key        = "MY_API_KEY"                            # 存放 key 的环境变量名 —— 让密钥不落盘
api_backend    = "chat_completions"                      # "chat_completions"(默认)、"responses" 或 "messages"
context_window = 200000                                  # 上下文 token 数;决定自动压缩触发点
```

把 API key 放进环境变量而非写入文件,然后运行:

```sh
export MY_API_KEY="YOUR_API_KEY_HERE"   # Windows PowerShell:setx MY_API_KEY "YOUR_API_KEY_HERE"
grok -m my-model                        # 或在 TUI 内切换:/model my-model
```

`[model."…"]` 的 key 是你传给 `-m` / `/model` 的本地 id;`model` 字段才是发给
provider 的 slug。完整字段说明见
[`docs/user-guide/11-custom-models.md`](crates/codegen/xai-grok-pager/docs/user-guide/11-custom-models.md)。

## 仓库结构

| 路径 | 内容 |
|------|------|
| `crates/codegen/xai-grok-pager-bin` | 组合根 package;构建 `xai-grok-pager` 二进制 |
| `crates/codegen/xai-grok-pager` | TUI:滚动回看、输入框、模态窗、渲染 |
| `crates/codegen/xai-grok-shell` | Agent 运行时 + leader/stdio/headless 入口 |
| `crates/codegen/xai-grok-tools` | 工具实现(终端、文件编辑、搜索……) |
| `crates/codegen/xai-grok-workspace` | 宿主文件系统、VCS、执行、检查点 |
| `crates/codegen/...` | CLI crate 闭包的其余部分(config、MCP、markdown、sandbox……) |
| `crates/common/`、`crates/build/`、`prod/mc/` | 闭包引入的小型共享叶子 crate |
| `third_party/` | vendored 上游源码(Mermaid 图表栈) |

> [!IMPORTANT]
> 根 `Cargo.toml`(workspace 成员、依赖版本、lints、profiles)是**生成的** ——
> 视为只读。优先编辑各 crate 自己的 `Cargo.toml`。

## 开发

```sh
cargo check -p <crate>         # 始终针对具体 crate;全 workspace 构建很慢
cargo test  -p xai-grok-config # 单 crate 测试
cargo clippy -p <crate>        # lint 配置:仓库根的 clippy.toml
cargo fmt --all                # 仓库根的 rustfmt.toml
```

## 许可证

本仓库的第一方代码依据 **Apache License, Version 2.0** 授权 ——
见 [`LICENSE`](LICENSE) 与 [`NOTICE`](NOTICE)。

第三方与 vendored 代码仍保留其各自的原始许可证。参见:

- [`THIRD-PARTY-NOTICES`](THIRD-PARTY-NOTICES) —— crates.io / git 依赖、内置 UI
  主题,以及树内源码移植(含 openai/codex 与 sst/opencode 的工具实现)。
- [`crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md`](crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md)
  —— codex 与 opencode 移植的 crate 级 notice。
- [`third_party/NOTICE`](third_party/NOTICE) —— vendored Mermaid 栈索引。
