# EvoCUA 

EvoCUA是一个Computer Use Agent（多模态模型 + 配套的Agent脚手架），能够接收用户任务指令与PC截图，与PC环境进行多轮交互，操作Chrome、Excel、PPT、VSCode等软件完成用户指令。

我们提出了一套数据合成、模型训练方法，在多个开源多模态模型上实验，均能在不损失模型通用能力的情况下，显著提升模型的Computer Use能力，在OSWorld Benchmark上取得了开源SOTA 效果，max_steps 50的设置下任务完成率为50.3%，详见https://os-world.github.io/。


## 目录结构

- `run_multienv_evocua.py`: EvoCUA 评测入口（多环境并行）
- `lib_run_single.py`: 单任务 rollout 执行逻辑（保存轨迹、截图、录屏、评分）
- `lib_results_logger.py`: 评测结果实时汇总写入 `results.json`
- `desktop_env/`: OSWorld 环境侧实现（providers/controllers/evaluators 等）
- `mm_agents/evocua/`: EvoCUA agent（Prompt、解析、动作生成）
- `mm_agents/utils/qwen_vl_utils.py`: EvoCUA 图像 resize 依赖
- `evaluation_examples/`: OSWorld 任务配置（examples/test_*.json 等）

## 安装

建议使用 Python 3.12

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## 模型依赖（HuggingFace + vLLM 0.12 Serving）

评测时需要先从 HuggingFace 下载 EvoCUA 模型权重，并使用 **vLLM 0.12** 部署一个 OpenAI-compatible 的推理服务，然后通过 `OPENAI_API_KEY / OPENAI_BASE_URL` 指向该服务。

示例（占位符请替换为你的模型仓库 ID 与本地路径）：

```bash
# 1) 下载模型（可选：使用 huggingface-cli 或 git lfs）
# huggingface-cli download <org_or_user>/<EvoCUA-model> --local-dir /path/to/EvoCUA-model --local-dir-use-symlinks False

# 2) 安装并启动 vLLM 0.12（建议在单独的 serving 环境里）
# pip install "vllm==0.12.*"
vllm serve /path/to/EvoCUA-model \
  --served-model-name EvoCUA-S2 \
  --host 0.0.0.0 \
  --port 8080

# 3) 在运行评测前指向 vLLM 的 OpenAI 接口
export OPENAI_API_KEY="dummy"
export OPENAI_BASE_URL="http://127.0.0.1:8080/v1"
```


## 运行（OSWorld 评测运行示例）

S2 示例：

```bash
python3 run_multienv_evocua.py \
  --headless \
  --provider_name aws \
  --observation_type screenshot \
  --model EvoCUA-S2 \
  --result_dir ./evocua_s2 \
  --test_all_meta_path evaluation_examples/test_nogdrive.json \
  --max_steps 50 \
  --num_envs 30 \
  --max_history_turns 4 \
  --coordinate_type relative \
  --resize_factor 32 \
  --prompt_style S2
```

S1 示例：

```bash
python3 run_multienv_evocua.py \
  --headless \
  --provider_name aws \
  --observation_type screenshot \
  --model EvoCUA-S1 \
  --result_dir ./evocua_s1 \
  --test_all_meta_path evaluation_examples/test_nogdrive.json \
  --max_steps 50 \
  --num_envs 30 \
  --max_history_turns 3 \
  --coordinate_type qwen25 \
  --max_tokens 10240 \
  --resize_factor 28 \
  --prompt_style S1
```

## 环境变量

可参考 `env.template`（按需复制为 `.env`），也可以直接在 shell 中 export。

## 说明

- 该仓库包含 OSWorld 评测所需的环境侧代码与任务配置；实际运行仍依赖你配置好的 VM/provider（例如 AWS/VMware/Docker 等）与 OSWorld VM 镜像/快照。


