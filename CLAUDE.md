# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository stores example code for "马克的技术工作坊" (Mark's Tech Workshop) video tutorials. Each top-level directory is an **independent Python project** corresponding to a specific video/tutorial topic. There is no shared build system or cross-project dependencies — each sub-project has its own `pyproject.toml`.

## Project management

- **Package manager:** All sub-projects use `uv` (modern Python package manager). Each has a `pyproject.toml` and `uv.lock`.
- **Run a project:** `uv run .` from within the sub-project directory (for projects with a `__main__.py`), or `uv run <script>.py`.
- **Install dependencies:** `uv sync` inside the sub-project directory.
- **Python version:** Projects target Python ≥3.12 or ≥3.13 (specified in each `pyproject.toml`).

## Environment & API keys

Projects that call LLM APIs load credentials via `python-dotenv` from a `.env` file in the project directory. Common env vars used:
- `OPENROUTER_API_KEY` — for OpenRouter-based projects (e.g., the ReAct Agent)
- Other API keys as needed (Google GenAI, etc.)

`.env` is in `.gitignore` — never commit it.

## Sub-project topics

| Directory | Topic | Key dependencies |
|---|---|---|
| `A2A协议深度解析(1)\weather` | A2A protocol — single weather agent | `a2a-sdk`, `uvicorn` |
| `A2A协议深度解析(2)\weather`, `flight` | A2A protocol — multi-agent (weather + flight) | `a2a-sdk`, `uvicorn` |
| `Agent的概念、原理与构建模式` | ReAct Agent from scratch with OpenRouter | `openai`, `click`, `python-dotenv` |
| `MCP 与 Function Calling 到底什么关系\MarkChat` | Flask chat app comparing MCP vs Function Calling | `flask`, `mcp`, `dotenv`, `requests` |
| `MCP终极指南-进阶篇\weather` | MCP server with I/O logging | `mcp[cli]`, `httpx` |
| `MCP终极指南-番外篇` | Cline system prompts & LLM logger (no uv — uses `requirements.txt`) | Flask-related |
| `使用Python构建RAG系统\rag` | RAG system with ChromaDB + sentence-transformers | `chromadb`, `sentence-transformers`, `google-genai` |

## Architecture patterns

- **A2A projects** follow the A2A SDK pattern: define an `AgentExecutor` subclass (`agent_executor.py`), configure an `AgentCard` with skills, and serve via `A2AStarletteApplication` + uvicorn in `__main__.py`.
- **The ReAct Agent** (`Agent的概念、原理与构建模式/agent.py`) implements the ReAct loop manually: parse `<thought>`, `<action>`, and `<final_answer>` XML tags from the model response, execute tools locally, and feed `<observation>` back.
- **The MCP Chat app** (`MarkChat`) is a Flask web app: `start.py` serves the UI, `backend.py` handles LLM logic, `mcp_client.py`/`mcp_server.py` handle MCP protocol.
- **The RAG project** is a Jupyter notebook (`main.ipynb`) with supporting config in `pyproject.toml`.
- Code comments and user-facing strings are in Chinese.

## Common file patterns

- `__main__.py` — entry point when using `uv run .`
- `pyproject.toml` — per-project dependencies (no workspace/monorepo)
- `.python-version` — pins the Python version for `uv`
- `示例/` directory — example JSON request/response payloads shown in videos
