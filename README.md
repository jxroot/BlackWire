<div align="center">

# ⚡ BlackWire

### Silent link. Full control.

[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/Frontend-React_19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![Kotlin](https://img.shields.io/badge/Agent-Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white)](https://kotlinlang.org/)
[![Docker](https://img.shields.io/badge/Deploy-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)

**Web dashboard + powerful backend + headless Android agent**


</div>

## ✨ Overview

**BlackWire** is a full-stack platform for remote management of multiple Android devices.  
A headless agent runs on each phone and stays connected automatically — you control everything from a single web dashboard.

<div align="center">
<img src="https://raw.githubusercontent.com/jxroot/BlackWire/refs/heads/main/images/dashboard.png">
</div>


> 🔒 **Note:** This is commercial software. A valid license is required to use it.

---

## 🚀 Features

<table>
<tr>
<td width="50%" valign="top">

### 📊 Dashboard & Management
- Live online / offline device status
- Advanced search and filtering
- Real-time updates via WebSocket
- Battery, RAM, and storage metrics
- Custom APK builder from the panel

</td>
<td width="50%" valign="top">

### 📱 Remote Control
- Live screen monitoring
- File and app management
- Remote shell terminal
- Camera and microphone access
- Real-time GPS tracking

</td>
</tr>
<tr>
<td width="50%" valign="top">

### 💬 Communications & Data
- SMS read and send
- Contacts and call history
- Notification logging
- User activity timeline
- Toggleable data-collection modules

</td>
<td width="50%" valign="top">

### ⚙️ System & Security
- Permission management
- Quick toggles — airplane mode, Wi-Fi, and more
- JWT authentication
- Headless agent — auto-starts on install and reboot
- Watchdog for connection stability

</td>
</tr>
</table>

---

## 🛠️ Tech Stack

| Layer | Technologies |
|:--|:--|
| **Frontend** | React 19 · Vite · TypeScript · Tailwind CSS · Zustand · TanStack Query |
| **Backend** | FastAPI · Pydantic · SQLAlchemy · Uvicorn |
| **Agent** | Kotlin · OkHttp WebSocket · Foreground Service |
| **Database** | PostgreSQL 16 |
| **Real-time** | WebSocket (`/ws/dashboard` · `/ws/device`) |
| **Deploy** | Docker Compose |

---

## 🏗️ Architecture

```mermaid
flowchart TB

    %% =========================================================
    %% CLIENT / ACCESS
    %% =========================================================
    subgraph CLIENT["CLIENT & ACCESS TIER"]
        SPA["Web Dashboard
        React 19 · Vite · TypeScript
        Tailwind · Zustand · TanStack Query · Monaco"]

        NGX["Edge Gateway
        nginx :80
        SPA · /api · /ws proxy"]

        PUBAPK["Public APK Distribution
        GET /d/{token}"]

        TGBOT["Telegram Operator Bot"]

        AUTH["Access & Authentication
        JWT · 2FA · Scoped API Keys"]
    end


    %% =========================================================
    %% CONTROL PLANE
    %% =========================================================
    subgraph CORE["CONTROL PLANE · FastAPI :8000"]

        HTTP["HTTP API
        381 endpoints · 71 route modules"]

        WSD["Dashboard WebSocket
        /ws/dashboard"]

        WSA["Device WebSocket
        /ws/device"]

        WSCDP["CDP Live
        /ws/cdp-live/{session_id}"]

        WSRB["Remote Browser
        /ws/remote-browser/{session_id}"]

        DISP["Command Dispatcher
        requestId · waiter · queue runner"]

        BUS["Event Broadcaster
        fan-out / subscriptions"]

        ENROLL["Agent Enrollment
        enroll · challenge · token"]

        HOOKS["Hook Runner
        35 trigger events"]

        TASKS["Task Runner / SDK
        on_register · on_online
        first_connection"]

        TL["Timeline / Event Recorder"]
    end


    %% =========================================================
    %% API DOMAINS
    %% =========================================================
    subgraph DOMAINS["API DOMAIN LAYER"]

        D1["Auth & Access
        auth · accounts · security
        dashboard-users · api-keys"]

        D2["Fleet & Device Core
        devices · capabilities · modules
        tags · groups · permissions
        actions · queue · realtime
        timeline · telemetry"]

        D3["Screen / Control / CDP
        screen · cdp"]

        D4["Media & Sensors
        camera · microphone · location
        now-playing · IR"]

        D5["Communications
        SMS · contacts · call logs
        notifications · device commands"]

        D6["Files & Transfer
        files · bulk transfer
        file tracking · storage"]

        D7["Shell & Runtime
        terminal · Termux · Shizuku
        ADB · WebView · SOCKS
        PRoot · QEMU"]

        D8["Automation & AI
        tasks · assignments · runs
        hooks · scripts · OCR · assistant"]

        D9["Build & Distribution
        builds · APK requests
        public APK"]

        D10["Platform Services
        settings · Telegram bot"]
    end


    %% =========================================================
    %% INFRASTRUCTURE
    %% =========================================================
    subgraph INFRA["INFRASTRUCTURE"]

        PG[("PostgreSQL 16
        SQLAlchemy · Alembic")]

        REDIS[("Redis
        Pub/Sub · Rate Limits
        Waiters / transient state")]

        SERVICES["Background Workers
        queue runner
        Telegram runner
        stale-device sweeper
        retention sweeper
        CDP keepalive"]

        SECRETS["Runtime Configuration
        secrets volume
        trusted proxy CIDRs
        DNS proxy"]
    end


    %% =========================================================
    %% BUILD PIPELINE
    %% =========================================================
    subgraph BUILD["BUILD & DISTRIBUTION PIPELINE"]

        GRADLE["APK Builder
        Gradle · Docker · per-flavor"]

        BINDER["APK Binder
        apktool merge
        XAPK split merge
        DEX / ABI merge · re-sign"]

        TBOOT["Termux Bootstrap Builder
        per-host package"]

        ART["Artifact Manager
        build history
        install status
        public distribution
        enrollment lifecycle"]
    end


    %% =========================================================
    %% ANDROID FLEET
    %% =========================================================
    subgraph FLEET["ANDROID FLEET · KOTLIN AGENT"]

        AG["Android Agent
        Kotlin · OkHttp WebSocket
        foreground execution
        watchdog · FCM wake"]

        PRIV["Execution / Privilege Paths
        Accessibility
        Device Admin
        Shizuku / ADB
        Local / USB / LAN transports"]

        RUNTIME["On-Device Runtime
        Termux · PRoot · QEMU
        Python Task SDK · OCR"]

        CONTROL["Device Control
        screen · input
        camera · microphone
        location · IR
        WebRTC / HVNC"]

        MODULES["Telemetry & Optional Modules
        input tracking
        keyboard watcher
        notifications
        screen reader
        timeline · logs
        shell / privileged modules"]
    end


    %% =========================================================
    %% CLIENT → SERVER
    %% =========================================================

    SPA --> NGX

    NGX -->|"HTTPS /api/*"| HTTP
    NGX <-->|"WSS"| WSD

    PUBAPK -->|"HTTPS"| HTTP
    TGBOT -->|"API / internal integration"| HTTP
    AUTH --> HTTP


    %% =========================================================
    %% CONTROL PLANE
    %% =========================================================

    HTTP --> DOMAINS

    WSD --> BUS

    WSA --> DISP
    DISP --> WSA
    WSA --> BUS

    ENROLL --> WSA

    HOOKS --> DISP
    TASKS --> DISP

    WSCDP --> D3
    WSRB --> D7

    DISP --> TL
    BUS --> TL


    %% =========================================================
    %% DATA
    %% =========================================================

    DOMAINS --> PG
    TL --> PG

    DISP <--> REDIS
    BUS <--> REDIS

    SERVICES --> PG
    SERVICES <--> REDIS

    HTTP --> SECRETS


    %% =========================================================
    %% SERVER ↔ AGENT
    %% =========================================================

    WSA <-->|"Persistent WSS
    registration · heartbeat
    commands · results · events"| AG

    ENROLL -.->|"enrollment flow"| AG

    AG --> PRIV
    AG --> RUNTIME
    AG --> CONTROL
    AG --> MODULES


    %% =========================================================
    %% BUILD
    %% =========================================================

    SPA -.->|"build request"| GRADLE

    GRADLE --> BINDER
    TBOOT --> BINDER
    BINDER --> ART

    ART -.->|"install / enrollment"| AG


    %% =========================================================
    %% STYLES
    %% =========================================================

    classDef client fill:#61DAFB22,stroke:#61DAFB
    classDef core fill:#00968822,stroke:#009688
    classDef data fill:#4169E122,stroke:#4169E1
    classDef fleet fill:#7F52FF22,stroke:#7F52FF
    classDef build fill:#FF980022,stroke:#FF9800

    class SPA,NGX,PUBAPK,TGBOT,AUTH client
    class HTTP,WSD,WSA,WSCDP,WSRB,DISP,BUS,ENROLL,HOOKS,TASKS,TL,D1,D2,D3,D4,D5,D6,D7,D8,D9,D10 core
    class PG,REDIS,SERVICES,SECRETS data
    class AG,PRIV,RUNTIME,CONTROL,MODULES fleet
    class GRADLE,BINDER,TBOOT,ART build
```

---




## 📸 Preview
<div align="center">
<img src="https://raw.githubusercontent.com/jxroot/BlackWire/refs/heads/main/images/devices.png">
<img src="https://raw.githubusercontent.com/jxroot/BlackWire/refs/heads/main/images/device-detail.png">

</div>

## ▶️ Demo Video & Wiki


- [Watch Demo video](videos/demo.mp4)
- [Capabilities - Screen monitor](videos/screen-monitor.mp4)
- [Capabilities - Microphone](videos/microphone.mp4)

## 🔨 APK Builder

Configure the agent app and build a customized APK for installation — directly from the dashboard, no Android Studio required.

Open **Build** in the sidebar after signing in.



### App identity

| Option | Description |
|:--|:--|
| **App name** | Display name shown in notifications and system settings (default: `Device Agent`) |
| **Package name** | Unique application ID Android uses to identify the installed app (default: `com.devicemanager.agent`) |
| **App icon** | Optional square PNG, JPG, or WebP up to 1 MB |
| **Server URL** | Backend address the agent connects to (e.g. `http://192.168.1.100:8000`) |

### Permissions at install

Choose whether the APK requests permissions on first launch, or leaves everything for the device **Permissions** panel later.

#### Minimal install *(recommended)*

Smaller APK without SMS, camera, location, and other optional features in the manifest. You can choose which of **Notifications** and **Battery optimization** to request on first launch. For more permissions, switch to **Custom on install**.

#### Custom on install

Full-featured APK. Request any selected permissions when the app is opened for the first time. Anything not selected can still be granted later from the panel.

### First-launch permissions

In **minimal** mode, only **Notifications** and **Battery optimization** are available. Switch to **Custom** for SMS, camera, location, and more.

| Permission | Purpose | Minimal | Custom |
|:--|:--|:--:|:--:|
| **All Files Access** | File manager and storage browsing | — | ✓ |
| **Battery Optimization** | Keeps the agent connected in the background | ✓ | ✓ |
| **Photos** | Wallpaper and media access on Android 13+ | — | ✓ |
| **Notifications** | Foreground service notification on Android 13+ | ✓ | ✓ |
| **SMS** | Read and send SMS messages | — | ✓ |
| **Contacts** | Read the contacts list | — | ✓ |
| **Call Log** | Read phone call history | — | ✓ |
| **Location** | GPS location tracking | — | ✓ |
| **Microphone** | Record and stream audio | — | ✓ |
| **Camera** | Capture photos and live preview | — | ✓ |
| **Accessibility** | Screen control and remote taps | — | ✓ |
| **Notification Access** | Track notifications from other apps | — | ✓ |

### Advanced options

| Option | Description |
|:--|:--|
| **Heartbeat interval** | Seconds between agent heartbeats (default: `15`) |
| **Fake APK size (MB)** | Pads the APK with extra data at build time. Use `0` to disable |
| **Hide app after install** | Removes the launcher icon after the agent starts. The app keeps running in the background. The icon hides after the agent runs once, not at install time. On Xiaomi/Samsung devices it may take a few seconds or a launcher restart |
| **Device Admin** | Includes device administrator support in the APK. Must be enabled here before building — it cannot be added later from the panel. On first launch the app prompts to activate; status appears under **Permissions**. The user can revoke it from Android settings |

### Security

| Option | Description |
|:--|:--|
| **Require HTTPS** | Blocks plain HTTP and WebSocket connections in the agent |
| **Allow HTTP (local dev only)** | Permits `http://` server URLs for LAN testing. Keep disabled in production |
| **Certificate pinning** | Automatically enabled for valid HTTPS hosts; disabled for ngrok and plain HTTP |
| **Anti-root detection** | Exits the agent when root access is detected |
| **Anti-emulator detection** | Exits the agent when running on an emulator |
| **Anti-debug detection** | Exits when a debugger is attached or the app is debuggable |

### Build & download

1. Click **Build APK** — the server runs Gradle in the background
2. Wait for the build to finish (status: queued → building → ready)
3. Download the APK and install it on your device
4. View **build history** with version code, config fingerprint, and install-status tracking

Once installed, the device connects over WebSocket and shows as **Online** in the dashboard.

---

## 🔐 License

This software is **proprietary**. Use, distribution, or modification without a valid license is not permitted.

<div align="center">

### 📩 Get a License

Contact us on Telegram:


[![Telegram](https://img.shields.io/badge/Telegram-@amajax-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/amajax)


</div>

---


## ⚠️ Legal & Ethical Disclaimer

🚨 This tool is developed strictly for educational and authorized security testing purposes only.

🔬 It is intended to help cybersecurity professionals, researchers, and enthusiasts understand post-exploitation, red teaming, and detection techniques in lab or controlled environments.

❌ Do NOT use this tool on any system or network without explicit permission. Unauthorized use may be illegal and unethical.

🛡 The author takes no responsibility for any misuse or damage caused by this project.

---

> Always hack responsibly. 💻🔐

