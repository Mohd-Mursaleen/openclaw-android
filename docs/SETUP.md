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

### 2.1 Prevent Android From Killing Termux

```bash
termux-wake-lock
```

Acquires a wake lock so Android doesn't suspend Termux when the screen turns off. Critical for any long-running process.

### 2.2 Update Package Lists and Upgrade All Packages

```bash
pkg update && pkg upgrade -y
```

Fetches latest package metadata and upgrades everything. The `-y` flag auto-confirms prompts. Do this before installing anything.

### 2.3 Install OpenSSH

```bash
pkg install openssh -y
```

Installs the SSH server so you can connect to this device remotely from your laptop.

### 2.4 Start the SSH Server

```bash
sshd
```

Starts the SSH daemon. Termux's SSH server runs on port **8022** (not the standard 22).

### 2.5 Get Your Username

```bash
whoami
```

Prints your Termux username (e.g., `u0_a123`). You'll need this for the SSH command.

### 2.6 Get Your Phone's IP Address

```bash
ifconfig
```

Look for the `wlan0` section and note the `inet` address (e.g., `192.168.1.5`). This is your phone's local IP.

### 2.7 Set an SSH Password

```bash
passwd
```

Sets a password for your Termux user. You'll enter this when SSHing in.

### 2.8 Connect From Your Laptop

```bash
ssh -p 8022 <username>@<wlan-ip>
```

Replace `<username>` with the output of `whoami` and `<wlan-ip>` with the IP from `ifconfig`.  
All commands from here on are run in this SSH session.

---

## Part 3 — Install Node.js and Git

### 3.1 Install Node.js

```bash
pkg install nodejs -y
```

Installs Node.js natively in Termux. OpenClaw is a Node.js application.

### 3.2 Install Git

```bash
pkg install git -y
```

Installs Git. Required for some npm packages that pull from GitHub during install.

---

## Part 4 — Bionic Bypass (The Core Fix)

This is the key difference between this guide and every other one.

Android uses **Bionic libc** instead of the standard glibc. This causes two Node.js crashes that break OpenClaw:

1. `os.networkInterfaces()` — crashes or returns null on Android
2. `os.homedir()` — returns a wrong path (not Termux's actual home)

We fix both by patching Node's `os` module at startup, injected via `NODE_OPTIONS`.

### 4.1 Create the Bypass Directory

```bash
mkdir -p ~/.openclaw
```

Creates a hidden config directory in your Termux home.

### 4.2 Write the Bypass Script

```bash
cat > ~/.openclaw/bionic-bypass.js << 'EOF'
const os = require('os');

// Fix 1: os.networkInterfaces() crash on Android
const _ni = os.networkInterfaces.bind(os);
os.networkInterfaces = function() {
  try {
    const r = _ni();
    if (r && Object.keys(r).length > 0) return r;
  } catch(e) {}
  return { lo: [{ address: '127.0.0.1', netmask: '255.0.0.0', family: 'IPv4', mac: '00:00:00:00:00:00', internal: true, cidr: '127.0.0.1/8' }] };
};

// Fix 2: homedir() returns wrong path in Termux
const _hd = os.homedir.bind(os);
os.homedir = function() {
  return process.env.HOME || _hd();
};
EOF
```

This script wraps the two broken Node.js `os` functions with safe Android-compatible versions.

### 4.3 Inject the Bypass Into Every Node Process

```bash
echo 'export NODE_OPTIONS="--require /data/data/com.termux/files/home/.openclaw/bionic-bypass.js --max-old-space-size=5632"' >> ~/.bashrc
```

- `--require` — loads the bypass script before any other code runs
- `--max-old-space-size=5632` — raises Node's V8 heap limit to ~5.5GB to prevent memory-related crashes on the phone

### 4.4 Apply the Changes to Current Session

```bash
source ~/.bashrc
```

Reloads your shell config so `NODE_OPTIONS` takes effect immediately without restarting Termux.

---

## Part 5 — Install OpenClaw

### 5.1 Install the OpenClaw CLI

```bash
npm install -g openclaw@2026.4.5 --ignore-scripts
```

- `-g` — installs globally so `openclaw` is available as a command
- `--ignore-scripts` — skips postinstall scripts that try to call `systemd` or write to paths that don't exist on Android. Without this flag, the install fails.

### 5.2 Install PM2 (Process Manager)

```bash
npm install -g pm2
```

PM2 keeps OpenClaw running in the background, auto-restarts it on crash, and persists it across Termux restarts.

---

## Part 6 — Install Messaging Integrations (Optional)

These packages add support for various messaging platforms. Install only what you need.

```bash
npm i -g @grammyjs/runner
```
Enables concurrent Telegram update processing.

```bash
npm i -g @grammyjs/transformer-throttler
```
Adds rate limiting for Telegram API calls.

```bash
npm install -g grammy
```
Telegram bot framework.

```bash
npm install -g @slack/web-api
```
Slack integration.

```bash
npm install -g @buape/carbon
```
Discord bot framework.

```bash
npm install -g @larksuiteoapi/node-sdk
```
Lark / Feishu integration.

---

## Part 7 — Create the Startup Script

### 7.1 Write start.sh

```bash
cat > ~/start.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
source ~/.bashrc
openclaw gateway
EOF
```

This script sources `.bashrc` first — which loads the bionic bypass — before starting OpenClaw. This order is important. Without it, the bypass won't be active when OpenClaw starts.

### 7.2 Make It Executable

```bash
chmod +x ~/start.sh
```

Grants execute permission to the script.

---

## Part 8 — Run OpenClaw with PM2

### 8.1 Start the Gateway

```bash
pm2 start ~/start.sh --name "gateway"
```

Starts OpenClaw gateway as a named background process managed by PM2.

### 8.2 Save the Process List

```bash
pm2 save
```

Persists the PM2 process list so you can restore it after a Termux restart using `pm2 resurrect`.

---

## Part 9 — Onboard

```bash
openclaw onboard
```

Runs OpenClaw's interactive onboarding — connects your gateway to the OpenClaw cloud, sets up your agent config, and gets your instance registered.

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
