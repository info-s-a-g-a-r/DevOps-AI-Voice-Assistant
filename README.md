DevOps Siri VoiceOps Assistant

A DevOps Siri–branded real-time AI voice assistant for DevOps learning, troubleshooting guidance, and safe local diagnostics.

The browser captures microphone audio and streams it over WebSocket to a FastAPI backend. The backend keeps a Gemini Live session open, streams the assistant's voice back to the browser, and exposes a small set of read-only DevOps tools.

📁 Project Structure

devops-siri-voiceops-assistant/
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
│   └── pcm-processor.js
│
├── assets/
│   └── README.md
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
│
├── pyproject.toml
├── uv.lock
├── requirements.txt
│
├── .python-version
├── .env.example
├── .gitignore
├── .dockerignore
│
├── run-local.sh
├── run-local.ps1
│
└── README.md

✨ What the Demo Can Do

Real-time voice conversation with Gemini Live

DevOps Siri–branded frontend

Explain Linux, Docker, Kubernetes, Jenkins, CI/CD, networking, and troubleshooting concepts

Check CPU usage

Check memory usage

Check disk usage

Check TCP ports

Check HTTP/HTTPS endpoints

List running Docker containers when Docker socket access is explicitly enabled

Show transcripts and tool activity in the UI

Safety: The project intentionally exposes only read-only diagnostics. It does not provide arbitrary shell execution or destructive infrastructure actions.

🏗️ Architecture

┌─────────────────────┐
│ Browser Microphone  │
└──────────┬──────────┘
           │
           │ WebSocket
           │ 16 kHz PCM
           ▼
┌─────────────────────┐
│ FastAPI / Uvicorn   │
└──────────┬──────────┘
           │
           ├──────────────────────► Read-only DevOps Tools
           │
           ▼
┌─────────────────────┐
│ Google GenAI SDK    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Gemini Live API     │
└──────────┬──────────┘
           │
           │ Streamed Voice
           ▼
┌─────────────────────┐
│ Browser Speaker     │
└─────────────────────┘

See docs/ARCHITECTURE.md for the detailed flow.

✅ Prerequisites

Native Local Execution

Python 3.11

Internet connection

Gemini API key

Modern browser with microphone access

uv recommended

Docker Compose Execution

Docker Engine or Docker Desktop

Docker Compose v2 (docker compose)

Internet connection

Gemini API key

No Node.js, database, Kubernetes cluster, or cloud account is required for the local demo.

⚙️ Configure the Application

Copy the environment template:

Linux / macOS / WSL

cp .env.example .env

Windows PowerShell

Copy-Item .env.example .env

Edit .env and set your Gemini API key:

GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=YOUR_GEMINI_API_KEY
LIVE_MODEL=gemini-3.8-live
LIVE_VOICE=Aoede
LOG_LEVEL=INFO
OTEL_SDK_DISABLED=true
DOCKER_SOCKET=/var/run/docker.sock

Important: Never commit .env to the repository.

🚀 Run the Application

Option A — Run Locally with uv

Install the locked dependencies:

uv sync --frozen

Start the application:

uv run uvicorn app.server:app --host 0.0.0.0 --port 8000

Or on Linux/macOS/WSL:

./run-local.sh

On PowerShell:

.
un-local.ps1

Open:

http://localhost:8000

Health endpoint:

http://localhost:8000/api/health

Detailed native setup:

docs/LOCAL_SETUP.md

Option B — Run with Python venv + pip

Linux / macOS / WSL

python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m uvicorn app.server:app --host 0.0.0.0 --port 8000

Windows PowerShell

python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m uvicorn app.server:app --host 0.0.0.0 --port 8000

Option C — Run with Docker Compose

Start the standard demo:

docker compose up --build

Open:

http://localhost:8000

Useful Commands

docker compose ps
docker compose logs -f voiceops
docker compose down

The standard compose.yaml mounts /var/run/docker.sock so the read-only Docker tool can list running containers through the Docker Engine API.

Security note: Docker socket access is highly privileged even when bind-mounted with :ro. Use this only on a trusted local demo machine.

Run Without Docker Socket Access

docker compose -f compose.safe.yaml up --build

Detailed Compose setup:

docs/DOCKER_COMPOSE.md

🌐 Important localhost Behavior in Docker

Native Execution

localhost → your host machine

Docker Execution

localhost → the VoiceOps container

To reach a service running on your host from the Compose version, use:

host.docker.internal

Examples

Check port 8080 on host.docker.internal.

Check http://host.docker.internal:8080/health.

🎙️ Demo Prompts

After clicking Start Live Session, try:

What's my CPU usage?

Check memory usage.

Check disk space.

Show running Docker containers.

Check port 8080 on host.docker.internal.

Check http://host.docker.internal:8080/health.

Explain CrashLoopBackOff.

What's the difference between a Docker image and a container?

How would you troubleshoot a failed Jenkins pipeline?

For conceptual DevOps questions, Gemini answers directly. For supported diagnostics, Gemini can invoke a read-only tool and the UI displays the tool activity and result.

📚 Documentation

Document

Description

docs/ARCHITECTURE.md

Application and runtime architecture

docs/LOCAL_SETUP.md

Native local execution

docs/DOCKER_COMPOSE.md

Docker Compose execution

docs/TOOLS.md

Read-only tool behavior and boundaries

👤 Author

Sagar
