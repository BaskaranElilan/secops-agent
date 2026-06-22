# 🛡️ SecOps-Agent — Complete Windows Docker Setup Guide

**Developer:** Baskaran Elilan  
**Project:** SecOps-Agent — AI-Powered Cybersecurity Operations Platform  
**Stack:** FastAPI · Next.js · Ollama · Neo4j · Elasticsearch · Docker

---

## 📋 Table of Contents

1. [System Requirements](#1-system-requirements)
2. [Install Prerequisites](#2-install-prerequisites)
3. [Enable WSL 2 (Windows Subsystem for Linux)](#3-enable-wsl-2)
4. [Install Docker Desktop](#4-install-docker-desktop)
5. [Clone the Project](#5-clone-the-project)
6. [Configure Environment](#6-configure-environment)
7. [Start SecOps-Agent](#7-start-secops-agent)
8. [Download the AI Model](#8-download-the-ai-model)
9. [Verify Everything is Running](#9-verify-everything-is-running)
10. [Access the Dashboard](#10-access-the-dashboard)
11. [Daily Usage Commands](#11-daily-usage-commands)
12. [Switch AI Providers](#12-switch-ai-providers)
13. [Add API Keys (Optional)](#13-add-api-keys-optional)
14. [Troubleshooting](#14-troubleshooting)
15. [Stop / Remove Everything](#15-stop--remove-everything)

---

## 1. System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **OS** | Windows 10 (21H2+) | Windows 11 |
| **RAM** | 12 GB | 16 GB+ |
| **Storage** | 30 GB free | 50 GB+ free |
| **CPU** | 4 cores | 8 cores |
| **GPU** | Not required | NVIDIA (optional, for faster AI) |
| **Internet** | Required (first setup only) | — |

> [!IMPORTANT]
> You must be running **Windows 10 version 21H2 or later** or **Windows 11**.  
> Check your version: Press `Win + R` → type `winver` → press Enter.

---

## 2. Install Prerequisites

### 2a. Install Git

1. Go to **https://git-scm.com/download/win**
2. Download the **64-bit** installer
3. Run the installer — use **all default settings**
4. After install, open **Command Prompt** and verify:
   ```cmd
   git --version
   ```
   You should see something like: `git version 2.45.0.windows.1`

### 2b. Install a Text Editor (to edit config files)

Download **Notepad++** (free): https://notepad-plus-plus.org/downloads/  
Or use **VS Code**: https://code.visualstudio.com/

---

## 3. Enable WSL 2

Docker Desktop on Windows requires WSL 2 (Windows Subsystem for Linux).

### Step 1 — Open PowerShell as Administrator

Press `Win + X` → click **"Windows PowerShell (Admin)"** or **"Terminal (Admin)"**

### Step 2 — Install WSL

```powershell
wsl --install
```

> [!NOTE]
> This installs Ubuntu by default. If you already have WSL, just update it:
> ```powershell
> wsl --update
> ```

### Step 3 — Set WSL 2 as default

```powershell
wsl --set-default-version 2
```

### Step 4 — Restart your computer

```cmd
shutdown /r /t 0
```

After reboot, Ubuntu will finish setup. Create a Linux username and password when prompted (you can use anything — this is only for the Linux subsystem).

---

## 4. Install Docker Desktop

### Step 1 — Download

Go to: **https://www.docker.com/products/docker-desktop/**  
Click **"Download for Windows"**

### Step 2 — Install

1. Run the installer (`Docker Desktop Installer.exe`)
2. On the configuration screen:
   - ✅ **Use WSL 2 instead of Hyper-V** (keep this checked)
   - ✅ **Add shortcut to desktop**
3. Click **OK** and wait for installation
4. Click **Close and restart** when prompted

### Step 3 — Start Docker Desktop

After restart, **Docker Desktop** will start automatically. Look for the **whale icon** 🐳 in your system tray (bottom-right corner).

Wait until it shows: **"Docker Desktop is running"**

> [!TIP]
> First launch may take 2–3 minutes. The whale icon will be animated while starting.

### Step 4 — Verify Docker is working

Open **Command Prompt** and run:

```cmd
docker --version
docker compose version
```

Expected output:
```
Docker version 27.x.x, build ...
Docker Compose version v2.x.x
```

---

## 5. Clone the Project

Open **Command Prompt** or **PowerShell** and navigate to where you want to save the project.

### Option A — Use an existing folder

```cmd
cd "G:\Cybersecurity Projects"
```

### Option B — Clone from GitHub (if you uploaded it)

```cmd
git clone https://github.com/YOUR_USERNAME/secops-agent.git
cd secops-agent
```

> [!NOTE]
> If you already have the project files in `G:\Cybersecurity Projects\cybersentinel-ai`, just navigate there:
> ```cmd
> cd "G:\Cybersecurity Projects\cybersentinel-ai"
> ```

---

## 6. Configure Environment

> [!IMPORTANT]
> This step is **required** before starting for the first time.

### Step 1 — Create your .env file

Open **Command Prompt** in the project folder and run:

```cmd
copy .env.example .env
```

### Step 2 — Open .env in Notepad++

```cmd
notepad .env
```

Or right-click `.env` → Open with → Notepad++ (or Notepad)

### Step 3 — Review the key settings

The default settings work **out of the box** with local Ollama AI (no API keys needed):

```env
# AI Provider — use local Ollama by default (FREE, no API key needed)
AI_PROVIDER=ollama
OLLAMA_MODEL=qwen2.5:7b

# Database password — change this!
NEO4J_PASSWORD=change-me
```

> [!CAUTION]
> Change `NEO4J_PASSWORD=change-me` to something secure, e.g.:
> ```env
> NEO4J_PASSWORD=MySecurePass2024
> ```

### Optional: Add Cloud AI Keys

If you want to use Claude, GPT-4, or OpenRouter instead of local Ollama:

```env
# Option A: Anthropic Claude (https://console.anthropic.com)
ANTHROPIC_API_KEY=sk-ant-api03-...
AI_PROVIDER=claude

# Option B: OpenAI GPT-4 (https://platform.openai.com/api-keys)
OPENAI_API_KEY=sk-proj-...
AI_PROVIDER=openai

# Option C: OpenRouter — 100+ models including free ones
# Get key at: https://openrouter.ai/keys
OPENROUTER_API_KEY=sk-or-v1-...
AI_PROVIDER=openrouter
# Free model example:
OPENROUTER_MODEL=meta-llama/llama-3.3-70b-instruct:free
```

Save the file after editing.

---

## 7. Start SecOps-Agent

Make sure you are in the project folder in Command Prompt:

```cmd
cd "G:\Cybersecurity Projects\cybersentinel-ai"
```

### Option A — Use the quick start script (easiest)

```cmd
start.bat
```

This will automatically create `.env` if missing, then start everything.

### Option B — Manual Docker Compose

```cmd
docker compose up -d --build
```

### What happens during first startup

| Stage | What's happening | Time |
|-------|-----------------|------|
| Building images | Compiling frontend & backend | 3–8 min |
| Pulling base images | Downloading Ollama, Neo4j, ELK | 2–5 min |
| Starting services | All 7 containers come online | 1–2 min |
| **Total first time** | | **~10–15 min** |

> [!NOTE]
> Subsequent starts (without `--build`) take only **30–60 seconds**.

### Watch the startup logs (optional)

```cmd
docker compose logs -f
```

Press `Ctrl+C` to stop watching logs (containers keep running).

---

## 8. Download the AI Model

SecOps-Agent uses **Qwen2.5:7b** as the local AI model. It downloads automatically during startup via `ollama-init`.

To verify or manually pull the model:

```cmd
docker exec -it secops-agent-ollama-1 ollama pull qwen2.5:7b
```

> [!NOTE]
> This downloads ~4.7 GB. Progress will show in the terminal.  
> Only needed once — stored in the `ollama_data` Docker volume.

### Check what models are downloaded

```cmd
docker exec -it secops-agent-ollama-1 ollama list
```

### Pull a different/additional model (optional)

```cmd
# Smaller, faster model (3.8 GB)
docker exec -it secops-agent-ollama-1 ollama pull llama3.2:3b

# Larger, smarter model (4.9 GB)
docker exec -it secops-agent-ollama-1 ollama pull qwen2.5:14b
```

To use a different model, update `.env`:

```env
OLLAMA_MODEL=llama3.2:3b
```

Then restart the backend:

```cmd
docker compose restart backend
```

---

## 9. Verify Everything is Running

### Check container status

```cmd
docker compose ps
```

You should see all containers with status **Up** or **healthy**:

```
NAME                            STATUS          PORTS
secops-agent-frontend-1         Up              0.0.0.0:3000->3000/tcp
secops-agent-backend-1          Up (healthy)    0.0.0.0:8000->8000/tcp
secops-agent-ollama-1           Up              127.0.0.1:11434->11434/tcp
secops-agent-neo4j-1            Up (healthy)    127.0.0.1:7474->7474/tcp
secops-agent-sandbox-1          Up              (no ports — internal only)
secops-agent-elasticsearch-1    Up (healthy)    127.0.0.1:9200->9200/tcp
secops-agent-kibana-1           Up              127.0.0.1:5601->5601/tcp
```

### Check the backend API health

Open your browser and go to: **http://localhost:8000/health**

You should see a JSON response like:
```json
{
  "status": "ok",
  "ai_provider": "ollama",
  "model": "qwen2.5:7b"
}
```

### Check backend API docs

Visit: **http://localhost:8000/docs** — this is the interactive Swagger UI showing all API endpoints.

---

## 10. Access the Dashboard

Open your browser and go to:

| Service | URL | Purpose |
|---------|-----|---------|
| 🖥️ **Main Dashboard** | http://localhost:3000 | SecOps-Agent UI |
| 🔌 **Backend API** | http://localhost:8000 | FastAPI REST API |
| 📄 **API Docs** | http://localhost:8000/docs | Interactive API documentation |
| 🕸️ **Neo4j Browser** | http://localhost:7474 | Graph database UI |
| 📊 **Kibana SIEM** | http://localhost:5601 | ELK Stack dashboard |

> [!TIP]
> **Neo4j Login:** Username: `neo4j` / Password: whatever you set in `NEO4J_PASSWORD` in `.env`

---

## 11. Daily Usage Commands

Run all commands from the project folder:

```cmd
cd "G:\Cybersecurity Projects\cybersentinel-ai"
```

### Start (after initial setup)

```cmd
docker compose up -d
```

### Stop (containers, keep data)

```cmd
docker compose down
```

### Restart a single service

```cmd
docker compose restart backend
docker compose restart frontend
docker compose restart ollama
```

### View live logs

```cmd
# All services
docker compose logs -f

# Single service
docker compose logs -f backend
docker compose logs -f frontend
docker compose logs -f ollama
```

### Rebuild after code changes

```cmd
docker compose up -d --build
```

### Check resource usage

```cmd
docker stats
```

---

## 12. Switch AI Providers

You can switch AI providers without rebuilding. Just edit `.env` and restart the backend.

### Switch to Claude (Anthropic)

```env
AI_PROVIDER=claude
ANTHROPIC_API_KEY=sk-ant-api03-xxxxx
CLAUDE_MODEL=claude-sonnet-4-20250514
```

```cmd
docker compose restart backend
```

### Switch to OpenAI GPT-4o

```env
AI_PROVIDER=openai
OPENAI_API_KEY=sk-proj-xxxxx
OPENAI_MODEL=gpt-4o
```

```cmd
docker compose restart backend
```

### Switch to OpenRouter (100+ models, free options available)

```env
AI_PROVIDER=openrouter
OPENROUTER_API_KEY=sk-or-v1-xxxxx
OPENROUTER_MODEL=meta-llama/llama-3.3-70b-instruct:free
```

```cmd
docker compose restart backend
```

### Switch back to local Ollama (free, no API key)

```env
AI_PROVIDER=ollama
OLLAMA_MODEL=qwen2.5:7b
```

```cmd
docker compose restart backend
```

> [!TIP]
> You can also switch providers **from the dashboard UI** without editing `.env` — look for the provider selector in the top-right corner.

---

## 13. Add API Keys (Optional)

These keys unlock additional security tool features (not required for basic use):

| Tool | Purpose | Get Key |
|------|---------|---------|
| **Shodan** | IP/device scanning | https://account.shodan.io |
| **VirusTotal** | Malware/IOC lookup | https://virustotal.com/gui/my-apikey |
| **AbuseIPDB** | IP reputation | https://www.abuseipdb.com/account/api |
| **OTX (AlienVault)** | Threat intel feeds | https://otx.alienvault.com/settings |

Add to your `.env` file:

```env
SHODAN_API_KEY=your-key-here
VIRUSTOTAL_API_KEY=your-key-here
ABUSEIPDB_API_KEY=your-key-here
OTX_API_KEY=your-key-here
```

Then restart the backend:

```cmd
docker compose restart backend
```

---

## 14. Troubleshooting

### ❌ Problem: Docker Desktop won't start

**Cause:** WSL 2 not properly installed or virtualization disabled in BIOS.

**Fix 1 — Check virtualization:**
Open **Task Manager** → `Performance` tab → `CPU` → Check if **Virtualization** shows `Enabled`

**Fix 2 — Reinstall WSL:**
```powershell
# Run as Administrator
wsl --unregister Ubuntu
wsl --install
```

**Fix 3 — Enable in BIOS:**
Restart your PC, enter BIOS (usually F2/Del/F10), and enable **Intel VT-x** or **AMD-V**.

---

### ❌ Problem: Port already in use (3000, 8000, 7474, etc.)

**Error message:** `bind: address already in use`

**Fix — Find and kill the process using the port:**
```cmd
# Find what's using port 3000
netstat -ano | findstr :3000

# Kill it (replace 1234 with the PID from above)
taskkill /PID 1234 /F
```

Or change the port in `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"   # Change 3000 to 3001
```

---

### ❌ Problem: Containers won't start — "Cannot connect to Docker daemon"

**Fix:** Make sure Docker Desktop is running (whale icon in system tray).

---

### ❌ Problem: Ollama model download is stuck

**Fix 1 — Check Ollama logs:**
```cmd
docker compose logs -f ollama
```

**Fix 2 — Manually pull the model:**
```cmd
docker exec -it secops-agent-ollama-1 ollama pull qwen2.5:7b
```

**Fix 3 — Restart Ollama:**
```cmd
docker compose restart ollama
```

---

### ❌ Problem: Backend is unhealthy / not starting

**Fix — Check backend logs:**
```cmd
docker compose logs backend
```

Common causes:
- Neo4j not ready yet (wait 1–2 min then retry)
- Port 8000 already in use
- `.env` file missing or has syntax errors

---

### ❌ Problem: "Out of memory" errors

**Fix — Reduce memory limits in `docker-compose.yml`:**

For a machine with 8–12 GB RAM, reduce Ollama memory:
```yaml
ollama:
  deploy:
    resources:
      limits:
        memory: 4G    # Reduce from 8G to 4G
```

---

### ❌ Problem: Neo4j browser not accessible at localhost:7474

**Fix:**
```cmd
docker compose restart neo4j
```

Wait 30 seconds, then try again.

---

### ❌ Problem: Frontend shows blank page or "Cannot connect to backend"

**Fix — Rebuild everything from scratch:**
```cmd
docker compose down
docker compose up -d --build
```

---

### 🔧 Nuclear Reset — Start completely fresh

> [!CAUTION]
> This deletes **all data** including AI model downloads, chat history, and databases. You will need to re-download the Ollama model (~4.7 GB).

```cmd
docker compose down -v --remove-orphans
docker compose up -d --build
```

---

## 15. Stop / Remove Everything

### Stop containers (keep all data)

```cmd
docker compose down
```

### Stop AND delete all data volumes

```cmd
docker compose down -v
```

### Remove all built images (free up disk space)

```cmd
docker compose down --rmi all
```

### Check Docker disk usage

```cmd
docker system df
```

### Free up unused Docker resources (safe)

```cmd
docker system prune
```

---

## 📁 Project Structure Reference

```
secops-agent/
├── .env                    ← Your configuration (never share this!)
├── .env.example            ← Template (copy to .env)
├── docker-compose.yml      ← Defines all 7 services
├── start.bat               ← Windows quick-start script
├── README.md               ← Project overview
│
├── backend/                ← FastAPI Python backend
│   ├── app/
│   │   ├── core/           ← AI router, config, middleware
│   │   ├── routers/        ← API endpoints
│   │   └── services/       ← Business logic (ollama, ELK, etc.)
│   └── Dockerfile
│
├── frontend/               ← Next.js dashboard
│   ├── src/
│   └── Dockerfile
│
├── sandbox/                ← Security tools container
│   └── Dockerfile
│
└── docs/                   ← Documentation
    ├── WINDOWS_SETUP.md    ← This file
    ├── SETUP.md            ← General setup
    └── ARCHITECTURE.md     ← System architecture
```

---

## 🌐 Service Ports Summary

| Container | Port | Access |
|-----------|------|--------|
| Frontend (Dashboard) | `3000` | Public → http://localhost:3000 |
| Backend (API) | `8000` | Public → http://localhost:8000 |
| Ollama (AI) | `11434` | Localhost only |
| Neo4j Browser | `7474` | Localhost only |
| Neo4j Bolt | `7687` | Localhost only |
| Elasticsearch | `9200` | Localhost only |
| Kibana | `5601` | Localhost only |
| Sandbox | (none) | Internal Docker only |

---

## 💡 Quick Tips

- **First time startup is slow** (10–15 min) — be patient!
- **Daily restart is fast** (30–60 sec) — Docker reuses cached images
- **Ollama runs on CPU** by default — responses take 5–30 sec depending on your CPU
- **Use cloud AI** (Claude/GPT-4) for faster, smarter responses
- **Docker Desktop** must be running before you run any `docker compose` commands
- **Logs are your friend** — always check `docker compose logs -f <service>` when something breaks

---

*SecOps-Agent — Developed by Baskaran Elilan*  
*Powered by FastAPI · Next.js · Ollama · Neo4j · Elasticsearch*
