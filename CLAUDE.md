# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this repo is

A personal learning repo. The owner is a software developer (Python, Django,
.NET, Angular) who also does support work, and is learning to build systems
with AI and to use AI tools effectively. Learning matters as much as the
result.

## How to work here

- Explain the *why* behind non-obvious choices in a sentence or two — in chat,
  not as long code comments.
- Prefer small, runnable steps over big drops of code. Show how to run it.
- Python 3.11+ by default. Each experiment/project has its own `.venv` and
  `requirements.txt` (or `pyproject.toml`).
- Read API keys from environment variables (loaded from `.env`). Never
  hardcode or print secrets. Add any new variable name to `.env.example`.
- **Free path rule:** everything must be runnable at $0. Default to Ollama
  (local, small 1–4B models: the owner's laptop has no GPU) or free tiers
  (Gemini free tier, GitHub Models, OpenRouter `:free`). Paid APIs may only be
  optional adapters, never required. Claude is used via the owner's Claude Pro
  plan (Claude Code / `claude -p`), not the paid API.
- **Data safety:** only synthetic data in this public repo — no customer names,
  hosts, IPs, credentials, real logs, or proprietary TeamSite docs. Private
  data is read from paths outside the repo and never committed.
- Don't commit large files (models, datasets, vector stores); write a
  download/setup script instead.

## Layout and conventions

- `ROADMAP.md` — the learning path; the capstone is
  `projects/support-copilot/`. Follow the phase the owner is on; don't build
  later phases early.

- `experiments/NN-short-name/` — numbered, throwaway, quick to write.
  Each has a `README.md` with: Goal / How to run / What I learned.
- `projects/<name>/` — keepers, promoted from experiments. Each has a README
  with setup, run, and test instructions.
- `notes/` — learning log in Markdown, one file per topic or week.
