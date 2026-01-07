<div align="center">

# EvoCUA: Evolving Computer Use Agent

**🥇 #1 Open-Source Model on OSWorld | A General-Purpose Multimodal Model Excelling at Computer Use**

[![Model](https://img.shields.io/badge/🤗%20HuggingFace-EvoCUA--32B-blue)](https://huggingface.co/meituan/EvoCUA-32B-20260105)
[![OSWorld Score](https://img.shields.io/badge/OSWorld-56.7%25-brightgreen)](https://os-world.github.io/)
[![License](https://img.shields.io/badge/License-Apache%202.0-orange)](./LICENSE)

[English](./README.md) | [中文](./README_CN.md)

<img src="assets/images/osworld_leaderboard.png" width="900" alt="OSWorld Leaderboard">

**🥇 #1 Open-Source Model on OSWorld Leaderboard (Jan 2026)**

</div>

---

## 🌟 Highlights

- 🥇 **#1 Open-Source Model on OSWorld**: Achieves **56.7%** task completion rate, **#1 among all open-source models**
- 📈 **+11.7% over OpenCUA-72B** (45.0%→56.7%): Significantly outperforms the previous open-source SOTA while using fewer parameters (32B vs 72B)
- 🚀 **+15.1% over Qwen3-VL Thinking** (41.6%→56.7%): Improves with half the steps (100→50), demonstrating effective training methodology
- 🧠 **General-Purpose Multimodal Model**: Built on Qwen3VL-32B, retains strong general capabilities while excelling at computer use tasks
- 🖥️ **End-to-End Computer Automation**: Operates real desktop environments through screenshots and natural language instructions
- 🔄 **Multi-Turn Interaction**: Seamlessly operates Chrome, Excel, PowerPoint, VSCode, and more through natural language instructions
- 📊 **Novel Training Methodology**: Our data synthesis and training approach consistently improves Computer Use capability across multiple open-source VLMs without degrading general performance

---

## 📊 Performance Comparison

| Rank | Model | Open/Closed | Type | Max Steps | Score |
|------|-------|-------------|------|-----------|-------|
| 1 | Claude-sonnet-4-5 | 🔒 Closed | General | 100 | 62.9% |
| 2 | Seed-1.8 | 🔒 Closed | General | 100 | 61.9% |
| 3 | Claude-sonnet-4-5 | 🔒 Closed | General | 50 | 58.1% |
| **4** | **EvoCUA-20260105 (Ours)** | **🟢 Open** | **General** | **50** | **56.7% 🥇** |
| 5 | DeepMiner-Mano-72B | 🔒 Closed | Specialized | 100 | 53.9% |
| 6 | UI-TARS-2-2509 | 🟢 Open | General | 100 | 53.1% |
| 7 | EvoCUA *(Previous Version)* | 🔒 Closed | General | 50 | 50.3% |
| 8 | OpenCUA-72B | 🟢 Open | Specialized | 100 | 45.0% |
| ... | ... | ... | ... | ... | ... |
| 13 | Qwen3-VL-Flash | 🟢 Open | General | 100 | 41.6% |

**Key Highlights:**
- 🥇 **#1 among ALL open-source models**
- 📈 **+11.7%** higher than OpenCUA-72B (45.0%→56.7%), with fewer parameters (32B vs 72B)
- 🚀 **+15.1%** improvement over Qwen3-VL model (41.6%→56.7%) with half the steps (100→50)
- ⚡ Achieves competitive results with only **50 steps** while others use 100 steps

> Note: Human-level performance on OSWorld remains significantly higher, indicating substantial room for improvement in the field.

---

## 🚀 Quick Start

### Installation

Python 3.12 is recommended.

```bash
git clone https://github.com/meituan/EvoCUA.git
cd EvoCUA
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Model Download & Deployment

EvoCUA requires downloading the model weights from HuggingFace and deploying with **vLLM 0.12** as an OpenAI-compatible inference server.

```bash
# 1) Download model weights
huggingface-cli download meituan/EvoCUA-32B-20260105 \
  --local-dir /path/to/EvoCUA-32B \
  --local-dir-use-symlinks False

# 2) Launch vLLM serving (recommend separate environment)
pip install "vllm==0.12.*"
vllm serve /path/to/EvoCUA-32B \
  --served-model-name EvoCUA \
  --host 0.0.0.0 \
  --port 8080 \
  --tensor-parallel-size 2

# 3) Set environment variables
export OPENAI_API_KEY="dummy"
export OPENAI_BASE_URL="http://127.0.0.1:8080/v1"
```

### Run Evaluation on OSWorld

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
  --temperature 0.01 \
  --max_history_turns 4 \
  --coordinate_type relative \
  --resize_factor 32 \
  --prompt_style S2
```

---

## 📁 Project Structure

```
EvoCUA/
├── run_multienv_evocua.py      # Main entry point (multi-env parallel evaluation)
├── lib_run_single.py           # Single task rollout logic (trajectory, screenshots, recording, scoring)
├── lib_results_logger.py       # Real-time result aggregation to results.json
├── desktop_env/                # OSWorld environment implementation
│   ├── providers/              # VM providers (AWS/VMware/Docker/etc.)
│   ├── controllers/            # Environment controllers
│   └── evaluators/             # Task evaluators
├── mm_agents/
│   └── evocua/                 # EvoCUA agent (prompts, parsing, action generation)
└── evaluation_examples/        # OSWorld task configurations
```

---

## 🔧 Configuration

Environment variables can be configured in `.env` file (see `env.template` for reference):

```bash
cp env.template .env
# Edit .env with your configurations
```

---

## 📖 About OSWorld

[OSWorld](https://os-world.github.io/) is the most influential benchmark in the Computer Use Agent domain. It is adopted by leading AI organizations including **OpenAI, Anthropic, ByteDance Seed, Moonshot AI, Zhipu AI, Step**, and more. OSWorld evaluates agents' ability to complete real-world computer tasks through multi-turn interactions with actual desktop environments.

---

## 🔗 Resources

- 🤗 **Model Weights**: [meituan/EvoCUA-32B-20260105](https://huggingface.co/meituan/EvoCUA-32B-20260105)
- 📊 **OSWorld Benchmark**: [os-world.github.io](https://os-world.github.io/)
- 📄 **Technical Report**: Coming Soon
- 🚀 **More Model Sizes**: Models of various sizes are on the way and will be open-sourced soon!

---

## 🙏 Acknowledgements

We sincerely thank the open-source community for their outstanding contributions to the Computer Use Agent field:

- **[OSWorld](https://github.com/xlang-ai/OSWorld)** — Tianbao Xie and team for creating the most influential benchmark in the Computer Use Agent domain
- **[OpenCUA](https://github.com/xlang-ai/OpenCUA)** — Xinyuan Wang and team for pioneering open-source Computer Use Agent research.

We are committed to giving back to the community and will continue to open-source our research to advance the field.

---

## 📝 Citation

If you find EvoCUA useful in your research, please consider citing:

```bibtex
@misc{evocua2026,
  title={EvoCUA: Evolving Computer Use Agent},
  author={Meituan LongCat Team},
  year={2026},
  url={https://github.com/meituan/EvoCUA}
}
```

---

## 📜 License

This project is licensed under the Apache 2.0 License - see the [LICENSE](./LICENSE) file for details.

---

<div align="center">

**Built with ❤️ by Meituan LongCat Team**

</div>

