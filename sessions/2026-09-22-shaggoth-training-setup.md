# Session: 2026-09-22 - Shaggoth AI Training Setup

## Goal
Set up DeepSeek-R1 training environment on R510 with monitoring from R410.

## Discoveries

### Hardware Reality Check
- **R510**: 23GB RAM, CPU only (no NVIDIA GPU)
- **R410**: 16GB RAM, CPU only
- DeepSeek-R1 (671B params) requires 80-100GB+ RAM even with 4-bit quantization
- **Finding**: DeepSeek-R1 is NOT viable on R510

### Memory Issues on R510
**Before optimization:**
- Free RAM: 714MB
- In SWAP: 6.1GB (very bad - system thrashing)
- Top consumers:
  - dashboard.py: 2.5GB
  - Elasticsearch: 1.6GB
  - Kibana: 315MB
  - OpenCode: 194MB

**After stopping services:**
- Free RAM: 3.8GB
- Available: 19GB
- Still 5.7GB in SWAP

**Critical services kept running:**
- ✅ P3 Lending (p3lending.space → port 5173) - 107MB
- ✅ Shaggoth serve (port 8420) - 576MB  
- ✅ Cloudflare tunnel (config.yml) - 29MB

### Model Testing Results
1. **Qwen2.5-7B-Instruct** - FAILED: Timed out/crashed during loading
2. **Phi-3-mini (3.8B)** - FAILED: Dumped core, out of memory

**Root cause**: Even with 19GB available, models fail due to:
- Memory fragmentation
- 5.7GB still in SWAP
- CPU-only quantized loading instability on this hardware
- Contiguous memory requirements

## Alternative Models Considered

### Best for 23GB RAM (if hardware worked):
1. **Qwen2.5-7B-Instruct** - Excellent reasoning, but timeouts
2. **Llama 3.2-8B** - Good generalist
3. **Mistral-7B-v0.3** - Efficient
4. **Phi-3-mini (3.8B)** - Smaller, but still failed

### Cloud Training Options (FREE):
1. **Google Colab** - T4 GPU (16GB), ~15 hours/week
2. **Kaggle Notebooks** - P100/T4 GPU, 30 hours/week ⭐ BEST
3. **Lightning.ai** - Free tier with GPU

## Decision: Use Cloud Training

**Recommendation:**
1. Train models on **Kaggle** (free GPU, 30hrs/week)
2. Deploy trained adapters to R510 for inference
3. Monitor from R410 with command center

**Rationale:**
- R510 CPU training is 100-1000x slower than GPU
- Even loading models for testing fails on R510
- Cloud training is free and proven to work
- R510 can handle inference with pre-trained models
- Dashboard display belongs on R410 anyway

## Scripts Created

1. **prepare_r510_for_training.sh** - Frees 3.5GB RAM safely
2. **restore_r510_services.sh** - Restores stopped services
3. **test_qwen.py** - Tests Qwen2.5-7B (failed)
4. **test_phi3_mini.py** - Tests Phi-3-mini (failed)

## Next Steps

1. Set up Kaggle notebook for LoRA training
2. Use existing datasets in ~/AI/datasets/
   - shaggoth_general.jsonl (1,000 examples)
   - shaggoth_reasoning.jsonl (548 examples)
3. Train Qwen2.5-7B-Instruct with LoRA on Kaggle
4. Download trained adapter to R510
5. Set up inference endpoint
6. Move dashboard.py to R410
7. Create distributed monitoring setup

## Files/Locations

**R510:**
- AI project: ~/AI/
- Datasets: ~/AI/datasets/
- Venv: ~/AI/venv/
- Cloudflare config: ~/.cloudflared/config.yml

**R410:**
- AI project: ~/AI/ (synced)
- Will host: dashboard.py, monitoring TUI

## Infrastructure Notes

**Cloudflare Tunnels on R510:**
- Main tunnel (config.yml): Serves P3 Lending ✅ KEEP
  - p3lending.space → localhost:5173
  - api.p3lending.space → localhost:5001
  - ai.relayapp.pro → localhost:8420 (Shaggoth)

**Services Architecture:**
- R510: Heavy compute (Shaggoth API, P3 backend)
- R410: Display/monitoring (dashboard, TUI)
- Cloud: Training (Kaggle/Colab)
