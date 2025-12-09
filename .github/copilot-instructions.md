<!-- Copilot / AI agent instructions for contributors and automated coding agents -->
# Project snapshot for AI coding agents

This repo implements a small web front-end (React + Vite) and a Python-based agent backend (FastAPI + LangChain). The goal is an interactive "Ask the Web" UI that proxies user questions to a LangChain agent which in turn uses small tools (DuckDuckGo search + others) and a local LLM backend (`ollama`/`ChatOllama`).

Key facts (quick):

- **Backend:** `backend/app.py` — FastAPI app that initializes a LangChain agent using `langchain_community.chat_models.ChatOllama`, registers tools decorated with `@tool` (see `web_search`), and exposes a single endpoint: `POST /ask` which expects JSON `{ "question": "..." }` and returns `{"answer": <agent output>}`. The agent is invoked via `agent.invoke({"input": req.question})` and the response string is at `result["output"]`.
- **Frontend:** `frontend/` — Vite + React. Main component `frontend/src/AskTheWeb.jsx` issues `fetch('/ask', { method: 'POST', body: JSON.stringify({ question }) })`. The Vite dev server proxies `/ask` to the backend (see `frontend/vite.config.js`).
- **Dependencies & env:** `environment.yml` lists the environment for the backend (conda). Important packages: `fastapi`, `uvicorn`, `langchain`, `langchain-community`, `ollama-python`, `ddgs` (DuckDuckGo). Frontend uses Vite + React (see `frontend/package.json`).

Run & debug (repro steps):

- Create the Python environment (recommended):

  ```bash
  conda env create -f environment.yml
  conda activate web_agent
  ```

- Start backend (from repo root):

  ```bash
  uvicorn backend.app:app --reload --port 8000
  ```

- Start frontend (in separate terminal):

  ```bash
  cd frontend
  npm install
  npm run dev
  ```

- Open the Vite dev URL (usually `http://localhost:5173`). The app uses Vite's proxy so `fetch('/ask')` is forwarded to `http://localhost:8000/ask`.

Patterns and project-specific conventions

- Tools registration: add small utility functions and decorate with `@tool` (from `langchain.tools`). Example in `backend/app.py`:

  ```py
  @tool
  def web_search(query: str) -> str:
      return search_web(query)
  ```

  These must be present in the same module before `initialize_agent(...)` is called.

- Agent invocation: the backend expects `agent.invoke({"input": <text>})` and returns `result["output"]`. When modifying agent logic, keep the same I/O contract for the `/ask` endpoint.

- Frontend-backend interaction: the frontend sends JSON `{question}` to `POST /ask`. Vite proxy config (`frontend/vite.config.js`) maps `/ask` → `http://localhost:8000`. If you change the API path or server port, update both CORS in backend `allow_origins` and the Vite proxy.

Integration points & external requirements

- Local LLM: `ChatOllama(model=\"llama3\")` indicates an Ollama-backed model is expected. Ensure Ollama is installed/configured or substitute another LangChain chat model and update `backend/app.py`.
- Web search: `duckduckgo_search` (package `ddgs`) is used to implement `search_web`. If you replace/extend search tools, follow the `@tool` pattern.

Files to inspect for changes

- `backend/app.py` — core agent, tools, and `/ask` endpoint.
- `environment.yml` — Python dependency list and recommended environment setup.
- `frontend/vite.config.js` — dev proxy for `/ask`.
- `frontend/src/AskTheWeb.jsx` — UI and fetch usage examples.
- `frontend/package.json` — dev scripts: `npm run dev`, `npm run build`, `npm run preview`.

Developer tips specific to this repo

- Keep the agent/tools initialization atomic in `backend/app.py` — the `llm`, `tools`, and `agent = initialize_agent(...)` happen at import time. Restart the backend after edits to these values.
- Use the conda environment to get the exact dependency versions from `environment.yml` (recommended) to avoid language model client mismatches.
- When adding tools, prefer returning compact plain-text summaries (the frontend displays `answer` in a pre-wrapped text area). The UI expects a string suitable for display (no HTML).

What I won't change automatically

- Authentication and secrets for LLM providers are out of scope for these instructions — modify `backend/app.py` only after agreeing on credential handling.

If anything above is unclear or you'd like this adjusted (more examples, expanded run/debug steps, tests, or CI hooks), tell me which parts to expand and I will iterate.  

---
Generated from repository inspection on files: `backend/app.py`, `environment.yml`, `frontend/vite.config.js`, `frontend/src/AskTheWeb.jsx`, `frontend/package.json`, `frontend/README.md`.
