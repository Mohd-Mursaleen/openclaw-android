# Local LLM on Android — Gemma 4 via LiteRT-LM (Not llama.cpp)

<p align="center">
  <img src="./public/IMG_0724.JPG" width="320" alt="Local LLM running on Android via LiteRT-LM" />
</p>

Running a local LLM on Android sounds simple. It isn't. Here's what actually works — and why the common approach fails.

---

## The Common Approach: llama.cpp

Most guides tell you to run `llama.cpp` in Termux. It works — but it's **CPU-only**. On a phone:
- Gemma 4 runs at ~2–3 tokens/second
- The phone heats up immediately
- Unusable for any real agent workflow

This is the approach used by most "OpenClaw on Android" repos. Technically correct, practically obsolete.

---

## What Actually Works: Google LiteRT-LM

Google's **AI Edge Gallery** app runs the same Gemma 4 model on the same phone — fast.  
The difference: **LiteRT-LM** uses GPU + CPU together via Android's NNAPI and GPU delegate. llama.cpp was CPU only.

Same phone. Same model. Completely different experience.

---

## The Approach: Expose LiteRT-LM as a Local API

```
OpenClaw (Termux)
      ↓  HTTP API call
LiteRT-LM App (localhost:8080)
      ↓
  Gemma 4 (GPU-accelerated)
      ↓
  Android GPU/NPU
```

Build a small Android app that:
1. Runs Gemma 4 via LiteRT-LM (GPU-accelerated)
2. Exposes it on a local port (`localhost:8080`)
3. Termux makes API calls to it
4. OpenClaw uses it as its LLM backend — fully offline

One phone. One brain. Zero cloud.

---

## Why This Matters for OpenClaw

- OpenClaw runs **fully offline** — no API keys, no internet
- Image processing via ADB screenshots passed directly to the local model
- Nothing leaves the device
- Unlocks air-gapped agent deployments

---

## Hardware Reality Check

| Device tier | LiteRT-LM experience |
|---|---|
| Flagship (Snapdragon 8 Gen 2+, Dimensity 9200+) | Fast, usable |
| Mid-range (Snapdragon 7s, Dimensity 7000) | Slow but functional |
| Budget / old phones | Too slow, high heat |

llama.cpp on any of the above = slow regardless of chip.

---

## llama.cpp vs LiteRT-LM

| | llama.cpp in Termux | LiteRT-LM App (this approach) |
|---|---|---|
| GPU acceleration | ❌ CPU only | ✅ GPU + CPU via NNAPI |
| Speed on Gemma 4 | ~2–3 tok/s | Usable |
| Works offline | ✅ | ✅ |
| Usable with OpenClaw | ❌ Too slow | ✅ |
| Status | Obsolete for agent use | Active experiment |

---

## Full Write-up

> 📎 [Blog: I Tried Running a Local LLM on My Android](https://geekymd.me/blog/running-local-llm-on-android)

> 📎 [LinkedIn: Local LLM on my phone — the dream, the reality](https://www.linkedin.com/posts/mohd-mursaleen-dev_local-llm-on-my-phone-the-dream-the-reality-ugcPost-7450530069896171521-jb1o)

---

> Built and written by [@Mohd-Mursaleen](https://github.com/Mohd-Mursaleen). Original research — no one else has documented this approach for OpenClaw.
