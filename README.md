# EvoCUA 


## 目录结构（核心）

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

## 运行（示例）

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


