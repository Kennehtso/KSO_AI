# KSO_AI

My workspace for learning to build real systems with AI — as a software
developer and in day-to-day support work — and for getting better at
working *with* AI tools along the way.

📍 **Start here: [ROADMAP.md](ROADMAP.md)**, a 26-week learning path that grows
one real system, the **KSO Support Copilot**. Every step can be followed for
free. Track your progress with the [roadmap tracker](https://claude.ai/artifact/3bSBfn1ZkQgYMqPatsxSYa)
(source in [`tracker/`](tracker/)).

## Layout

| Folder | What goes in it |
| --- | --- |
| [`notes/`](notes/) | Learning log: what I tried, what worked, prompts worth keeping, mistakes |
| [`experiments/`](experiments/) | Small throwaway scripts, one folder per idea (`01-hello-llm`, `02-...`) |
| [`projects/`](projects/) | Real systems worth keeping, each with its own README and setup |
| [`ROADMAP.md`](ROADMAP.md) | The full learning path, phase by phase |
| `CLAUDE.md` | Instructions for Claude Code when it works in this repo |
| `.env.example` | Names of the environment variables / API keys the code expects |

## Getting started

```bash
cp .env.example .env      # then fill in your own keys (.env is git-ignored)
```

Each experiment or project keeps its own dependencies, so set up a virtual
environment inside the folder you're working in:

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Rules I follow

- API keys live only in `.env` — never in code, notebooks, or commits.
  If a key is ever pushed, revoke it and create a new one.
- Big files (models, datasets, vector stores) stay out of git.
- Every experiment gets a short README: goal, what I did, what I learned.

## License

- **Code**: [MIT](LICENSE)
- **Written material** (ROADMAP, notes, lessons): [CC BY 4.0](LICENSE-docs)

Free to use, share and adapt, with credit.
