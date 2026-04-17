# Use Cases — OpenClaw Native on Android

These are real things built using this native Termux setup. No emulators. No cloud VMs. Just Android phones running OpenClaw.

---

## 1. Android App Automation Agent

**Repo:** [android-automation-agent](https://github.com/Mohd-Mursaleen/android-automation-agent)

An OpenClaw skill that uses the ADB bridge to control any app on the phone — tap, swipe, type, read screen state, launch apps.

What it can do:
- Navigate any Android app autonomously
- Extract UI state and make decisions based on it
- Perform multi-step workflows (e.g., open app → search → tap result → confirm)

**How it works:**  
OpenClaw receives a natural language instruction → skill parses it → issues ADB commands → Android executes them → result is returned to the agent.

> 🔗 [LinkedIn post: OpenClaw does Android automation via ADB skill](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-just-went-%F0%9D%90%92%F0%9D%90%AE%F0%9D%90%9E%F0%9D%90%AB%F0%9D%90%9C%F0%9D%90%9A%F0%9D%90%92%F0%9D%90%9A%F0%9D%90%A2%F0%9D%90%B2%F0%9D%90%9A%F0%9D%90%A7-activity-7446791313733373953-W3Td)

---

## 2. Movie Ticket Booking Bot

OpenClaw agent navigates BookMyShow autonomously:
- Opens the app
- Searches for a movie
- Selects show, seats, and payment method
- Completes the booking

All triggered by a single natural language command.

> 🔗 [LinkedIn post: OpenClaw books a movie ticket on Android](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-books-propoganda-movie-ticket-android-ugcPost-7441544517129875456-t5xM)

---

## 3. Kitchen Surveillance Agent

An old Android phone mounted in the kitchen, running OpenClaw 24/7.  
The camera feed is monitored by the AI agent — it can detect activity, log events, and send alerts.

This use case went viral on LinkedIn — demonstrating that a $50 old Android phone can replace expensive IoT hardware for home surveillance.

> 🔗 [LinkedIn post: OpenClaw builds a kitchen surveillance setup](https://www.linkedin.com/posts/mohd-mursaleen-dev_openclaw-builds-a-kitchen-surveillance-state-ugcPost-7444255147301519360-hXqH)

---

## 4. First Paid Customer — Android Automation

The Android automation setup (ADB bridge + OpenClaw) was used to deliver a real product to a paying customer.  
UI became optional — the agent navigated the app entirely through ADB without any human interaction.

> 🔗 [LinkedIn post: First customer — UI will become optional](https://www.linkedin.com/posts/shubhamkhetan_ui-will-become-optional-priyesh-need-not-activity-7447123595824177153-5ueY)

---

## What Else Can You Build?

With OpenClaw running natively on Android + ADB bridge, the surface area is huge:

- Auto-reply bots for WhatsApp, Instagram DMs
- Automated form filling and data entry
- Scheduled app interactions (daily tasks, reminders)
- Screen monitoring and alerting
- E-commerce price tracking with automatic purchase
- Any workflow you currently do manually on your phone

---

## Setup Required

All use cases above require:
1. [Native OpenClaw setup on Termux](../docs/SETUP.md)
2. [ADB bridge](../docs/ADB-BRIDGE.md) (for app automation use cases)
