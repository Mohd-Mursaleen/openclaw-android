# Run OpenClaw Natively on Android — No Root, No Ubuntu, No proot

<p align="center">
  <img src="./public/IMG_0342.JPG" width="320" alt="OpenClaw running natively on Android" />
</p>

<p align="center">
  <a href="https://openclaw.ai">OpenClaw</a> •
  <a href="./SETUP.md">Setup Guide</a> •
  <a href="./ADB-BRIDGE.md">ADB Bridge</a> •
  <a href="./USE-CASES.md">Use Cases</a> •
  <a href="./LOCAL-LLM.md">Local LLM</a>
</p>

---

## Why This Repo Is Different

Every other guide tells you to run Ubuntu inside Termux via `proot-distro` before installing OpenClaw.  
It works — but it's slow, bloated, and laggy.

This repo documents running **OpenClaw directly on Termux's native environment** — no proot, no Ubuntu, no container layer.

| | proot + Ubuntu (common method) | Native Termux (this repo) |
|---|---|---|
| Boot time | 10–30 seconds | ~2 seconds |
| Extra RAM | ~300MB+ for Ubuntu layer | Minimal |
| OpenClaw speed | Slow, laggy | Fast, snappy |
| Setup complexity | High | Low |
| Root required | No | No |

The only thing you need on top of Termux is a **small Node.js patch** that fixes Android's Bionic libc incompatibilities. That's it.

> Discovered, tested, and documented by [@Mohd-Mursaleen](https://github.com/Mohd-Mursaleen). No official docs cover this method.

---

## Full Setup Guide

👉 **[SETUP.md](./SETUP.md)** — complete step-by-step installation, from F-Droid to `openclaw onboard`

---

## Android Automation

Once OpenClaw is running, you can connect it to the Android OS via ADB and control any app with natural language.

I built a dedicated skill for this — **[android-automation-agent](https://github.com/Mohd-Mursaleen/android-automation-agent)**.  
It comes with a `SKILL.md` file specifically created for OpenClaw. Drop it into OpenClaw and it sets up the Android automation agent for you — ADB bridge, preferences system, multi-step task decomposition, everything.

👉 **[ADB-BRIDGE.md](./ADB-BRIDGE.md)** — how to connect Termux to Android OS via ADB

---

## Kitchen Surveillance

An old Android phone mounted in the kitchen, running OpenClaw 24/7. The camera feed is monitored by the AI agent — detects activity, logs events, sends alerts.

> 📎 [LinkedIn: OpenClaw builds a kitchen surveillance setup](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-builds-a-kitchen-surveillance-state-ugcPost-7444255147301519360-hXqH)

---

## Local LLM on Android

Running Gemma 4 on the phone via Google's **LiteRT-LM** (GPU + CPU) — not llama.cpp (CPU only, unusably slow).  
Exposes the model on a local port so OpenClaw can use it as a fully offline LLM backend.

👉 **[LOCAL-LLM.md](./LOCAL-LLM.md)** — full breakdown, architecture, and why llama.cpp doesn't work

---

## Use Cases

Real things built with this setup:

- 📱 Android app automation via ADB + AI
- 🎟️ Movie ticket booking bot (BookMyShow, autonomously)
- 📷 Kitchen surveillance agent
- 🔄 Always-on AI gateway on an old phone

👉 **[USE-CASES.md](./USE-CASES.md)** — all use cases with LinkedIn posts and details

---

## Related

- [android-automation-agent](https://github.com/Mohd-Mursaleen/android-automation-agent) — ADB-powered Android automation skill built for OpenClaw
- [OpenClaw](https://openclaw.ai) — the AI agent gateway this repo runs
- [Blog: Running Local LLM on Android](https://geekymd.me/blog/running-local-llm-on-android)

---

## Keywords

`openclaw` `android` `termux` `ai-agent` `no-root` `no-proot` `llm` `agent-gateway` `android-automation` `adb` `nodejs-android` `termux-setup` `self-hosted-ai` `mobile-ai` `gemma` `litert` `local-llm-android`

---

<p align="center">If this helped you, drop a ⭐ — it helps others find this.</p>
