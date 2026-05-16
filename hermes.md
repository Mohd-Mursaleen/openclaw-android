# I switched to Hermes

![Hermes Agent](public/hermes.png)

> **Update — May 2026:** I've moved my mobile agent stack off OpenClaw and onto **Hermes on Termux**.
> This repo stays up as a reference, but Hermes is what I'd recommend to anyone starting today. Why below.

---

## Why I Switched

I spent weeks trying to make OpenClaw behave on an old Redmi.

It ran — barely. Patched binaries. Broken bridges. Manual rebuilds every other commit. Every new agent meant a fresh blocker.

The device wasn't the problem. The platform was.

So I ripped it out and started over on Hermes.

---

## Why Hermes Is Better

### 1. Native Termux compatibility

Hermes runs on Termux out of the box. No patches, no shims, no custom toolchains. Install and go.

Mobile shells are fragile — the fewer custom layers between you and the kernel, the longer your setup actually lives.

- Setup time on OpenClaw: **~3 days**
- Setup time on Hermes: **under 2 hours**

### 2. Multi-agent as a first-class primitive

This was the one that mattered most.

Hermes treats agent orchestration as a primitive, not an afterthought bolted onto a single-agent core. Spawn one, spawn seven — same loop, same message contract.

I tried building a swarm on OpenClaw for months and never got past two agents without something breaking. On Hermes the seventh one came up in an evening.

### 3. No blockers

Zero runtime crashes in two weeks of uptime.

The runtime stopped being the bottleneck. The ideas became the bottleneck.

That flip is worth more than any feature comparison.

---

## Get Started With Hermes

Official Termux setup guide:
👉 **[hermes-agent.nousresearch.com/docs/getting-started/termux](https://hermes-agent.nousresearch.com/docs/getting-started/termux)**

---

## How I Actually Use It

Full writeup of my 7-agent collective running on a $100 OnePlus 6:
👉 **[geekymd.me/blog/hermes-on-termux](https://geekymd.me/blog/hermes-on-termux)**

---

## About This Repo

The original OpenClaw-on-Android setup scripts and notes are still in the history. Feel free to reference them, fork them, or use them as a starting point for your own experiments.

But if you're choosing a mobile agent runtime in 2026, start with Hermes.

---

— Mohd Mursaleen
[geekymd.me](https://geekymd.me) · [@dev_mursaleen](https://x.com/dev_mursaleen)

---
