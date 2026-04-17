# OpenClaw on Android — Native Termux Setup (No Ubuntu, No proot)

Most people install OpenClaw on Android by running Ubuntu inside Termux via proot-distro.  
It works — but it's slow, heavy, and laggy.

This is a different approach. **OpenClaw running directly on Termux's native environment.**  
No proot. No Ubuntu. Just Termux + a small Node.js patch to fix Android's Bionic libc quirks.

> Tested and built by me. No official docs cover this method.

---

## Why Native Termux?

|                         | proot + Ubuntu           | Native Termux (this guide) |
| ----------------------- | ------------------------ | -------------------------- |
| Boot time               | 10–30s                   | ~2s                        |
| RAM overhead            | ~300MB+ for Ubuntu layer | Minimal                    |
| OpenClaw responsiveness | Slow, laggy              | Fast, snappy               |
| Complexity              | High                     | Low                        |

---

## Prerequisites

- Android phone (any, no root needed)
- F-Droid installed
- "Allow incompatible versions" enabled in F-Droid settings
- Termux + Termux:API installed from F-Droid
- Full permissions granted to both apps

---

## Step 1 — SSH Into Your Device

Do this first. Everything after is easier from your laptop keyboard.

```bash
termux-wake-lock         # prevent Android from killing Termux
pkg update && pkg upgrade -y
pkg install openssh -y
sshd                     # start SSH server on port 8022
whoami                   # note your username
ifconfig                 # note your WLAN IP
passwd                   # set a password
```

From your laptop:

```bash
ssh -p 8022 <username>@<wlan-ip>
```

---

## Step 2 — Install Node.js & Git

```bash
pkg install nodejs -y
pkg install git -y
```

---

## Step 3 — Bionic Bypass (The Key Fix)

Android's Bionic libc causes two Node.js crashes:

1. `os.networkInterfaces()` crashes or returns null
2. `os.homedir()` returns a wrong path

This patch fixes both at Node startup, injected via `NODE_OPTIONS`.

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

---

## Step 4 — Install OpenClaw

```bash
npm install -g openclaw@2026.4.5 --ignore-scripts
npm install -g pm2
```

`--ignore-scripts` skips postinstall scripts that break on Android.

---

## Step 5 — Install Messaging Integrations

```bash
npm i -g @grammyjs/runner
npm i -g @grammyjs/transformer-throttler
npm i -g @larksuiteoapi/node-sdk
npm install -g @buape/carbon
npm install -g @slack/web-api
npm install -g grammy
```

---

## Step 6 — Run OpenClaw Gateway with PM2

```bash
cat > ~/start.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
source ~/.bashrc
openclaw gateway
EOF

chmod +x ~/start.sh
pm2 start ~/start.sh --name "gateway"
pm2 save
```

---

## Step 7 — Onboard

```bash
openclaw onboard
```

---

## More Coming Soon

- Android Automation Agent setup
- Kitchen Surveillance use case with OpenClaw
- Multi-device agent fleet on old Android phones

---

## Author

Built by [Mohd Mursaleen](https://github.com/your-handle)  
If this helped you, drop a ⭐
