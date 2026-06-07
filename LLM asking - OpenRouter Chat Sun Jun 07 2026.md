# I want best audio compression(best quali

**User - --**

I want best audio compression(best quality in very small size). i don't much care about ffmpeg or specific platform support as i want to use it to archive some audio files.

I already found:
https://github.com/hubertsiuzdak/snac
https://bellard.org/tsac/

which seems awesome. but i want to know if there are better and newer alternates. and after that compare all the available candidates. for example what is the difference between `TSAC` and `SNAC`(the two codecs that i mentioned above) and the alternates that you provided?  

**Assistant - --**

<tool_call>
<function=openrouter_web_search>
<parameter=query>
"BigCodec" "WavTokenizer" "SACodec" "BSCodec" "UniSRCodec" neural audio codec 2025 2026 archiving
</parameter>
</function>
</tool_call>

**Assistant - --**

Let me research the latest audio compression options for you.

Now I have comprehensive data. Let me compile the full, detailed comparison.

---

# 🎵 Ultra-Low Bitrate Audio Codecs: Full Comparison Guide (2024–2026)

You've picked two excellent starting points. Let me map the entire landscape and give you a thorough technical breakdown.

---

## 🗺️ The Codec Landscape

The field is split into two broad families:

| Family | Approach | Best For |
|---|---|---|
| **Traditional psychoacoustic** | Opus, AAC, MP3 — exploit hearing limits | Compatibility, real-time streaming |
| **Neural / AI codecs** | Deep learning + VQ/RVQ — learn the structure of audio itself | Extreme compression, archival, AI pipelines |

You're looking at the **neural codec** space. Here's everyone worth knowing:

---

## 🧑‍🔬 All Major Candidates Explained

<details>
<summary><strong>📌 1. TSAC — Fabrice Bellard (2024) [Your Pick]</strong></summary>

**GitHub / Page:** https://bellard.org/tsac/

**What it is:**
Created by Fabrice Bellard (the author of FFmpeg, QEMU, and qjs). TSAC takes **Descript Audio Codec (DAC)** as its backbone, extends it for stereo, then adds a **Transformer language model on top of the discrete codes** to predict and further compress them — essentially arithmetic coding the token stream with a learned prior.

**Key specs:**
- Sample rate: **44.1 kHz** (mono and stereo)
- Bitrates: ~**5.5 kbps mono / 7.5 kbps stereo** (default highest quality), down to ~2.5–3 kbps at lower settings
- Both models are **quantized to 8 bits per parameter**
- **Deterministic** decompression regardless of GPU/CPU hardware — a crucial archival property
- Supports up to 127 channels
- GPU (NVIDIA CUDA) strongly recommended; CPU supported but slow
- Input: WAV/raw `.sw` natively; other formats via FFmpeg
- **Closed binary** (compiled Linux/Windows executables), source not fully open

**Strengths:**
- ✅ Highest compression ratio of any publicly usable codec (stereo music at ~7 kbps!)
- ✅ Deterministic decode — identical output every run, on any hardware (archival gold)
- ✅ 44.1 kHz stereo support
- ✅ Subjectively impressive quality for the bitrate
- ✅ Ready-to-use binary, no Python/ML setup needed

**Weaknesses:**
- ❌ Slow on CPU (may be ~0.5x real-time on a mid-range CPU)
- ❌ Not open source (binary-only)
- ❌ Doesn't scale well to higher bitrates — artifacts don't cleanly disappear at e.g. 20+ kbps
- ❌ Fixed to 44.1 kHz input
- ❌ Some reported occasional "hallucinations" (wrong words in speech at low quality settings)
- ❌ GPU requires NVIDIA with CUDA 12.x and 4+ GB VRAM

</details>

---

<details>
<summary><strong>📌 2. SNAC — Hubert Siuzdak (2024) [Your Pick]</strong></summary>

**GitHub:** https://github.com/hubertsiuzdak/snac | **Paper:** arXiv:2410.14411

**What it is:**
Multi-Scale Neural Audio Codec. A clever extension of the standard RVQ approach where different quantizers operate at **different temporal resolutions** (hierarchical/multi-scale tokens). Coarse tokens cover long time spans, fine tokens cover short ones. This mirrors the natural structure of audio.

**Key specs (pretrained models):**

| Model | Bitrate | Sample Rate | Use Case |
|---|---|---|---|
| `snac_24khz` | **0.98 kbps** | 24 kHz | 🗣️ Speech |
| `snac_32khz` | **1.9 kbps** | 32 kHz | 🎸 Music / SFX |
| `snac_44khz` | **2.6 kbps** | 44.1 kHz | 🎸 Music / SFX |

- Parameters: 19.8M (24 kHz) / 54.5M (32/44 kHz)
- **Mono only** (all models)
- MIT License, open source Python
- Runs on CUDA GPU (PyTorch)

**Strengths:**
- ✅ Extremely low bitrate, especially for speech (~1 kbps!)
- ✅ Outperforms EnCodec and DAC at comparable or lower bitrates (per MUSHRA tests)
- ✅ Open source, MIT licensed
- ✅ Multi-scale design excellent for AI/LLM audio generation downstream tasks
- ✅ Very compact model (19.8M params for speech)

**Weaknesses:**
- ❌ **Mono only** — no stereo support
- ❌ Requires Python + PyTorch + CUDA (no simple binary)
- ❌ Not as competitive for music compared to speech (DAC beats it on music at similar bitrate in some tests)
- ❌ Last updated Nov 2024 — development appears stalled
- ❌ Archival concern: decode depends on model weights being available

</details>

---

<details>
<summary><strong>📌 3. DAC — Descript Audio Codec (2023, still widely used)</strong></summary>

**GitHub:** https://github.com/descriptinc/descript-audio-codec | **Paper:** NeurIPS 2023

**What it is:**
The current "reference standard" for neural audio codecs. Improved upon EnCodec with: low-dimensional RVQ projection to prevent codebook collapse, Snake activation functions (better for periodic signals), multi-scale STFT discriminator losses. **TSAC is built on top of DAC.**

**Key specs:**
- Sample rates: 16 kHz, 24 kHz, **44.1 kHz**
- Bitrate: scalable from **~0.75 kbps to 8 kbps** (via number of RVQ codebooks: 1–12)
- **Mono and Stereo** (44.1 kHz model)
- ~74M parameters
- MIT License, Python/PyTorch

**Strengths:**
- ✅ General purpose — works on speech, music, and all audio
- ✅ Excellent quality at 8 kbps (near-transparent for speech, very good for music)
- ✅ Flexible bitrate via codebook count
- ✅ Widely supported — drop-in for EnCodec in many AI pipelines
- ✅ Open source, active ecosystem

**Weaknesses:**
- ❌ At very low bitrates (1–2 kbps), newer models like SNAC, WavTokenizer, DualCodec beat it
- ❌ Requires GPU for practical use
- ❌ Large model (~74M params)

</details>

---

<details>
<summary><strong>📌 4. WavTokenizer (2024, ICLR 2025)</strong></summary>

**Paper:** ICLR 2025 | **GitHub:** Available

**What it is:**
Extreme single-codebook compression. WavTokenizer encodes 24 kHz audio (speech, music, general) into **only 40 or 75 tokens per second** — just **one VQ codebook**, achieving 0.48–0.9 kbps.

**Key specs:**
- 24 kHz, mono
- **0.48 kbps** (40 tokens/s) or **0.9 kbps** (75 tokens/s)
- Single codebook with 4096 entries
- ~103M parameters
- Uses inverse-FFT upsampling decoder (vocoder-style)

**Strengths:**
- ✅ 0.9 kbps WavTokenizer-large outperforms DAC at 9 kbps on UTMOS (subjective speech quality!)
- ✅ Extremely compact token stream — ideal for LLM-based audio generation
- ✅ Good for speech and general audio

**Weaknesses:**
- ❌ **Poor music quality** — single codebook can't capture music's complexity (confirmed by AudioCodecBench)
- ❌ Not suitable for archival of music at these bitrates
- ❌ Mono only
- ❌ Relies heavily on perceptual tricks; objective waveform metrics (SI-SDR, PESQ) are not great

</details>

---

<details>
<summary><strong>📌 5. Stable Codec / TAAE — Stability AI (2024, arxiv 2411.19842)</strong></summary>

**GitHub:** https://github.com/Stability-AI/stable-codec | **Paper:** arXiv:2411.19842

**What it is:**
A Transformer-based (not CNN/SEANet like most others) speech codec. Uses **Finite Scalar Quantization (FSQ)** instead of RVQ. Scaled up to a large parameter count. Achieves **state-of-the-art speech quality** at extremely low bitrates.

**Key specs:**
- 16 kHz, **speech only**
- Bitrates: **400 bps, 700 bps, 1000 bps** (yes, sub-1 kbps!)
- Uses FlashAttention (GPU required, no CPU inference currently)
- Flexible post-hoc FSQ bottleneck decomposition

**Strengths:**
- ✅ **Best-in-class** for low-bitrate speech (outperforms Mimi, SemantiCodec, DAC in MUSHRA at ≤700 bps)
- ✅ SOTA MUSHRA subjective scores at 400 and 700 bps — results "close to ground truth"
- ✅ Generalizes to unseen languages

**Weaknesses:**
- ❌ **Speech only** — not suitable for music or general audio archival
- ❌ Hard requirement for FlashAttention — no CPU inference
- ❌ 16 kHz only (telephone-quality frequency range)
- ❌ Large transformer model
- ❌ Not useful for you unless you're archiving speech exclusively

</details>

---

<details>
<summary><strong>📌 6. FlowDec — Meta AI / Facebook Research (ICLR 2025)</strong></summary>

**GitHub:** https://github.com/facebookresearch/flowdec | **Paper:** arXiv:2503.01485

**What it is:**
A diffusion/flow-based **post-filter codec**. It trains a base codec (non-adversarially, no GAN), then applies a **conditional flow matching model** as a stochastic post-filter to enhance perceptual quality. 48 kHz, full-band, general audio.

**Key specs:**
- **48 kHz**, stereo/mono, general audio
- Bitrates: **4.5 kbps or 7.5 kbps**
- 6 neural network evaluations (NFE) at inference (fast for a diffusion model)
- ICLR 2025 paper, MIT licensed

**Strengths:**
- ✅ Full-band 48 kHz general audio (music-friendly!)
- ✅ Better FAD (Fréchet Audio Distance) than DAC
- ✅ More natural-sounding harmonic structures in music
- ✅ Avoids GAN artifacts — stochastic generation fills in missing detail
- ✅ No adversarial training instability

**Weaknesses:**
- ❌ **Stochastic** — output is not bit-exact/deterministic across runs (problematic for archival!)
- ❌ Minimum 4.5 kbps — not ultra-low bitrate
- ❌ More complex inference pipeline than simpler codecs
- ❌ Slower than pure feed-forward codecs

</details>

---

<details>
<summary><strong>📌 7. Mimi — Kyutai (2024)</strong></summary>

**What it is:**
A low-frame-rate streaming codec built for real-time speech-to-speech systems (used in Kyutai's Moshi model). Combines semantic distillation (HuBERT-guided first codebook) with acoustic RVQ.

**Key specs:**
- 24 kHz, causal/streaming
- **1.1 kbps**, 12.5 frames/sec, 8 RVQ codebooks
- ~39M parameters

**Strengths:**
- ✅ Streamable / real-time causal
- ✅ Low latency
- ✅ Semantic + acoustic tokens combined

**Weaknesses:**
- ❌ Audio quality lower than WavTokenizer, DualCodec at same bitrate in tests
- ❌ Speech-focused
- ❌ Not ideal for music archival

</details>

---

<details>
<summary><strong>📌 8. HILCodec (2024)</strong></summary>

**What it is:** Lightweight streaming neural codec using depthwise separable convolutions and a variance-constrained design. Outperforms EnCodec and AudioDec. Good general-purpose streaming option but not pushing the ultra-low bitrate frontier.

</details>

---

## 🥊 Head-to-Head: TSAC vs. SNAC vs. The Field

Here is the comprehensive comparison you asked for:

| Feature | **TSAC** | **SNAC** | **DAC** | **WavTokenizer** | **Stable Codec** | **FlowDec** |
|---|---|---|---|---|---|---|
| **Release** | Apr 2024 | Oct 2024 | 2023 | 2024 | Nov 2024 | Mar 2025 |
| **Sample rate** | 44.1 kHz | 24/32/44 kHz | 16/24/44 kHz | 24 kHz | 16 kHz | 48 kHz |
| **Min. bitrate** | ~2.5 kbps | **0.98 kbps** | ~0.75 kbps | **0.48 kbps** | **0.4 kbps** | 4.5 kbps |
| **Stereo** | ✅ Yes | ❌ No | ✅ Yes | ❌ No | ❌ No | ✅ Yes |
| **Music quality** | ✅ Good | ⚠️ Fair | ✅ Good | ❌ Poor | ❌ N/A | ✅ Good |
| **Speech quality** | ✅ Good | ✅ Excellent | ✅ Good | ✅ Excellent | ✅ Outstanding | ✅ Good |
| **Deterministic** | ✅ **Yes** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ❌ **No** |
| **Open source** | ❌ Binary only | ✅ MIT | ✅ MIT | ✅ Open | ✅ Apache 2 | ✅ MIT |
| **CPU usable** | ⚠️ Slow | ⚠️ Slow | ⚠️ Slow | ⚠️ Slow | ❌ No | ⚠️ Slow |
| **Ease of use** | ✅ CLI binary | ⚠️ Python | ⚠️ Python | ⚠️ Python | ⚠️ Python | ⚠️ Python |
| **General audio** | ✅ Yes | ⚠️ Limited | ✅ Yes | ⚠️ Speech best | ❌ Speech only | ✅ Yes |
| **Archival safe?** | ✅ **Best** | ⚠️ Model dependency | ⚠️ Model dependency | ⚠️ Model dependency | ⚠️ Model dependency | ❌ Stochastic |
| **Architecture** | DAC + Transformer LM | Multi-scale RVQ GAN | Improved RVQGAN | Single-VQ GAN | Transformer + FSQ | Flow matching post-filter |

---

## 🔑 Key Differences: TSAC vs. SNAC (Deep Dive)

| Dimension | **TSAC** | **SNAC** |
|---|---|---|
| **Core approach** | Takes DAC's discrete codes, then runs a **Transformer language model** to predict & re-encode them with arithmetic coding — "compress the compressed" | Extends RVQ with **multi-temporal-resolution quantizers** — coarser tokens at low Hz, fine tokens at high Hz |
| **Bitrate floor** | ~2.5–3 kbps stereo | ~0.98 kbps mono (speech) |
| **Stereo** | ✅ Native joint stereo | ❌ Mono only |
| **Music** | Better (designed for music) | Weaker (RVQ struggles at very low bitrate for music) |
| **Speech at ~1kbps** | Not designed for this range | ✅ Excellent — SNAC 0.98 kbps beats EnCodec 6 kbps in MUSHRA |
| **The "magic trick"** | Transformer predicts the next DAC token given context → arithmetic-codes the residual → enormous savings | Multi-scale hierarchy → coarse tokens encode long-range structure cheaply |
| **Why TSAC is lower bit/s for music** | Because music has long-range structure a Transformer can exploit | RVQ doesn't exploit temporal context between frames |
| **Format longevity** | Better — single binary, deterministic | Worse — depends on PyTorch model weights |
| **Who made it** | Fabrice Bellard (solo genius, author of FFmpeg) | Hubert Siuzdak et al. (academic research) |

**In plain English:**
- Use **TSAC** if you want to archive **stereo music** at the absolute lowest possible bitrate (~7 kbps) with a ready-made tool and guaranteed reproducible decoding.
- Use **SNAC** if you want to archive **mono speech** at sub-1 kbps, or if you need the token representation for ML/AI use.

---

## 🏆 My Recommendation for Pure Archival Use

<details open>
<summary><strong>🎵 If archiving Music (stereo, full-quality)</strong></summary>

**Best pick: TSAC** at its highest quality setting (`-q 12`)

It's the only neural codec with:
1. Native stereo at 44.1 kHz
2. Ready-to-use binary
3. Deterministic decode
4. Bitrate that's actually competitive (~7 kbps stereo, music quality that's surprisingly good)

> ⚠️ **Archival caveat for ALL neural codecs:** The decode is lossy and psychoacoustically reconstructed. If you care about waveform fidelity (archival in the true sense), neural codecs are the **wrong tool**. Use **FLAC** for true lossless archival. Neural codecs are for extreme space-constrained storage where some perceptual quality loss is acceptable.

> If you must pick one for space-constrained music archiving: **TSAC** or **DAC at 44.1 kHz @ 8 kbps**.

</details>

<details open>
<summary><strong>🗣️ If archiving Speech (podcasts, lectures, voice recordings)</strong></summary>

**Best picks in order:**
1. **Stable Codec (TAAE)** @ 700 bps — outstanding quality for 16 kHz speech (but Python + FlashAttention required)
2. **SNAC 24 kHz** @ 0.98 kbps — excellent speech, easiest to use of the open ML codecs, consistently beats much higher-bitrate classical codecs
3. **TSAC mono** @ ~5 kbps — excellent for speech too, easiest to use of all

</details>

<details open>
<summary><strong>🎸 General audio (mixed content)</strong></summary>

**Best pick: DAC @ 44.1 kHz / 8 kbps** — the most battle-tested, general-purpose, high-quality option. Or **FlowDec @ 7.5 kbps** for 48 kHz with more natural harmonic quality (but non-deterministic).

</details>

---

## ⚠️ Critical Archival Warning for All Neural Codecs

> Neural audio codecs are **lossy and generative** — the decoder is not reconstructing your original signal; it is **hallucinating a plausible audio signal** from the compressed tokens. This means:
> - Different model versions may decode differently
> - If the model weights become unavailable, your archive is **undecodable**
> - No byte-level bit-exact reproduction is possible
>
> **Recommendation:** Always keep the **original lossless file** (FLAC/WAV) alongside your neural codec archive. Use neural codecs as a space-efficient listening/distribution copy, not as your sole archive.

TSAC partially mitigates this by being a **single compiled binary** with embedded weights — but if Bellard's site goes down and no one has archived the binary, your `.tsac` files become useless.

---

## 📊 Quick Bitrate Quality Reference

At what bitrate does each codec sound "acceptable"?

```
Codec              | ~Transparent | Acceptable | Degraded
-------------------|-------------|------------|----------
FLAC (lossless)    | ∞            | ∞          | never
Opus               | ~64 kbps    | ~32 kbps   | <16 kbps
DAC (44.1 kHz)     | ~8 kbps     | ~5 kbps    | <2 kbps
TSAC (stereo)      | ~7 kbps*    | ~5 kbps    | <3 kbps
SNAC (speech)      | ~1 kbps     | ~0.98 kbps | <0.5 kbps
WavTokenizer       | ~0.9 kbps†  | ~0.5 kbps† | (music fails)
Stable Codec       | ~0.7 kbps‡  | ~0.4 kbps‡ | n/a
FlowDec            | ~7.5 kbps   | ~4.5 kbps  | n/a
```

`*` For music — neural artifacts still present, not truly transparent
`†` Speech only; music quality is poor at any bitrate for WavTokenizer
`‡` Speech only at 16 kHz

**Assistant - --**

### Reasoning

The user wants the best audio compression for highest quality in the smallest size, for archiving audio files. They don't care much about FFmpeg or platform support. They've found SNAC (https://github...The user is looking for the best audio compression for archiving: highest quality at smallest size (very low bitrate, high perceptual quality), specifically for general audio files (music, etc.), usin...First, the user wants the best audio compression for archiving: highest quality at smallest size. They're interested in neural codecs like SNAC and TSAC, and want newer alternates, then compare them.

**Best options for archiving (highest perceptual quality at ultra-low bitrates)** are currently centered on neural audio codecs. These outperform traditional codecs (Opus, AAC, etc.) by large margins at bitrates below ~8–10 kbps.

### Top Candidates
Here are the strongest options, focused on general audio (music + mixed content):

| Codec | Bitrate (44.1 kHz) | Stereo? | Key Strengths | Status (2026) | Best For |
|-------|---------------------|---------|---------------|---------------|----------|
| **TSAC** (Bellard) | ~5.5 kbps mono<br>~7.5 kbps stereo | Yes | Excellent practical quality, reproducible, ready binaries | Mature, easy to use | Archiving stereo music |
| **SNAC** | 2.6 kbps (music)<br>1.9 kbps (32 kHz) | No (mono) | Best quality/bitrate on music in published tests | Open models + code | Lowest bitrate mono |
| **SwitchCodec** | 2.67–5.33 kbps (VBR) | Yes | Adaptive experts → better than DAC/EnCodec | Research (2025–26 papers) | High-fidelity general audio |
| **GAC** (Generative) | 0.175–0.375 kbps | Yes | Extreme compression via generative decoder (1.8B params) | Research prototype | Ultra-extreme compression (experimental) |
| **UniSRCodec** / **BSCodec** | ~0.5–3.8 kbps | Yes | Strong single-codebook or band-split designs | Newer research | Unified audio at low rates |

### SNAC vs TSAC (Your Two Finds)
- **SNAC** (Multi-Scale RVQ) uses quantizers at different temporal resolutions (coarse tokens at ~10–14 Hz, finer at higher rates). This is more efficient than classic RVQ (EnCodec/DAC). It achieves very high MUSHRA scores on music at **2.6 kbps** (44.1 kHz) and even beats higher-bitrate DAC at 1.9 kbps (32 kHz). Limitation: currently **mono only**.
- **TSAC** starts from Descript Audio Codec (DAC) and adds a Transformer stage + stereo support. It reaches good perceptual quality at **~7.5 kbps stereo** (a 3.5-minute stereo track → ~192 KiB). It is more practical (pre-built binaries, deterministic/reproducible decoding even across hardware). Quality is strong but generally not as bitrate-efficient as SNAC on mono music.

**Key difference**: SNAC wins on raw efficiency/bitrate for mono music. TSAC wins on practicality + native stereo for archiving real music files.

### Newer/Better Alternatives (2025–2026)
Newer research has closed the gap or surpassed both in specific areas:

- **SwitchCodec** (Residual Experts VQ) — Uses a shared quantizer + sparsely activated expert quantizers. This decouples bitrate from codebook size and adapts to content complexity. It beats DAC and EnCodec at 2.67–5.33 kbps on objective + MUSHRA tests for general audio. Strong candidate if implemented.
- **GAC** (Generative Audio Compression) — Shifts burden to a massive generative decoder. Achieves intelligible/high-fidelity results at **0.175–0.275 kbps** (thousands× compression). Very promising for archiving but requires a huge decoder model and may introduce generative artifacts.
- Single-codebook approaches (**UniSRCodec**, **WavTokenizer** derivatives) — Simpler token streams, good for downstream tasks, competitive quality at ~0.5–1 kbps when paired with strong decoders.
- Band-split designs (**BSCodec**) — Process frequency bands separately; competitive quality at roughly half the bitrate of DAC.

**Current reality for archiving**:
- **Practical winner** — **TSAC** (stereo + ready-to-use + solid quality at ~7.5 kbps).
- **Lowest bitrate winner** — **SNAC** (if you can accept mono or convert to mono) or experimental **GAC** / **SwitchCodec**.
- Nothing dramatically better than these two has reached the same level of usability + published music results yet.

### Recommendation
1. Start with **TSAC** — best balance for stereo archiving right now.
2. Test **SNAC** on mono or downmixed material for even smaller files.
3. Watch **SwitchCodec** and **GAC** as they mature (they have the most promising technical direction for future ultra-low-bitrate archiving).

All of these are lossy neural codecs. For true archival you would still keep a lossless copy (FLAC) alongside the compressed version.