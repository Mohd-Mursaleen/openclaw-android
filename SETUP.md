# Complete Setup Guide — OpenClaw Native on Termux

This guide walks you through every command, one step at a time, with context for what each does.

---

## Part 1 — Android Prerequisites

### 1.1 Install F-Droid

Download and install [F-Droid](https://f-droid.org) — a trusted open-source app store.  
We use F-Droid because the Termux version on Google Play Store is outdated and abandoned.

### 1.2 Enable Incompatible Repos

In F-Droid → Settings → enable **"Allow incompatible versions"**.  
This is required to install Termux:API from its own repo alongside the main F-Droid repo.

### 1.3 Install Termux and Termux:API

From F-Droid, install both:
- **Termux** — the terminal emulator and Linux environment
- **Termux:API** — exposes Android system APIs (camera, sensors, storage) to Termux

### 1.4 Grant Full Permissions

Go to Android Settings → Apps → Termux and Termux:API.  
Grant: Storage, Notifications, Display over other apps.  
OpenClaw needs these to execute device-level actions.

---

## Part 2 — SSH Setup (Do This First)

Setting up SSH before anything else means you can do the entire remaining setup from your laptop keyboard — no typing on the phone screen.

**In Termux on your phone:**

```bash
termux-wake-lock
pkg update && pkg upgrade -y
pkg install openssh -y
sshd
whoami    # note your username
ifconfig  # note your wlan0 IP
passwd    # set a password
```

**From your laptop:**

```bash
ssh -p 8022 <username>@<wlan-ip>
```

All commands from here on are run in this SSH session.

---

## Part 3 — Install Node.js and Git

```bash
pkg install nodejs git -y
```

Installs Node.js (OpenClaw runs on it) and Git (required by some npm packages during install).

---

## Part 4 — Bionic Bypass (The Core Fix)

Android uses **Bionic libc** instead of standard glibc. This causes two Node.js crashes that break OpenClaw:

1. `os.networkInterfaces()` — crashes or returns null on Android
2. `os.homedir()` — returns a wrong path

This patch fixes both at startup, injected via `NODE_OPTIONS` so every Node process gets it automatically.

```bash
mkdir -p ~/.openclaw
cat > ~/.openclaw/bionic-bypass.js << 'EOF'
const os = require('os');

const _ni = os.networkInterfaces.bind(os);
os.networkInterfaces = function() {
  try {
    const r = _ni();
    if (r && Object.keys(r).length > 0) return r;
  } catch(e) {}
  return { lo: [{ address: '127.0.0.1', netmask: '255.0.0.0', family: 'IPv4', mac: '00:00:00:00:00:00', internal: true, cidr: '127.0.0.1/8' }] };
};

const _hd = os.homedir.bind(os);
os.homedir = function() {
  return process.env.HOME || _hd();
};
EOF

echo 'export NODE_OPTIONS="--require /data/data/com.termux/files/home/.openclaw/bionic-bypass.js --max-old-space-size=5632"' >> ~/.bashrc
source ~/.bashrc
```

> `--max-old-space-size=5632` raises Node's V8 heap to ~5.5GB to prevent OOM crashes on the phone.

---

## Part 5 — Install OpenClaw and PM2

```bash
npm install -g openclaw@2026.4.5 --ignore-scripts
npm install -g pm2
```

- `--ignore-scripts` skips postinstall scripts that break on Android (they assume `systemd` exists)
- PM2 keeps OpenClaw running in the background and auto-restarts it on crash

---

## Part 6 — Install Messaging Integrations

These are required for the OpenClaw gateway to work. Run all in one go:

```bash
npm i -g @grammyjs/runner @grammyjs/transformer-throttler grammy @slack/web-api @buape/carbon @larksuiteoapi/node-sdk
```

This installs support for Telegram, Slack, Discord, and Lark — all the platforms OpenClaw gateway can connect to.

---

## Part 7 — Create the Startup Script

```bash
cat > ~/start.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
source ~/.bashrc
openclaw gateway
EOF

chmod +x ~/start.sh
```

The script sources `.bashrc` first so the bionic bypass is loaded before OpenClaw starts. Order matters here.

---

## Part 8 — Run OpenClaw with PM2

```bash
pm2 start ~/start.sh --name "gateway"
pm2 save
```

Starts OpenClaw as a named background process. `pm2 save` persists it so you can restore with `pm2 resurrect` after a Termux restart.

---

## Part 9 — Onboard

```bash
openclaw onboard
```

Connects your gateway to OpenClaw, sets up your agent config, and registers your instance.

---

## Useful PM2 Commands

```bash
pm2 list              # see all running processes
pm2 logs gateway      # tail OpenClaw logs
pm2 restart gateway   # restart the gateway
pm2 stop gateway      # stop the gateway
pm2 resurrect         # restore saved processes after Termux restart
```

---

## Troubleshooting

| Issue | Fix |
|---|---|
| `openclaw: command not found` | Run `source ~/.bashrc` |
| SSH connection refused | Make sure `sshd` is running, check port 8022 |
| Node OOM crash | Increase `--max-old-space-size` in `.bashrc` |
| PM2 not found after restart | Run `npm install -g pm2` again, then `pm2 resurrect` |
| `networkInterfaces` error | Confirm bionic-bypass.js exists at `~/.openclaw/bionic-bypass.js` |
