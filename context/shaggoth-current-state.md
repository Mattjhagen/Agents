# Shaggoth AI - Current State (2026-09-22)

## System Architecture

```
┌─────────────────────┐
│   Kaggle (Cloud)    │
│   GPU Training      │
│   - P100/T4 16GB    │
│   - 30 hrs/week     │
│   - LoRA training   │
└──────────┬──────────┘
           │ Download trained model
           ↓
┌─────────────────────┐      ┌─────────────────────┐
│   R510 (Server)     │      │   R410 (Monitor)    │
│   23GB RAM, CPU     │◄────►│   16GB RAM, CPU     │
│                     │ SSH  │                     │
│ Running:            │      │ Will run:           │
│ ✓ P3 Lending:5173   │      │ • Dashboard (TUI)   │
│ ✓ Shaggoth:8420     │      │ • Training monitor  │
│ ✓ CF Tunnel         │      │ • Display station   │
│                     │      │                     │
│ Stopped (freed 3.5GB):     │                     │
│ • Dashboard (2.5GB) │──────┤ • Moved here        │
│ • Elasticsearch     │      │                     │
│ • Kibana            │      │                     │
│ • OpenCode          │      │                     │
└─────────────────────┘      └─────────────────────┘
```

## Training Data

**Location:** `~/AI/datasets/`

| File | Examples | Size | Purpose |
|------|----------|------|---------|
| shaggoth_general.jsonl | 1,000 | 10.5MB | General Shaggoth knowledge |
| shaggoth_reasoning.jsonl | 548 | 372KB | Reasoning tasks |
| shaggoth_mixed.jsonl | 405 | 3.4MB | Mixed topics |
| shaggoth_shaggoth.jsonl | 5 | 2.5KB | Core Shaggoth identity |
| **Total** | **1,958** | **14.3MB** | **Ready for training** |

**Archive:** `~/AI/shaggoth-datasets.tar.gz` (3.6MB compressed)

## Models Evaluated

| Model | Parameters | RAM Required | Status | Notes |
|-------|-----------|--------------|--------|-------|
| DeepSeek-R1 | 671B | 80-100GB | ❌ Too large | Original target, unfeasible |
| Qwen2.5-7B-Instruct | 7B | 4-6GB | ⚠️ Timeout | Crashes on R510, works on GPU |
| Phi-3-mini | 3.8B | 2-3GB | ❌ Crashed | Still too heavy for R510 |
| **Qwen2.5-7B on Kaggle** | 7B | GPU | ✅ **Selected** | Training in cloud |

## Current Services (R510)

**Running (Must Keep):**
- P3 Lending Protocol: http://localhost:5173 → p3lending.space
- Shaggoth API: http://localhost:8420 → ai.relayapp.pro
- Cloudflare Tunnel: ~/.cloudflared/config.yml
- Node/Vite: 107MB
- Shaggoth serve: 576MB
- CF tunnel: 29MB

**Stopped (Freed Memory):**
- Dashboard: Was 2.5GB → Moving to R410
- Elasticsearch: 1.6GB
- Kibana: 315MB
- OpenCode: 194MB
- **Total freed: 4.6GB**

**Memory Status:**
- Before: 714MB free, 6.1GB swap
- After: 3.8GB free, 5.7GB swap, 19GB available

## Network Configuration

**Tailscale IPs:**
- R510: 100.103.3.35
- R410: (current machine)

**Cloudflare Tunnels (R510):**
- p3lending.space → localhost:5173 ✅
- api.p3lending.space → localhost:5001 ✅
- ai.relayapp.pro → localhost:8420 (Shaggoth) ✅
- shaggoth.relayapp.pro → localhost:8420 ✅

## Scripts Created

**R510 (~/AI/):**
- `prepare_r510_for_training.sh` - Frees memory safely
- `restore_r510_services.sh` - Restarts stopped services
- `test_qwen.py` - Tests Qwen2.5-7B (failed)
- `test_phi3_mini.py` - Tests Phi-3-mini (failed)
- `kaggle-shaggoth-training.ipynb` - Complete training notebook

**R410 (~/AI/):**
- `setup_r410_monitoring.sh` - Sets up monitoring station
- `monitor_r510_training.py` - Remote training monitor TUI
- `start_monitoring.sh` - Quick launcher

**Documentation:**
- `KAGGLE_SETUP_GUIDE.md` - Step-by-step Kaggle instructions
- `DEPLOYMENT_PLAN.md` - Complete deployment roadmap
- `CLAUDE.md` - Project context for Claude

## Next Actions (In Order)

### 1. Kaggle Training (Now)
- [ ] Go to: https://www.kaggle.com/code/mattyhagen/shaggoth-coder-fine-tune
- [ ] Upload datasets from ~/AI/datasets/
- [ ] Enable GPU (P100 or T4)
- [ ] Add dataset to notebook
- [ ] Copy training code
- [ ] Run training (2-3 hours)
- [ ] Download shaggoth-qwen-lora.zip

### 2. Deploy to R510 (After training)
- [ ] Transfer: `scp shaggoth-qwen-lora.zip r510:~/AI/`
- [ ] Extract on R510
- [ ] Create inference endpoint
- [ ] Test with Shaggoth queries
- [ ] Integrate with port 8420 API

### 3. Setup R410 Monitoring
- [ ] Run: `~/AI/setup_r410_monitoring.sh`
- [ ] Test remote monitoring
- [ ] Start dashboard display
- [ ] Verify R510 connection

### 4. Production Integration
- [ ] Benchmark new model vs base
- [ ] Collect user feedback
- [ ] Gather more training data
- [ ] Plan next training iteration

## Key Decisions Made

1. **Use cloud training instead of R510** - R510 can't handle training, only inference
2. **Qwen2.5-7B-Instruct over DeepSeek-R1** - Manageable size, good reasoning
3. **Kaggle over Colab** - Better limits (30hrs vs 15hrs/week)
4. **Keep P3 Lending running** - Production service, can't interrupt
5. **Move dashboard to R410** - Frees 2.5GB, belongs on display machine
6. **LoRA not full fine-tune** - 50-200MB adapters vs 14GB full model

## Known Issues

1. **R510 swap usage** - 5.7GB still in swap, causes instability
2. **Memory fragmentation** - Models fail even with 19GB available
3. **CPU-only limitations** - Can't train locally, inference only
4. **Dashboard on R510** - Currently stopped, needs to move to R410

## Success Metrics

After deployment, verify:
- [ ] Model loads on R510 with <8GB RAM
- [ ] Inference time <5 seconds per query
- [ ] Responses show Shaggoth-specific knowledge
- [ ] Better reasoning than base model
- [ ] Integration with existing API works
- [ ] P3 Lending unaffected
- [ ] R410 monitoring functional

## References

- Kaggle notebook: https://www.kaggle.com/code/mattyhagen/shaggoth-coder-fine-tune
- Agents repo: https://github.com/Mattjhagen/Agents
- Qwen2.5 docs: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
- LoRA paper: https://arxiv.org/abs/2106.09685
