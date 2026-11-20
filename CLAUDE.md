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
- Don't commit large files (models, datasets, vector stores); write a
  download/setup script instead.

## Layout and conventions

- `experiments/NN-short-name/` — numbered, throwaway, quick to write.
  Each has a `README.md` with: Goal / How to run / What I learned.
- `projects/<name>/` — keepers, promoted from experiments. Each has a README
  with setup, run, and test instructions.
- `notes/` — learning log in Markdown, one file per topic or week.
