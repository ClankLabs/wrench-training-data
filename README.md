# Wrench Training Data

Training data and notebooks for **Wrench** — a family of LoRA fine-tuned models purpose-built for agentic tool calling.

| Model | Base | Score | VRAM | Download |
|-------|------|-------|------|----------|
| **Wrench 35B** | [Qwen3.5-35B-A3B](https://huggingface.co/Qwen/Qwen3.5-35B-A3B) (MoE) | 113/120 (94.2%) | 16GB | [HuggingFace](https://huggingface.co/ClankLabs/Wrench-35B-A3B-Q4_K_M-GGUF) |
| **Wrench 9B** | [Qwen3.5-9B](https://huggingface.co/Qwen/Qwen3.5-9B) (dense) | 105/120 (87.5%) | 8GB | [HuggingFace](https://huggingface.co/ClankLabs/Wrench-9B-Q4_K_M-GGUF) |

## Results

Wrench 35B scores **113/120 (94.2%)** and Wrench 9B scores **105/120 (87.5%)** on our 8-category agentic benchmark — near Claude Sonnet-tier performance running locally on consumer hardware.

| Category | Wrench 35B | Wrench 9B | Claude Sonnet | GPT-4o |
|----------|------------|-----------|---------------|--------|
| Basic Tool Use | 15/15 | 11/15 | 15/15 | 14/15 |
| Multi-Step Tasks | 14/15 | 13/15 | 15/15 | 14/15 |
| Error Recovery | 14/15 | 14/15 | 15/15 | 14/15 |
| Response Quality | 15/15 | 14/15 | 14/15 | 14/15 |
| System Prompt Following | 14/15 | 12/15 | 14/15 | 14/15 |
| Tool Restraint | 14/15 | 14/15 | 15/15 | 13/15 |
| Destructive Action Caution | 14/15 | 14/15 | 14/15 | 14/15 |
| Sub-Agent Delegation | 13/15 | 13/15 | 12/15 | 13/15 |
| **Total** | **113/120** | **105/120** | **~114/120** | **~110/120** |

## What's Here

```
wrench-training-data/
├── datasets/              # 1,147 training examples (JSONL, ShareGPT format)
│   ├── tool-calling.jsonl
│   ├── agent-behavior.jsonl
│   ├── multi-step-chains.jsonl
│   ├── error-recovery.jsonl
│   ├── ... (14 files total)
│   └── tool-restraint.jsonl
├── notebooks/
│   └── train.ipynb        # Full training notebook (RunPod 2x H100)
├── benchmark.md           # 120-prompt scoring rubric (8 categories)
└── README.md
```

## Dataset Breakdown

| Dataset | Examples | Description |
|---------|----------|-------------|
| advanced-coding-2 | 92 | Complex coding tasks with tool use |
| agent-behavior | 92 | General agentic patterns |
| coding-knowledge | 91 | Code comprehension and generation |
| concise-direct | 91 | Terse, no-filler responses |
| debugging-mastery | 91 | Systematic debugging with tools |
| error-recovery | 91 | Graceful handling of tool failures |
| knowing-when-to-stop | 91 | Avoiding unnecessary extra steps |
| multi-step-chains | 91 | Read → edit → verify sequences |
| sub-agents | 91 | Delegating to sub-agents |
| system-prompt-following | 91 | Respecting system prompt constraints |
| tool-calling | 91 | Core ReAct tool-call format |
| always-use-tools | 30 | Use tools for factual queries, never hallucinate |
| multi-step-chains-v2 | 30 | Full multi-turn chains with realistic tool results |
| destructive-action-caution | 20 | Warn before destructive actions |
| tool-restraint | 30 | When NOT to use tools — social responses, bash for system queries |
| **Total** | **1,147** | |

## Training Details

### Wrench 35B
- **Base model:** Qwen3.5-35B-A3B (MoE, 3B active parameters)
- **Method:** LoRA (rank 64, alpha 128) via HuggingFace PEFT + Trainer
- **Hardware:** 2x NVIDIA H100 80GB (RunPod)
- **Training time:** ~1 hour per run
- **Hyperparameters:** batch_size=1, gradient_accumulation=8, 2 epochs, lr=1e-4
- **Output format:** GGUF Q4_K_M (~20GB, runs on 16GB VRAM)

### Wrench 9B
- **Base model:** Qwen3.5-9B (dense)
- **Method:** LoRA (rank 32, alpha 64) via HuggingFace PEFT + Trainer
- **Hardware:** 1x NVIDIA H100 80GB (RunPod)
- **Training time:** ~30 min per run
- **Hyperparameters:** batch_size=1, gradient_accumulation=8, 2 epochs, lr=1e-4
- **Output format:** GGUF Q4_K_M (~5GB, runs on 8GB VRAM)

## Data Format

ShareGPT format — each example is a multi-turn conversation with tool calls:

```json
{
  "conversations": [
    {"from": "system", "value": "You are an AI agent with tools..."},
    {"from": "human", "value": "Read the config file"},
    {"from": "gpt", "value": "I'll read that file.\n\n```tool_call\n{\"name\": \"read_file\", \"arguments\": {\"path\": \"/etc/config.json\"}}\n```"},
    {"from": "tool", "value": "{\"content\": \"...\"}"},
    {"from": "gpt", "value": "Here's what's in the config: ..."}
  ]
}
```

## How to Train Your Own

1. Clone this repo
2. Sign up for [RunPod](https://runpod.io) — rent 2x H100 80GB (~$5/hr)
3. Upload `notebooks/train.ipynb` and the `datasets/` folder
4. Run all cells
5. Download the output GGUF and load it in [Ollama](https://ollama.com) or llama.cpp

See the notebook for full setup instructions including the exact pip install order.

## License

Apache 2.0 — use the data however you want.

## Links

- [Wrench 35B on HuggingFace](https://huggingface.co/ClankLabs/Wrench-35B-A3B-Q4_K_M-GGUF)
- [Wrench 9B on HuggingFace](https://huggingface.co/ClankLabs/Wrench-9B-Q4_K_M-GGUF)
- [Training Data Repository](https://github.com/ClankLabs/wrench-training-data)
- [Clank Gateway](https://github.com/ClankLabs/Clank) — the AI agent gateway Wrench was built for
- [clanklabs.dev/wrench](https://clanklabs.dev/wrench)
