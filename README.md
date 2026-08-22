# Karlsen Hybrid

**Mine $KLS and serve AI prompts — one binary, one machine.**

Karlsen Hybrid merges the [KarlsenMiner](https://github.com/karlsencoin/karlsen-miner) GPU miner and the KarlsenAI inference worker into a single executable. Your GPU mines Karlsen continuously; when an AI prompt arrives from the network, mining pauses for inference, then resumes automatically. You earn both the block reward and the prompt reward from the same hardware.

> **v7.4.0 — First public release. Windows only. Linux + HiveOS coming in v7.5.0.**

---

## What's inside

| File | Size | Purpose |
|------|------|---------|
| `karlsen-hybrid.exe` | ~7 MB | Miner + AI worker (single binary) |
| `sd-cli.exe` | ~98 MB | Image generation (Stable Diffusion, Vulkan) |
| `ggml-vulkan.dll` | ~52 MB | llama.cpp Vulkan GPU compute backend |
| `mtmd.dll` | ~1.2 MB | Multimodal tokenizer (vision support) |
| `llama.dll` | ~2.3 MB | llama.cpp core runtime |
| `ggml-cpu.dll` | ~750 KB | CPU fallback compute |
| `ggml-base.dll` | ~637 KB | GGML base layer |
| `ggml.dll` | ~65 KB | GGML interface |
| `cudart64_12.dll` | ~561 KB | CUDA runtime (KLS mining on NVIDIA) |

---

## Requirements

| | |
|-|--|
| VRAM | 10 GB minimum for inference roles; 8 GB for mining-only |
| RAM | 16 GB system RAM |
| Disk | 15–30 GB (model files) |
| OS | Windows 10/11 64-bit |
| Driver | AMD Adrenalin (latest) or NVIDIA Game Ready / Studio (latest) |
| Wallet | $KLS address — optional (treasury wallet used as default) |
| CPU | x86-64 with AES-NI for optional XMR mining |

No ROCm SDK or CUDA toolkit needed. All runtime DLLs are bundled.

---

## Quick Start

### 1. Install your GPU driver
AMD: [Adrenalin](https://www.amd.com/en/support) · NVIDIA: [Game Ready Driver](https://www.nvidia.com/drivers)

### 2. Download
Grab `karlsen-hybrid-v7.4.0-windows-vulkan.zip` from the [Releases](../../releases) page and extract it anywhere stable (e.g. `C:\karlsen-hybrid\`). Keep all `.exe` and `.dll` files in the same folder.

### 3. Run
Double-click `karlsen-hybrid.exe`.

On first launch the **setup wizard** walks you through:
- KLS wallet address (or press Enter to use the treasury wallet)
- Mining pool (WoolyPooly default)
- XMR/Monero CPU dual-mining — optional, runs on any x86-64 CPU
- Per-GPU role: what AI tasks each GPU will serve
- Model download — catalog models are fetched automatically

Settings are saved to `karlsen-hybrid.json`. Subsequent launches skip the wizard.

### 4. Pick a role per GPU

```
Role for GPU 0 (RX 6800, 16 GB):
  1. FAST CHAT     (conversational LLM — Qwen3.5-9B)
  2. QUALITY CHAT  (larger model — Qwen3.6-27B)
  3. LAB           (any local .gguf off-catalog)
  4. IMAGE GEN     (Stable Diffusion — Juggernaut XL v9)
  5. MINING ONLY   (no inference)
Select (1-5) [default=1]:
```

Mixed rigs work — assign different roles to different GPUs. The worker connects to the KarlsenAI orchestrator and starts serving prompts.

---

## Modes

Set `mode` in `karlsen-hybrid.json`:

| Mode | Behaviour |
|------|-----------|
| `hybrid` | Mine KLS continuously; pause GPU for inference when a prompt arrives, then resume |
| `inference` | Inference only — no KLS mining |
| `mining` | KLS + XMR mining only — no AI worker |

---

## Earnings

**Mining:** KLS block rewards paid by your pool as usual.  
**Inference:** 100% of the prompt reward goes to your wallet — no platform commission.

Prompt rewards are VRAM-tier based:

| Tier | Min VRAM | Price per prompt |
|------|----------|-----------------|
| Small | 10 GB | ~$0.005 USDT in $KLS |
| Medium | 16 GB | ~$0.010 USDT in $KLS |
| Large | 24 GB+ | ~$0.020 USDT in $KLS |

Image generation: flat 100 $KLS / image.  
Payouts every Sunday 00:00 UTC.

---

## Supported Hardware

### AMD — Inference (Vulkan) + Mining (OpenCL)

| Generation | GFX Target | Cards | Inference | Mining |
|-----------|------------|-------|:---------:|:------:|
| RDNA2 — Navi 21 | gfx1030 | RX 6800, RX 6800 XT, RX 6900 XT, RX 6950 XT | ✅ | ✅ |
| RDNA2 — Navi 22 | gfx1031 | RX 6700 XT, RX 6750 XT | ✅ | ✅ |
| RDNA2 — Navi 23 | gfx1032 | RX 6600 XT, RX 6650 XT | ⚠️ 8 GB limit | ✅ |
| RDNA3 — Navi 31 | gfx1100 | RX 7900 GRE, RX 7900 XT, RX 7900 XTX | ✅ | ✅ |
| RDNA3 — Navi 32 | gfx1101 | RX 7800 XT, RX 7700 XT | ✅ | ✅ |
| RDNA1 — Navi 10 | gfx1010 | RX 5700, RX 5700 XT | ⚠️ untested | ✅ |
| GCN — Polaris / Vega | — | RX 580, RX 590, RX Vega 56/64 | ❌ | ✅ LDS fallback |

> Cards with less than 10 GB VRAM cannot load inference models and will run in mining-only mode automatically.

### NVIDIA — Inference (Vulkan) + Mining (CUDA)

| Generation | Architecture | Cards | Inference | Mining |
|-----------|-------------|-------|:---------:|:------:|
| RTX 20xx | Turing | RTX 2060 Super, 2070, 2070 Super, 2080, 2080 Super, 2080 Ti | ✅ | ✅ |
| CMP | Turing | CMP 90HX | ✅ | ✅ |
| RTX 30xx | Ampere | RTX 3070, 3070 Ti, 3080, 3080 Ti, 3090, 3090 Ti | ✅ | ✅ |
| RTX 40xx | Ada Lovelace | RTX 4070, 4070 Ti, 4080, 4090 | ✅ | ✅ |
| RTX 50xx | Blackwell | RTX 5080, 5090 | ⚠️ untested | ✅ |
| GTX 10xx | Pascal | GTX 1080, 1080 Ti | ❌ | ✅ |

> RTX 2060 (6 GB) and GTX 16xx series are below the 10 GB VRAM floor — mining only.

### CPU — XMR Mining (RandomX)

| | |
|-|-|
| Supported | Any x86-64 CPU with AES-NI (Intel Haswell+, AMD Zen+) |
| Boost | ~+15% hashrate with MSR tweaks on AMD Zen and Intel (requires Administrator) |

---

## Configuration

`karlsen-hybrid.json` is auto-created by the wizard. Key fields:

```json
{
  "wallet": "karlsen:qq...",
  "orchestrator_url": "wss://ai.karlsencoin.org/ws",
  "mode": "hybrid",
  "gpus": [
    { "ordinal": 0, "roles": ["mine", "inference"], "chat_catalog_id": "unsloth/Qwen3.5-9B-GGUF/UD-Q4_K_XL" },
    { "ordinal": 1, "roles": ["mine"] }
  ]
}
```

To re-run the wizard: `karlsen-hybrid.exe --wizard`

---

## Known Limitations (v7.4.0)

- **Windows only** — Linux + HiveOS port in v7.5.0
- **RTX 50xx (Blackwell) / RDNA1 / RDNA4** — Vulkan inference not yet tested; KLS mining works via OpenCL/CUDA
- **Split mode** (multi-GPU for one large model) not yet supported
- **Vision/multimodal** (image-in prompts) — Fast Chat role only; Quality Chat is text-only in this release

---

## Troubleshooting

**"Vulkan device not found"**  
Update your GPU driver. AMD Adrenalin 23.x+ or NVIDIA 536+ required.

**Wizard loops or model download fails**  
The orchestrator catalog is fetched live. If `ai.karlsencoin.org` is unreachable, the wizard skips the download — place the `.gguf` file manually in `models/<model-id>/` and re-run with `--wizard`.

**XMR hashrate shows 0**  
Check `xmr.wallet` in `karlsen-hybrid.json` — must be a valid 95-char Monero address starting with `4` or `8`.

**Windows SmartScreen popup**  
Click **More info → Run anyway**. The binary is not code-signed.

---

## Links

🌐 Service: [ai.karlsencoin.org](https://ai.karlsencoin.org)  
🔗 Website: [karlsencoin.org](https://karlsencoin.org)  
📊 Explorer: [explorer.karlsencoin.org](https://explorer.karlsencoin.org)  
💬 Discord: [discord.gg/QyrvshRBJV](https://discord.gg/QyrvshRBJV)  
📢 Telegram: [t.me/KarlsenTaskForce](https://t.me/KarlsenTaskForce)  
🪙 Buy/sell $KLS: [NonKYC.io — KLS/USDT](https://nonkyc.io)

---

## License

MIT — see [LICENSE](LICENSE).

Built for the Karlsen community.
