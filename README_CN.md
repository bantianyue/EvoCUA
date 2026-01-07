<div align="center">

# EvoCUA: Evolving Computer Use Agent

**🥇 开源模型第一 | 擅长 Computer Use 的通用多模态大模型**

[![Model](https://img.shields.io/badge/🤗%20HuggingFace-EvoCUA--32B-blue)](https://huggingface.co/meituan/EvoCUA-32B-20260105)
[![OSWorld Score](https://img.shields.io/badge/OSWorld-56.7%25-brightgreen)](https://os-world.github.io/)
[![License](https://img.shields.io/badge/License-Apache%202.0-orange)](./LICENSE)

[English](./README.md) | [中文](./README_CN.md)

<img src="assets/images/osworld_leaderboard.png" width="900" alt="OSWorld Leaderboard">

**🥇 开源模型第一 | OSWorld 排行榜（2026年1月）**

</div>

---

## 🌟 亮点

- 🥇 **开源模型第一**：达到 **56.7%** 任务完成率，**开源模型中排名第一**
- 📈 **比 OpenCUA-72B 高 11.7%**（45.0%→56.7%）：以更少的参数量（32B vs 72B）显著超越此前开源 SOTA
- 🚀 **比 Qwen3-VL Thinking提升 15.1%**（41.6%→56.7%）：步数减半（100→50），验证训练方法有效性
- 🧠 **通用多模态大模型**：基于 Qwen3VL-32B 训练，在提升 Computer Use 能力的同时保持模型通用能力
- 🖥️ **端到端电脑自动化**：通过截图和自然语言指令，操作真实桌面环境
- 🔄 **多轮交互能力**：通过自然语言指令，流畅操作 Chrome、Excel、PPT、VSCode 等多种软件
- 📊 **创新训练方法**：我们提出的数据合成与模型训练方法，能在不损失模型通用能力的情况下，显著提升多个开源多模态模型的 Computer Use 能力

---

## 📊 性能对比

| 排名 | 模型 | 开源/闭源 | 类型 | 最大步数 | 得分 |
|------|------|-----------|------|----------|------|
| 1 | Claude-sonnet-4-5 | 🔒 闭源 | 通用模型 | 100 | 62.9% |
| 2 | Seed-1.8 | 🔒 闭源 | 通用模型 | 100 | 61.9% |
| 3 | Claude-sonnet-4-5 | 🔒 闭源 | 通用模型 | 50 | 58.1% |
| **4** | **EvoCUA-20260105 (Ours)** | **🟢 开源** | **通用模型** | **50** | **56.7% 🥇** |
| 5 | DeepMiner-Mano-72B | 🔒 闭源 | 专用模型 | 100 | 53.9% |
| 6 | UI-TARS-2-2509 | 🟢 开源 | 通用模型 | 100 | 53.1% |
| 7 | EvoCUA (Previous) | 🔒 闭源 | 通用模型 | 50 | 50.3% |
| 8 | OpenCUA-72B | 🟢 开源 | 专用模型 | 100 | 45.0% |
| ... | ... | ... | ... | ... | ... |
| 13 | Qwen3-VL-Flash | 🟢 开源 | 通用模型 | 100 | 41.6% |

**核心亮点：**
- 🥇 **开源模型第一**
- 📈 比 OpenCUA-72B **高 11.7%**（45.0%→56.7%），参数量更少（32B vs 72B）
- 🚀 比 Qwen3-VL 基座模型**提升 15.1%**（41.6%→56.7%），步数减半（100→50）
- ⚡ 仅用 **50 步**即达到竞争力效果，其他模型多使用 100 步

> 注：人类在 OSWorld 上的表现仍显著高于当前最佳模型，说明该领域仍有很大的提升空间。

---

## 🚀 快速开始

### 安装

建议使用 Python 3.12

```bash
git clone https://github.com/meituan/EvoCUA.git
cd EvoCUA
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 模型下载与部署

EvoCUA 需要从 HuggingFace 下载模型权重，并使用 **vLLM 0.12** 部署为 OpenAI 兼容的推理服务。

```bash
# 1) 下载模型权重
huggingface-cli download meituan/EvoCUA-32B-20260105 \
  --local-dir /path/to/EvoCUA-32B \
  --local-dir-use-symlinks False

# 2) 启动 vLLM 推理服务（建议使用单独的环境）
pip install "vllm==0.12.*"
vllm serve /path/to/EvoCUA-32B \
  --served-model-name EvoCUA \
  --host 0.0.0.0 \
  --port 8080 \
  --tensor-parallel-size 2

# 3) 设置环境变量
export OPENAI_API_KEY="dummy"
export OPENAI_BASE_URL="http://127.0.0.1:8080/v1"
```

### 在 OSWorld 上运行评测

```bash
python3 run_multienv_evocua.py \
  --headless \
  --provider_name aws \
  --observation_type screenshot \
  --model EvoCUA-S2 \
  --result_dir ./evocua_results \
  --test_all_meta_path evaluation_examples/test_nogdrive.json \
  --max_steps 50 \
  --num_envs 30 \
  --max_history_turns 4 \
  --coordinate_type relative \
  --resize_factor 32 \
  --prompt_style S2
```

---

## 📁 项目结构

```
EvoCUA/
├── run_multienv_evocua.py      # 评测入口（多环境并行）
├── lib_run_single.py           # 单任务 rollout 执行逻辑（轨迹、截图、录屏、评分）
├── lib_results_logger.py       # 评测结果实时汇总写入 results.json
├── desktop_env/                # OSWorld 环境侧实现
│   ├── providers/              # VM 提供商（AWS/VMware/Docker 等）
│   ├── controllers/            # 环境控制器
│   └── evaluators/             # 任务评估器
├── mm_agents/
│   └── evocua/                 # EvoCUA Agent（Prompt、解析、动作生成）
└── evaluation_examples/        # OSWorld 任务配置
```

---

## 🔧 配置

环境变量可以在 `.env` 文件中配置（参考 `env.template`）：

```bash
cp env.template .env
# 编辑 .env 文件，填入你的配置
```

---

## 📖 关于 OSWorld

[OSWorld](https://os-world.github.io/) 是 Computer Use Agent 领域最具影响力的基准测试。包括 **OpenAI、Anthropic、字节跳动 Seed、月之暗面、智谱 AI、阶跃星辰** 等多家顶尖 AI 团队均在使用该基准进行评测。OSWorld 通过与真实桌面环境的多轮交互，评估 Agent 完成实际电脑任务的能力。

---

## 🔗 相关资源

- 🤗 **模型权重**：[meituan/EvoCUA-32B-20260105](https://huggingface.co/meituan/EvoCUA-32B-20260105)
- 📊 **OSWorld 基准测试**：[os-world.github.io](https://os-world.github.io/)
- 📄 **技术报告**：即将发布!
- 🚀 **更多模型尺寸**：多种尺寸的模型正在路上，即将开源！

---

## 🙏 致谢

衷心感谢开源社区对 Computer Use Agent 领域的杰出贡献：

- **[OSWorld](https://github.com/xlang-ai/OSWorld)** — 感谢 Tianbao Xie 及团队创建了 Computer Use Agent 领域最具影响力的基准测试
- **[OpenCUA](https://github.com/xlang-ai/OpenCUA)** — 感谢 Xinyuan Wang 及团队在开源 Computer Use Agent 研究和 AgentNet 数据集方面的开创性工作

我们致力于回馈社区，将持续开源我们的研究成果，推动领域发展。

---

## 📝 引用

如果 EvoCUA 对您的研究有所帮助，请考虑引用：

```bibtex
@misc{evocua2026,
  title={EvoCUA: Evolving Computer Use Agent},
  author={Meituan LongCat Team},
  year={2026},
  url={https://github.com/meituan/EvoCUA}
}
```

---

## 📜 开源协议

本项目基于 Apache 2.0 协议开源，详见 [LICENSE](./LICENSE) 文件。

---

<div align="center">

**由美团 LongCat 团队用 ❤️ 打造**

</div>
