# Open-WebUI + SearXNG

A fully self-hosted, private AI stack featuring Ollama for LLM inference, Open WebUI for the chat interface, and SearXNG for private web search with RAG.

---

## Architecture Overview

* **Ollama**: Local LLM backend running models directly with NVIDIA GPU acceleration.
* **Open WebUI (CUDA)**: Feature-rich chat interface connected to Ollama, configured for GPU acceleration.
* **SearXNG**: Privacy-respecting metasearch engine used to provide real-time web search capabilities to your LLM.
* **Valkey (Redis)**: High-performance caching layer for SearXNG query results and rate limiting.
* **Caddy**: Reverse proxy managing local SSL certificates and host-level routing for the search instance.

---

## Prerequisites

* Docker and Docker Compose installed.
* [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) configured on the host.

---

## Directory Setup

Ensure the host directories and configuration files exist before launching:

```bash
mkdir -p ollama searxng

```

### 1. SearXNG Configuration (`./searxng/settings.yml`)

SearXNG requires JSON format support enabled so Open WebUI can parse search results. Ensure your `./searxng/settings.yml` contains at minimum:

```yaml
use_default_settings: true
search:
  formats:
    - html
    - json

```

### 2. Caddyfile (`./Caddyfile`)

Place your Caddy configuration in `./Caddyfile` to handle traffic proxying and HTTPS termination according to your `SEARXNG_HOSTNAME`.

---

## Getting Started

1. **Start the stack**:
```bash
docker compose up -d

```


2. **Access Open WebUI**:
Open `http://localhost:3060` in your browser. The first account registered automatically becomes the administrator.
3. **Pull a model via CLI** (or directly in Open WebUI under **Settings > Models**):
```bash
docker exec -it ollama ollama pull llama3.2

```



---

## Connecting Web Search in Open WebUI

1. Go to **Admin Panel > Settings > Web Search**.
2. Toggle **Enable Web Search** to **On**.
3. Set **Web Search Engine** to `searxng`.
4. Set **SearXNG Query URL** to:
```text
http://searxng:8080/search?q=<query>

```


5. Click **Save**. Web search can now be toggled on per-prompt inside chats.
