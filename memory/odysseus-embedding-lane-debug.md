---
name: odysseus-embedding-lane-debug
description: Ongoing debug (July 2026) — custom embedding lane fails at startup despite correct EMBEDDING_URL
metadata: 
  node_type: memory
  type: project
  originSessionId: 887ca0a2-277b-4344-9150-00afff1d42f6
---

As of 2026-07-07, Jackson is debugging "Custom embedding lane unavailable" in odysseus. EMBEDDING_URL=http://host.docker.internal:8001/v1/embeddings is set in the container and the endpoint works from the host, but the app falls back to FastEmbed.

**Why:** Key mechanics found: `src/embeddings.py` has a process-level `_http_embed_down` latch — the first failed probe (3s connect timeout) permanently skips HTTP for the process. The real exception is logged once as "HTTP embedding API unavailable (...)". A persisted `<DATA_DIR>/embedding_endpoint.json` can override the env var.

**How to apply:** Prime suspect is host.docker.internal not resolving from inside the container on Linux/WSL2 (needs `extra_hosts: host.docker.internal:host-gateway`). Check docker logs for the real exception before proposing fixes. Related setup: [[jackson-beginner-coder]].
