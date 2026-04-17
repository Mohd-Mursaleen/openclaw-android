# Use Cases — OpenClaw Native on Android

Real things built using this native Termux setup. No emulators. No cloud VMs. Just Android phones running OpenClaw.

---

## 1. Android App Automation Agent

**Repo:** [android-automation-agent](https://github.com/Mohd-Mursaleen/android-automation-agent)

An OpenClaw skill that uses the ADB bridge to control any app on the phone — tap, swipe, type, read screen state, launch apps. It comes with a `SKILL.md` built specifically for OpenClaw. Drop it in and OpenClaw sets up the entire Android automation agent for you.

How it works:  
OpenClaw receives a natural language instruction → skill parses it → issues ADB commands → Android executes them → result returned to agent.

> 📎 [LinkedIn: OpenClaw does Android automation via ADB skill](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-just-went-%F0%9D%90%92%F0%9D%90%AE%F0%9D%90%9E%F0%9D%90%AB%F0%9D%90%9C%F0%9D%90%9A%F0%9D%90%92%F0%9D%90%9A%F0%9D%90%A2%F0%9D%90%B2%F0%9D%90%9A%F0%9D%90%A7-activity-7446791313733373953-W3Td)

---

## 2. Movie Ticket Booking Bot

OpenClaw agent navigates BookMyShow autonomously — opens the app, searches for a movie, selects show and seats, completes the booking. All from a single natural language command.

> 📎 [LinkedIn: OpenClaw books a movie ticket on Android](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-books-propoganda-movie-ticket-android-ugcPost-7441544517129875456-t5xM)

---

## 3. Kitchen Surveillance Agent

An old Android phone mounted in the kitchen, running OpenClaw 24/7. Camera feed monitored by the AI agent — detects activity, logs events, sends alerts. A $50 old phone replacing expensive IoT hardware.

> 📎 [LinkedIn: OpenClaw builds a kitchen surveillance setup](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-builds-a-kitchen-surveillance-state-ugcPost-7444255147301519360-hXqH)

---

## 4. First Paid Customer — Android Automation

The Android automation setup was used to deliver a real product to a paying customer. UI became optional — the agent navigated the app entirely through ADB with zero human interaction.

> 📎 [LinkedIn: First customer — UI will become optional](https://www.linkedin.com/posts/shubhamkhetan_ui-will-become-optional-priyesh-need-not-activity-7447123595824177153-5ueY)

---

## What Else Can You Build?

- Auto-reply bots for WhatsApp, Instagram DMs
- Automated form filling and data entry
- Scheduled app interactions (daily tasks, reminders)
- Screen monitoring and alerting
- E-commerce price tracking with automatic purchase
- Any workflow you currently do manually on your phone

---

## Setup Required

1. [Native OpenClaw setup on Termux](./SETUP.md)
2. [ADB bridge](./ADB-BRIDGE.md) — for app automation use cases
