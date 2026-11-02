# Karlsen Hybrid

**Mine $KLS and serve AI prompts — one binary, one machine.**

Karlsen Hybrid merges the [KarlsenMiner](https://github.com/karlsencoin/karlsen-miner) GPU miner and the KarlsenAI inference worker into a single program. Your GPUs mine Karlsen and serve AI tasks for [ai.karlsencoin.org](https://ai.karlsencoin.org) from the same hardware: the model and the mining DAG stay resident in VRAM together, mining pauses only while a prompt is being answered, then resumes automatically. You earn the mining reward and the inference reward.

> **Latest release:** see the [Releases](../../releases/latest) page. Windows and Linux / HiveOS builds ship together for every version.

---

## Features

- **Mining:** KarlsenHashV2 on AMD (OpenCL) and NVIDIA (CUDA) GPUs, powered by the KarlsenMiner v3 Rust engine.
- **AI inference:** chat models (llama.cpp, Vulkan backend) and image generation (Stable Diffusion, Juggernaut XL v9).
- **Per-GPU roles:** each card can mine, serve AI, or do both.
- **Two-GPU split:** large models can be split across two cards.
- **LAB:** load your own GGUF model; the chat template is read from the model file.
- **Streamed answers** with a live "Thinking" panel for reasoning models.
- **Verified downloads:** catalog models are checked with SHA-256.

---

## Downloads

| File | Platform |
|------|----------|
| `karlsen-hybrid-vX.Y.Z-windows-vulkan.zip` | Windows 10/11 x64 |
| `karlsen-hybrid-X.Y.Z.tar.gz` | Linux (Ubuntu 22.04+) and HiveOS custom miner |
| `SHA256SUMS.txt` | Checksums for both packages |

All runtime libraries are bundled. No ROCm SDK or CUDA toolkit is needed.

---

## Requirements

| | |
|-|--|
| OS | Windows 10/11 x64, Ubuntu 22.04 or newer, or HiveOS |
| GPU | AMD (RDNA2 and newer recommended) or NVIDIA (RTX 20xx / Turing and newer) |
| VRAM | Enough for the chosen model on top of the mining DAG; cards under 10 GB: mining only, or use the standalone [KarlsenMiner](https://github.com/karlsencoin/karlsen-miner/releases) |
| Driver | Current AMD or NVIDIA driver with Vulkan support (NVIDIA mining on Linux / HiveOS: driver 580 or newer) |
| Disk | Several GB free per AI model |
| Wallet | A $KLS address |

---

## Quick start: Windows

1. Download the Windows zip from [Releases](../../releases/latest) and extract it somewhere with enough free disk space (for example `C:\karlsen-hybrid\`). Keep all files in the same folder.
2. Double-click `karlsen-hybrid.exe`.
3. The setup wizard asks for your wallet, your pool and a role and model for each GPU, then downloads the selected models into the `models\` folder.
4. Settings are saved to `karlsen-hybrid.json`; later launches skip the wizard. To run it again: `karlsen-hybrid.exe --wizard`.

## Quick start: Ubuntu (without HiveOS)

Extract the archive and run the setup wizard once:

```
tar xzf karlsen-hybrid-X.Y.Z.tar.gz
cd karlsen-hybrid
sudo ./wizard.sh
```

To start Karlsen Hybrid again later with your saved settings:

```
cd ~/karlsen-hybrid
sudo LD_LIBRARY_PATH=./lib ./karlsen-hybrid
```

Use the folder you extracted the archive into. Models already downloaded are not downloaded again. To change wallet, pool or per-GPU presets, run `sudo ./wizard.sh` again. Stop the program with `Ctrl+C`.

If you start it over SSH, run it inside `tmux` so it keeps running after you disconnect:

```
tmux new -s karlsen
sudo LD_LIBRARY_PATH=./lib ./karlsen-hybrid
```

Detach with `Ctrl+B` then `D`, reattach later with `tmux attach -t karlsen`.

## Quick start: HiveOS

Flight sheet: Miner = **Custom**, Installation URL = the `karlsen-hybrid-X.Y.Z.tar.gz` link from the release, Hash algorithm = `karlsenhashv2`, Wallet = your KLS address.

The first start stops with a "not configured" banner. SSH into the rig once:

```
cd /hive/miners/custom/karlsen-hybrid
sudo ./wizard.sh
```

Pick a role and model per GPU, then restart the miner in the HiveOS web UI. The flight sheet wallet and pool override the wizard values on every start. Logs: `/var/log/miner/karlsen-hybrid/karlsen-hybrid.log`.

---

## Pools

The wizard offers WoolyPooly (default, `stratum+ssl://pool.woolypooly.com:3132`), Cedric-Crispin (SSL and non-SSL) and NushyPool, or any custom stratum URL / local stratum bridge.

---

## Earnings

**Mining:** KLS rewards are paid by your pool as usual.
**Inference:** your rig earns KLS for every task it serves. Inference rewards are paid out every **Sunday at 00:00 UTC**.

---

## Troubleshooting

**Windows SmartScreen popup**
Click **More info → Run anyway**. The binary is not code-signed yet.

**Missing DLL / library error**
Keep every file from the package next to the program (on Linux, the `lib/` folder too).

**No GPUs found / Vulkan errors**
Update your GPU driver. AI inference uses Vulkan; mining uses OpenCL (AMD) and CUDA (NVIDIA).

**A model does not fit / out of memory**
Pick a smaller model in the wizard, or set that GPU to mining only.

---

## Links

🌐 Service: [ai.karlsencoin.org](https://ai.karlsencoin.org)
🔗 Website: [karlsencoin.org](https://karlsencoin.org)
📊 Explorer: [explorer.karlsencoin.org](https://explorer.karlsencoin.org)
💬 Discord: [discord.gg/QyrvshRBJV](https://discord.gg/QyrvshRBJV)
📢 Telegram: [t.me/KarlsenCoin](https://t.me/KarlsenCoin)

---

## License

See [LICENSE](LICENSE) in the release packages.

Built for the Karlsen community.
