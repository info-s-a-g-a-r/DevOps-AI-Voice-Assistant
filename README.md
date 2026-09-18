# 🎙️ DevOps Siri VoiceOps Assistant

A DevOps Siri-inspired real-time AI voice assistant for DevOps learning, troubleshooting guidance, and safe local diagnostics.

The browser captures microphone audio and streams it over WebSocket to a FastAPI backend. The backend maintains a Gemini Live session, streams the assistant's voice back to the browser, and exposes a small set of read-only DevOps tools.

---

## 🚀 Features

- 🎙️ Real-time voice conversations with Gemini Live
- 🤖 DevOps-focused AI assistant persona
- 🐧 Linux troubleshooting guidance
- 🐳 Docker concepts and diagnostics
- ☸️ Kubernetes learning and troubleshooting
- 🔄 Jenkins and CI/CD explanations
- 🌐 Networking fundamentals and troubleshooting
- 📊 Read-only system diagnostics
- 📝 Live transcripts and tool activity visibility

### Supported Diagnostics

- CPU Usage
- Memory Usage
- Disk Usage
- TCP Port Checks
- HTTP/HTTPS Endpoint Checks
- Docker Container Listing (Optional)

---

## 📁 Project Structure

```text
DevOps-AI-Voice-Assistant/
│
├── app/
│   ├── __init__.py
│   ├── server.py
│   ├── persona.py
│   └── tools.py
│
├── frontend/
│   ├── index.html
│   ├── main.js
│   ├── pcm-processor.js
│   └── assets/
│
├── docs/
│   ├── ARCHITECTURE.md
│   ├── LOCAL_SETUP.md
│   ├── DOCKER_COMPOSE.md
│   └── TOOLS.md
│
├── Dockerfile
├── compose.yaml
├── compose.safe.yaml
├── pyproject.toml
├── uv.lock
├── requirements.txt
├── .python-version
├── .env.example
├── .gitignore
├── .dockerignore
├── run-local.sh
├── run-local.ps1
└── README.md
```

---

## 🏗️ Architecture

### High-Level Flow

```text
┌─────────────────────┐
│ Browser Microphone  │
└──────────┬──────────┘
           │
           │ WebSocket Audio
           ▼
┌─────────────────────┐
│ FastAPI Backend     │
└──────────┬──────────┘
           │
           ├──────────────► Read-Only DevOps Tools
           │
           ▼
┌─────────────────────┐
│ Gemini Live API     │
└──────────┬──────────┘
           │
           │ Voice Response
           ▼
┌─────────────────────┐
│ Browser Speaker     │
└─────────────────────┘
```

### Request Flow

```text
User
 │
 ▼
Speak into Microphone
 │
 ▼
Browser Streams Audio
 │
 ▼
FastAPI Backend
 │
 ▼
Gemini Live Session
 │
 ├──► Tool Invocation (Optional)
 │
 ▼
Voice Response Generated
 │
 ▼
Audio Stream Returned
 │
 ▼
Browser Speaker
```

For detailed architecture documentation, see:

```text
docs/ARCHITECTURE.md
```

---

## ✅ Prerequisites

### Native Execution

- Python 3.11+
- Internet Connection
- Gemini API Key
- Modern Browser with Microphone Access
- UV (Recommended)

### Docker Execution

- Docker Engine or Docker Desktop
- Docker Compose v2
- Internet Connection
- Gemini API Key

> No Node.js, database, Kubernetes cluster, or cloud account is required.

---

## ⚙️ Configuration

Copy the environment template.

### Linux / macOS / WSL

```bash
cp .env.example .env
```

### Windows PowerShell

```powershell
Copy-Item .env.example .env
```

Edit `.env` and configure your Gemini API key:

```env
GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=YOUR_GEMINI_API_KEY

LIVE_MODEL=gemini-live-model
LIVE_VOICE=Aoede

LOG_LEVEL=INFO
OTEL_SDK_DISABLED=true

DOCKER_SOCKET=/var/run/docker.sock
```

> ⚠️ Never commit your `.env` file to the repository.

---

# 🚀 Running the Application

## Option A: Run Using UV

Install dependencies:

```bash
uv sync --frozen
```

Start the application:

```bash
uv run uvicorn app.server:app \
  --host 0.0.0.0 \
  --port 8000
```

Or use helper scripts:

### Linux / macOS

```bash
./run-local.sh
```

### Windows PowerShell

```powershell
.\run-local.ps1
```

Open:

```text
http://localhost:8000
```

Health Check:

```text
http://localhost:8000/api/health
```

More details:

```text
docs/LOCAL_SETUP.md
```

---

## Option B: Run Using Python Virtual Environment

### Linux / macOS / WSL

```bash
python3 -m venv .venv

source .venv/bin/activate

python -m pip install -r requirements.txt

python -m uvicorn app.server:app \
  --host 0.0.0.0 \
  --port 8000
```

### Windows PowerShell

```powershell
python -m venv .venv

.\.venv\Scripts\Activate.ps1

python -m pip install -r requirements.txt

python -m uvicorn app.server:app `
  --host 0.0.0.0 `
  --port 8000
```

---

## Option C: Run Using Docker Compose

Start the application:

```bash
docker compose up --build
```

Open:

```text
http://localhost:8000
```

Useful commands:

```bash
docker compose ps

docker compose logs -f voiceops

docker compose down
```

The default `compose.yaml` mounts:

```text
/var/run/docker.sock
```

This enables the assistant to list Docker containers via the Docker Engine API.

### Security Note

Docker socket access is highly privileged, even when mounted as read-only.

Use this option only for local development on trusted machines.

---

## 🔒 Safe Mode (Without Docker Socket)

Run:

```bash
docker compose -f compose.safe.yaml up --build
```

This disables Docker container discovery functionality while keeping all other voice assistant features available.

For detailed setup instructions:

```text
docs/DOCKER_COMPOSE.md
```

---

## 🌐 Docker Networking Notes

### Native Execution

```text
localhost → Your Host Machine
```

### Docker Execution

```text
localhost → VoiceOps Container
```

To reach services running on the host machine from inside the container:

```text
host.docker.internal
```

Examples:

```text
Check port 8080 on host.docker.internal
```

```text
Check http://host.docker.internal:8080/health
```

---

## 🎙️ Sample Prompts

Try asking:

```text
What's my CPU usage?
```

```text
Check memory usage.
```

```text
Check disk space.
```

```text
Check port 8080 on host.docker.internal.
```

```text
Check http://host.docker.internal:8080/health
```

```text
Show running Docker containers.
```

```text
Explain CrashLoopBackOff.
```

```text
What's the difference between a Docker image and a container?
```

```text
How would you troubleshoot a failed Jenkins pipeline?
```

---

## 🛡️ Security Model

The assistant intentionally exposes only read-only diagnostic functionality.

### Allowed

- ✅ CPU Inspection
- ✅ Memory Inspection
- ✅ Disk Inspection
- ✅ TCP Port Checks
- ✅ HTTP Endpoint Checks
- ✅ Docker Container Listing

### Not Allowed

- ❌ Arbitrary Shell Execution
- ❌ File Modification
- ❌ Infrastructure Changes
- ❌ Kubernetes Mutations
- ❌ Container Creation or Deletion
- ❌ Destructive System Commands

---

## 📚 Documentation

| Document | Description |
|-----------|-------------|
| docs/ARCHITECTURE.md | Application architecture and runtime flow |
| docs/LOCAL_SETUP.md | Native local execution |
| docs/DOCKER_COMPOSE.md | Docker Compose deployment |
| docs/TOOLS.md | Tool boundaries and behavior |

---

## 🛠️ Technology Stack

- Python
- FastAPI
- Uvicorn
- Gemini Live API
- Google GenAI SDK
- WebSockets
- HTML
- CSS
- JavaScript
- Docker

---

## 👤 Author

**Sagar**

 Cloud & DevOps Engineer | AI Enthusiast

- AWS
- Kubernetes
- Terraform
- Observability
- Generative AI
- Agentic AI Systems

---

## ⭐ Support

If you found this project useful:

- ⭐ Star the repository
- 🍴 Fork the repository
- 🐛 Report issues
- 🚀 Contribute improvements
