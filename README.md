# Ignite — private self-hosted AI stack (v1.2.0)

**Version V1.2.0.** This repository is maintained for internal use by a private AI company. Distribution, customization, and deployment described here are **proprietary**; they are not a public community offering.

This Docker Compose setup provides a complete local AI and workflow environment built around **n8n**, **Ollama**, **Qdrant**, **PostgreSQL**, and **Docling**, with optional GPU profiles.

---

### Recent capability (v1.2.0)

- **End-to-end RAG** wired for retrieval-augmented generation over your documents.
- **Image-aware RAG**: the stack supports pulling and using **images** alongside text in retrieval (see the bundled workflow below).

Workflow exports and iterations live under **`polished-workflows/`** (for example **`polished-workflows/RAG agent/RAG with image pulling.json`** — import into n8n).

---

### What’s included

- [**Self-hosted n8n**](https://n8n.io/) — workflow automation with LangChain-compatible AI nodes.
- [**Ollama**](https://ollama.com/) — local LLMs.
- [**Qdrant**](https://qdrant.tech/) — vector store.
- [**PostgreSQL**](https://www.postgresql.org/) — application and workflow data.
- [**Docling**](https://www.docling.ai/) — OCR and structured document extraction.
- **Static file server** — nginx serves the shared workspace (e.g. `http://localhost:8080`).

---

### What you can run

Example use cases aligned with private deployment:

- Agents and scheduled automation on your own infra.
- **RAG chat** over internal PDFs and documents without sending content to external APIs.
- **Multimodal RAG** when documents contain figures or screenshots you need in context.
- Low-cost analysis of sensitive material on hardware you control.

---

## Installation

### Clone and configure

```bash
git clone https://github.com/JavonTheGreat1/ignite-V1.git
cd ignite-V1
cp .env.example .env   # set secrets and credentials
```

Use your organization’s fork URL instead if this repository is mirrored elsewhere.

---

### Running with Docker Compose

#### Nvidia GPU

```bash
docker compose --profile gpu-nvidia up
```

If the host GPU has not been used with Docker before, follow [Ollama’s Docker GPU notes](https://github.com/ollama/ollama/blob/main/docs/docker.md).

---

#### AMD GPU (Linux)

```bash
docker compose --profile gpu-amd up
```

---

#### macOS / Apple Silicon

Apple Silicon guests cannot expose the Mac GPU into Docker like a discrete GPU. Either:

1. Run the full stack **CPU-only** (`--profile cpu` below), or  
2. Run **Ollama on the Mac** for faster inference and point n8n at `host.docker.internal:11434` (see below).

CPU profile:

```bash
docker compose --profile cpu up
```

**Ollama on the host (Docker n8n only)**  

1. Set `OLLAMA_HOST=host.docker.internal:11434` in `.env`.  
2. After n8n is up (`http://localhost:5678/`), open **Credentials** → **Local Ollama service** and set the base URL to `http://host.docker.internal:11434/`.

---

#### CPU (default for hosts without GPU pass-through)

```bash
docker compose --profile cpu up
```

> **`--profile cpu` is required for Docling.** Without an appropriate profile, only core services (e.g. n8n, PostgreSQL, Qdrant) come up depending on compose configuration.

---

## Quick start

1. Open `http://localhost:5678/` and complete initial n8n setup.  
2. Import a workflow from **`polished-workflows/`** or open the seeded demo workflow (if configured in your compose volume).  
3. Use **Chat** (or triggers) on the canvas to execute; ensure the selected model is pulled in Ollama before first run.

Docs for AI nodes (agents, retrieval, extractors): [n8n Advanced AI](https://docs.n8n.io/advanced-ai/).

---

## Upgrading

**Nvidia**

```bash
docker compose --profile gpu-nvidia pull
docker compose create && docker compose --profile gpu-nvidia up
```

**macOS CPU**

```bash
docker compose --profile cpu pull
docker compose create && docker compose --profile cpu up
```

**Other CPU**

```bash
docker compose --profile cpu pull
docker compose create && docker compose --profile cpu up
```

---

## Reference reading

- [AI agents — n8n blog](https://blog.n8n.io/ai-agents/)
- [Build an AI workflow — n8n docs](https://docs.n8n.io/advanced-ai/intro-tutorial/)
- [LangChain concepts in n8n](https://docs.n8n.io/advanced-ai/langchain/langchain-n8n/)
- [Agents vs chains](https://docs.n8n.io/advanced-ai/examples/agent-chain-comparison/)
- [Vector databases in n8n](https://docs.n8n.io/advanced-ai/examples/understand-vector-databases/)

---

## Tips

### Shared files

The compose file mounts a **shared** directory into n8n (often `/data/shared` inside the container). Use that path with **Read/Write Files**, **Local File Trigger**, **Execute Command**, etc., as documented in [n8n filesystem nodes](https://docs.n8n.io/).

---

## License and notices

Original upstream template material may remain covered by [**Apache License 2.0**](LICENSE) where applicable. **Custom workflows, documentation specific to this fork, and proprietary deployment choices are maintained by this organization and are not offered as open-source/community software.**

For third-party trademarks and component licenses (n8n, Ollama, Qdrant, Docling, PostgreSQL), refer to each vendor’s terms.
