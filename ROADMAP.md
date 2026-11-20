# KSO_AI Learning Roadmap

**From "how does an LLM work?" to a working AI system you use at work, and can prove you built.**

This roadmap takes about **26 weeks at 10+ hours a week**. Every phase teaches
concepts, has you build small experiments, and then adds one more ability to
**one system that keeps growing**: the **KSO Support Copilot**.

> 🆓 **Free to follow.** No step needs a paid API or a paid service. Everything
> runs on local open models (Ollama) or free tiers. The only exception is the
> Claude Code parts: the author uses a Claude Pro subscription, and free
> alternatives are listed for anyone without one.

---

## Contents

- [The capstone: KSO Support Copilot](#the-capstone-kso-support-copilot)
- [What's free, and how](#whats-free-and-how)
- [Ground rules: data safety](#ground-rules-data-safety)
- [How each phase works](#how-each-phase-works)
- [Overview](#overview)
- Phases
  [0](#phase-0--setup-and-ground-rules-week-1) ·
  [1](#phase-1--how-llms-work--the-model-landscape-weeks-23) ·
  [2](#phase-2--calling-models-from-code-weeks-45) ·
  [3](#phase-3--prompt-engineering--structured-output-weeks-67) ·
  [4](#phase-4--evaluation-proving-it-works-weeks-89) ·
  [5](#phase-5--rag-answering-from-your-docs-weeks-1012) ·
  [6](#phase-6--tools-agents-and-mcp-weeks-1315) ·
  [7](#phase-7--ai-assisted-coding-mastery-weeks-1617) ·
  [8](#phase-8--support-workflows--the-web-app-weeks-1820) ·
  [9](#phase-9--ml-fundamentals-weeks-2123) ·
  [10](#phase-10--production-safety-team-rollout--sharing-weeks-2426)
- [Beyond v1.0](#beyond-v10)
- [Free resource library](#free-resource-library)

---

## The capstone: KSO Support Copilot

An AI assistant for the work a web / TeamSite support engineer does every day:
debugging customer web issues, plus installing, upgrading and maintaining
TeamSite and the custom workflows and apps built on it (JSP, Java, JS).

| Ability | What it does | Phase |
| --- | --- | --- |
| Explain an error | Paste a stack trace and get plain-English cause, likely fixes, next steps | 2 |
| Analyze a log | Turn a long `catalina.out` or app log into a structured diagnosis with evidence lines | 3 |
| **Comparison Lab** | Run the same task on GPT, Gemini, DeepSeek, Claude and local models, and score them | 1 → 4 → 8 |
| Ask the KB | Answer questions from docs and runbooks, with citations | 5 |
| Investigate | An agent that searches logs, checks URLs, loads pages in a real browser and collects evidence | 6 |
| Review code | Review JSP/Java/JS for bugs, security and upgrade problems (javax → jakarta, Java 8 → 17) | 7 |
| Triage & reply | Classify a ticket, spot missing info, draft a reply for a human to edit | 8 |
| Read screenshots | Find the problem in a screenshot of a broken page | 8 |
| Own classifier | A small ML model you trained yourself for fast, private triage | 9 |
| Production-ready | Redaction, provider fallback, Docker, CI, demo deploy, team rollout | 10 |

**Proposed structure** (grows phase by phase, so don't create it all up front):

```
projects/support-copilot/
├── src/copilot/
│   ├── providers/   # ollama, gemini, openai_compatible, claude_code (optional)
│   ├── prompts/     # versioned prompt templates
│   ├── features/    # explain_error, analyze_log, ask, investigate, review, triage, reply
│   ├── rag/         # chunking, embeddings, vector store, retrieval
│   ├── tools/       # agent tools (search_logs, http_check, browser_check, ...)
│   ├── eval/        # evaluation runner + scorers  (the Comparison Lab)
│   ├── safety/      # redaction, injection defenses
│   ├── api/         # FastAPI
│   └── web/         # UI
├── data/samples/    # SYNTHETIC logs, tickets, docs (small, committed)
├── evals/           # test cases with expected answers
└── tests/
```

**Stack:** Python 3.11+, FastAPI, SQLite, Chroma or LanceDB (local vector
store), Ollama, pytest, and HTMX (or Django, which you already know) for the UI.
All free and open source.

---

## What's free, and how

| Need | Free option | Notes |
| --- | --- | --- |
| Local models | **[Ollama](https://ollama.com)** with small open models (Qwen, Llama, Gemma, DeepSeek-R1 distills, Phi) | On a laptop without a GPU (8–16 GB RAM), use **1–4B** models; 7–8B work but slowly. Always-available fallback. |
| Hosted model (Gemini) | **[Google AI Studio](https://aistudio.google.com)** Gemini API free tier | Rate-limited. Free tiers may use your data to improve products, so send **synthetic data only**. |
| Hosted model (GPT and others) | **[GitHub Models](https://github.com/marketplace/models)** free tier, using a GitHub account | Rate-limited prototyping access to OpenAI and other models through an OpenAI-compatible API. |
| Hosted model (DeepSeek and others) | **[OpenRouter](https://openrouter.ai)** `:free` models | Rate-limited; the free model list changes. DeepSeek also runs locally via Ollama. |
| Claude | **Claude Pro** (author): claude.ai + Claude Code, and `claude -p` for scripted runs. Readers: Claude's free plan on claude.ai for chat comparisons | The Claude API is billed separately and is **not** used here. |
| ChatGPT / DeepSeek / Gemini chat apps | Free plans | For hands-on comparisons by hand (Phase 1) |
| AI coding assistant | Claude Code (Pro). Free alternatives: **Gemini CLI**, **Aider** or **Continue** with Ollama/free models, **GitHub Copilot Free** | Each Phase 7 exercise works with any of them |
| GPU for training | **Google Colab** / **Kaggle Notebooks** free GPU | Phase 9 only. Everything else runs on CPU. |
| CI | **GitHub Actions** (free for public repos) | Phase 10 |
| Demo hosting | **Hugging Face Spaces** free CPU tier | Phase 10 |
| Everything else | Python, Git, VS Code, FastAPI, Chroma, scikit-learn, PyTorch, Playwright, Docker | Open source |

> ⚠️ Free tiers, rate limits and model names change often. Check current
> terms at the start of each phase. If a free tier disappears, every phase
> still works with **Ollama only**.

---

## Ground rules: data safety

Customer logs, code and tickets are **not yours to share**. Before using AI at
work:

1. **Check your company's AI policy.** Which tools are approved? Can customer
   data go to cloud AI at all?
2. **This public repo holds synthetic data only.** No customer names, hosts,
   IPs, credentials, real logs, or proprietary TeamSite documentation.
   Scenarios are written from general knowledge.
3. **Real work data stays local.** The copilot reads private docs and logs from
   paths outside the repo (`--kb-path`, `--log-path`) and uses local models by
   default for them.
4. **Redact before anything goes to the cloud** (Phase 10 automates this).
5. **Treat logs, tickets and web pages as untrusted input.** They can contain
   prompt injection (Phases 3, 6, 10).

---

## How each phase works

Each phase has the same five parts:

- 📖 **Learn**: concepts, with free resources
- 🧪 **Experiment**: small throwaway scripts in `experiments/NN-name/`
- 🚀 **Ship**: a new version of the Support Copilot
- ⚖️ **Compare**: what this phase showed about GPT vs Gemini vs DeepSeek vs Claude vs local, recorded in `notes/model-comparison.md` **with evidence**
- ✅ **Done when**: a concrete check that proves you can do it

**Suggested week (10 h):** about 3 h learning · 5 h building · 1 h evals & comparison · 1 h notes.

**At the end of every phase:**
1. Tag a release (`git tag copilot-v0.X`).
2. Update `notes/model-comparison.md`.
3. Write `notes/phase-NN.md` (what worked, what didn't, prompts worth keeping).
4. Tick it off in the tracker.

**Use AI to build all of this.** From Phase 2 on, build with Claude Code (or a
free alternative), then review every diff yourself. Learning *when the AI is
wrong* is part of the course.

---

## Overview

| # | Phase | Weeks | Copilot version |
| --- | --- | --- | --- |
| 0 | Setup and ground rules | 1 | — |
| 1 | How LLMs work + the model landscape | 2–3 | — (comparison matrix v1) |
| 2 | Calling models from code | 4–5 | v0.1 `explain-error` |
| 3 | Prompt engineering & structured output | 6–7 | v0.2 `analyze-log` |
| 4 | Evaluation: proving it works | 8–9 | v0.3 Comparison Lab |
| 5 | RAG: answering from your docs | 10–12 | v0.4 `ask` with citations |
| 6 | Tools, agents, and MCP | 13–15 | v0.5 `investigate` + MCP server |
| 7 | AI-assisted coding mastery | 16–17 | v0.6 `review` + team playbook |
| 8 | Support workflows & the web app | 18–20 | v0.8 triage, reply, web UI |
| 9 | ML fundamentals | 21–23 | v0.9 own triage model |
| 10 | Production, safety, team rollout & sharing | 24–26 | **v1.0** |

---

## Phase 0 — Setup and ground rules (Week 1)

📖 **Learn**
- How this repo works: branches, commits, pull requests
- What's free (table above) and the data-safety rules
- Claude Code basics: starting a session, `CLAUDE.md`, asking it to explain a repo

🧪 **Experiment: `00-setup-check`**
- Install Python 3.11+, Git, VS Code and [Ollama](https://ollama.com). Pull one small chat model and one embedding model.
- Create free keys: Google AI Studio (Gemini), a GitHub token for GitHub Models, OpenRouter. Put them in `.env`.
- Write `check.py`: send "Say hello in one word" to every configured provider and print which ones answer and how long each took.

📝 **Write** `notes/ai-usage-rules.md`: your company's AI policy and your
personal rules for work data.

✅ **Done when** `python experiments/00-setup-check/check.py` shows at least
**Ollama + 2 hosted providers** answering.

---

## Phase 1 — How LLMs work + the model landscape (Weeks 2–3)

📖 **Learn**
- Tokens, the context window, next-token prediction
- Temperature / top-p, and why output varies between runs
- Hallucination: why it happens and how to reduce it
- System vs user prompts, multi-turn chat
- Reasoning ("thinking") models vs standard models
- Multimodal input (images, screenshots)
- Open-weight vs closed models; what quantization means for your laptop
- Embeddings: a first look

Free resources: 3Blue1Brown's *Neural Networks* series (incl. *But what is a
GPT?*), Andrej Karpathy's *Intro to Large Language Models* (YouTube), and
Hugging Face LLM Course chapter 1.

🧪 **Experiments**
- `01-tokens-and-temperature`: count tokens for a stack trace; run the same prompt at temperature 0 / 0.7 / 1.2 with Ollama and compare.
- `02-context-limits`: feed a small local model a log that's too long. What breaks? (This sets up Phase 3.)

⚖️ **Compare: the hands-on model test.** Write **10 tasks** based on your real
work (sanitized) and run each one in the free chat apps (ChatGPT, Gemini,
DeepSeek) and in Claude (your Pro) by hand. For example:
1. Explain a Java `NullPointerException` stack trace
2. Find the bug in a JSP snippet
3. Write a regex that extracts timestamps and levels from a Tomcat log line
4. Summarize a 300-line log
5. Explain a JavaScript console error from a customer's page
6. Plan a Tomcat / Java version upgrade
7. Debug a CSS / layout issue from a description
8. Draft a polite customer reply asking for more information
9. Explain an unfamiliar XML config
10. Translate a reply (if you work in more than one language)

Score each answer 1–5 for correctness, usefulness and speed.

Create `notes/model-comparison.md` with these dimensions, and fill them in
**from evidence, not opinion**: quality on *your* tasks · coding · reasoning ·
context length · speed · API price · free tier · open weights / self-hostable ·
where hosted data is processed and stored · tool-use reliability · vision ·
language support.

✅ **Done when** you can explain tokens, context windows and temperature
without notes, and `model-comparison.md` v1 has scores for all 10 tasks.

---

## Phase 2 — Calling models from code (Weeks 4–5)

📖 **Learn**
- Chat API message format: roles, messages, parameters
- Streaming responses
- Errors, rate limits, retries with backoff, timeouts
- Token usage and estimating cost
- **OpenAI-compatible APIs**: Ollama, GitHub Models and OpenRouter all accept the same request format, and Gemini has its own SDK
- Why you'd build a thin provider layer yourself before using a library like LiteLLM

🧪 **Experiment: `03-provider-adapters`**. Build one interface,
`complete(messages, **opts) -> Result(text, tokens, latency_ms, provider, model)`,
with these adapters:
- `ollama` (local)
- `gemini` (free tier)
- `openai_compatible` (GitHub Models, OpenRouter)
- `claude_code` (optional): runs `claude -p` through your Pro subscription and is skipped automatically if Claude Code isn't installed

🚀 **Ship: Copilot v0.1, `explain-error`**
```bash
copilot explain-error --provider ollama < samples/npe-stacktrace.txt
```
It prints a plain-English explanation, the most likely causes and the next
steps, and logs provider, model, tokens and latency to SQLite.

🤖 **AI-coding practice:** build the adapters with Claude Code, then review
the code yourself. Write down every mistake it made.

⚖️ **Compare:** the same 5 stack traces on 3+ providers. Which explanations
were right? How fast was each? How did the small local models do?

✅ **Done when** one command explains the same error on 3+ providers and the
SQLite log shows tokens and latency for each run.

---

## Phase 3 — Prompt engineering & structured output (Weeks 6–7)

📖 **Learn**
- Clear instructions, roles, and where to put context
- Few-shot examples
- Delimiters and XML tags
- Step-by-step reasoning vs reasoning models
- **Structured output**: JSON schemas, Pydantic validation, retry on invalid output
- Prompt templates and versioning
- Pre-processing: not everything should go to the LLM (filter, dedupe, cut down)
- **Prompt injection basics**: logs and tickets are untrusted input

Free resources: Anthropic's prompt engineering docs and interactive tutorial
([github.com/anthropics/courses](https://github.com/anthropics/courses), free
to read), Google's Gemini prompting guide, OpenAI's prompt engineering guide,
and [Learn Prompting](https://learnprompting.org).

🧪 **Experiments**
- `04-sample-data`: generate **synthetic** samples with AI, then check them by hand: Tomcat `catalina.out`, Java stack traces, JSP compile errors, browser console errors, HAR snippets, and TeamSite-style workflow and upgrade errors written from general knowledge. Save them to `data/samples/`.
- `05-prompt-iterations`: write one prompt in 5 versions (zero-shot → instructions → few-shot → XML-structured → schema) and keep a table of results.

🚀 **Ship: Copilot v0.2, `analyze-log`**. It returns a validated `Diagnosis`:
```python
class Diagnosis(BaseModel):
    summary: str
    severity: Literal["low", "medium", "high", "critical"]
    suspected_component: str          # e.g. "JSP compile", "workflow engine", "frontend JS"
    hypotheses: list[Hypothesis]      # cause, evidence_lines, confidence
    next_steps: list[str]
    questions_for_customer: list[str]
```
Long logs go through a pre-filter first (ERROR/Exception lines, dedupe repeats,
keep context windows around them), so even small local models can handle them.

⚖️ **Compare:** which models return valid JSON every time? Small local models
vs hosted ones? With and without few-shot examples?

✅ **Done when** `analyze-log` returns schema-valid output on 10 sample logs
and cites the correct evidence lines.

---

## Phase 4 — Evaluation: proving it works (Weeks 8–9)

📖 **Learn**
- Why "it looked good" isn't evidence
- Golden datasets: test cases with expected answers
- Metrics: exact match, field accuracy, rubric scoring
- **LLM-as-judge**, and its biases
- Human review
- Run-to-run variance: run each case several times
- Latency and cost as metrics
- Regression-testing prompts

Free resources: Hamel Husain's articles on evals (hamel.dev), the
[promptfoo](https://promptfoo.dev) docs (open source), and Anthropic's docs on
building empirical evaluations.

🧪 **Experiment: `06-promptfoo`**: run promptfoo on 5 cases and compare it with
the runner you build below.

🚀 **Ship: Copilot v0.3, the Comparison Lab**
```bash
copilot eval --suite analyze-log --providers ollama,gemini,github:gpt,openrouter:deepseek --runs 3
```
- `evals/analyze-log/`: **30+ cases**, each with a known root cause
- Scorers: schema valid, correct component, root cause matches (rule-based + LLM judge), evidence lines found
- Report (Markdown + HTML): accuracy, validity %, p50/p95 latency, tokens, cost per 100 tickets *if it were paid*
- Claude scores come from `claude -p` runs on your Pro plan, marked optional

⚖️ **Compare:** your first **evidence-based** comparison table. Paste it into
`model-comparison.md`.

✅ **Done when** one command produces a report comparing 4+ providers, and you
can explain why the winner won.

---

## Phase 5 — RAG: answering from your docs (Weeks 10–12)

📖 **Learn**
- Embeddings and cosine similarity, properly this time
- Chunking strategies (size, overlap, by heading)
- Vector stores
- Keyword (BM25) vs semantic vs **hybrid** search; reranking
- Metadata filters
- **Citations**, and answering "I don't know"
- Evaluating retrieval (recall@k) separately from the answer (faithfulness)
- When to use RAG vs long context vs fine-tuning

Free resources: Hugging Face course sections on semantic search, the
sentence-transformers docs, and the Chroma / LanceDB docs.

🧪 **Experiments**
- `07-embeddings`: embed 50 error messages locally and plot or cluster them. Do similar errors land close together?
- `08-chunking`: try 3 chunking strategies on the same docs and measure recall@5.

🚀 **Ship: Copilot v0.4, `ask`**
```bash
copilot ask "Why do JSPs fail to compile after a Tomcat upgrade?"
```
- Indexes **public** docs (e.g. Apache Tomcat docs and Jakarta EE references; check each source's license) plus KB articles and runbooks you write yourself from general knowledge
- Local embeddings (an Ollama embedding model or sentence-transformers on CPU), local vector store
- Answers with `[source]` citations and says "not in the KB" when it doesn't know
- `analyze-log` now pulls related KB articles into its diagnosis
- `--kb-path /private/docs` indexes work docs **locally, never committed**

⚖️ **Compare:** answer faithfulness per provider. Which models stick to the
sources, and which make things up?

✅ **Done when** recall@5 ≥ 0.8 on 20 question/answer pairs and every answer
has correct citations.

---

## Phase 6 — Tools, agents, and MCP (Weeks 13–15)

📖 **Learn**
- Tool / function calling and tool schemas
- The agent loop: think → act → observe
- **Workflows vs agents**: when *not* to use an agent
- Guardrails: read-only tools, allow-lists, max steps, human approval
- Context engineering: what goes into the window, and when
- **MCP (Model Context Protocol)**: servers, tools, resources

Free resources: Anthropic's *Building effective agents* article,
[modelcontextprotocol.io](https://modelcontextprotocol.io), and the Hugging Face
Agents Course.

🧪 **Experiments**
- `09-tool-calling`: one tool, one model, by hand. Then compare tool-calling reliability across providers.
- `10-browser-check`: use **Playwright** to load a page and capture console errors, failed requests and a screenshot.

🚀 **Ship: Copilot v0.5, `investigate` + MCP server**
- Tools:
  - `search_logs(pattern)`, `read_log_window(file, line, n)`
  - `kb_search(query)`
  - `http_check(url)` (status, headers, redirects, timing)
  - `browser_check(url)` (Playwright: console errors, failed requests, screenshot)
  - `parse_har(file)`
  - `java_env_check()`
- `copilot investigate ticket.md`: the agent gathers evidence with these tools and returns a `Diagnosis` with an evidence trail of every tool call
- **MCP server**: exposes these tools so Claude Code (your Pro) or any MCP client can use them. For example: *"Investigate this log using the kso-copilot tools."*

⚖️ **Compare:** tool-calling reliability, wrong tool choices, and runaway
loops. Small local models usually struggle here, so record where.

✅ **Done when** `investigate` solves 5 synthetic tickets end to end, and
Claude Code can call your MCP tools.

---

## Phase 7 — AI-assisted coding mastery (Weeks 16–17, and all along)

📖 **Learn**
- Working effectively with an AI coding assistant:
  - Give context: `CLAUDE.md` and good prompts
  - Plan before code
  - Small steps, test-first
  - Review every diff
  - Know when the AI is wrong
- Custom slash commands / skills, hooks, subagents, MCP
- Security of generated code
- Git hygiene with AI: branches, small PRs

Free alternatives for readers: Gemini CLI, Aider or Continue (with Ollama or
free models), GitHub Copilot Free.

🧪 **Experiment: `11-legacy-webapp`**. Build a **small synthetic JSP/Java
webapp** with problems like the ones you see at work, then practice on it:
1. Explain unfamiliar code
2. Find a bug from a customer's symptom description
3. Write JUnit tests for untested legacy code
4. Migrate `javax.*` → `jakarta.*` (Tomcat 10+)
5. Fix Java 8 → 17 upgrade problems
6. Find and fix XSS in a JSP
7. Script a repetitive support check

🚀 **Ship: Copilot v0.6, `review` + team playbook**
- `copilot review path/to/file.jsp`: security, null handling, deprecated APIs, upgrade blockers
- `.claude/commands/` (shareable with your team): `/triage-log`, `/explain-jsp`, `/upgrade-check`, `/draft-reply`
- `notes/playbooks/`: the prompts and workflows that worked for each support task

⚖️ **Compare:** time the 7 tasks above **with and without AI**, and across
tools (Claude Code vs a free alternative). Record your actual speed-up.

✅ **Done when** all 7 tasks are done on the sample app, and your playbook has
at least 4 commands a teammate could use tomorrow.

---

## Phase 8 — Support workflows & the web app (Weeks 18–20)

📖 **Learn**
- Classification and routing with LLMs
- Human-in-the-loop design: **AI drafts, a human sends**
- Reply tone and templates
- **Vision**: diagnosing a screenshot (Gemini free tier, or a small local vision model)
- UI patterns for AI apps: streaming, showing sources, feedback buttons
- Storing history

🧪 **Experiments**
- `12-triage-prompt`: build a zero-shot vs few-shot ticket classifier.
- `13-screenshot-diagnosis`: diagnose screenshots of broken pages (layout issues, error banners).

🚀 **Ship: Copilot v0.7–v0.8**
- **Triage**: ticket → `{category (frontend / JSP / Java / workflow / config / infra / upgrade), priority, missing_info, suggested_owner}`
- **Draft reply**: diagnosis + KB → a reply in your team's tone, editable, **never sent automatically**
- **Web UI** (FastAPI + HTMX, or Django):
  - *Ticket workspace*: paste a ticket, log or screenshot → triage, diagnosis, KB answers, draft reply
  - *Comparison Lab*: run one case on several providers side by side
  - *History & feedback*: 👍/👎 on any answer; a 👎 becomes a new eval case

⚖️ **Compare:** triage accuracy and reply quality per provider; vision quality
on screenshots.

✅ **Done when** a synthetic ticket goes all the way through in the browser:
triage → diagnosis → KB → reply, and feedback saves eval cases.

---

## Phase 9 — ML fundamentals (Weeks 21–23)

📖 **Learn**
- What training is: loss, gradient descent, backpropagation
- A neural network from scratch
- Tokenization (BPE), transformers and attention
- Classic ML: TF-IDF + logistic regression
- Train / validation / test splits; overfitting
- **Prompting vs RAG vs fine-tuning**
- LoRA / QLoRA
- Quantization, and why 4-bit models run on your laptop

Free resources: Karpathy's *Neural Networks: Zero to Hero* (micrograd →
makemore → GPT from scratch), fast.ai *Practical Deep Learning for Coders*,
Google's Machine Learning Crash Course, the Hugging Face LLM Course
(fine-tuning chapters), Kaggle Learn, and 3Blue1Brown.

Compute: your laptop CPU for small models; **free Colab / Kaggle GPU** for
fine-tuning.

🧪 **Experiments**
- `14-micrograd`: follow along with Karpathy and build backprop yourself (CPU).
- `15-triage-three-ways`: on 500+ synthetic labeled tickets (generated, then spot-checked), compare:
  1. TF-IDF + logistic regression (scikit-learn, seconds on CPU)
  2. A zero-shot LLM (from Phase 8)
  3. A fine-tuned small transformer (DistilBERT-sized, free Colab GPU)
- `16-lora-reply-style` (optional): LoRA-fine-tune a small LLM on reply style on Colab, export it to GGUF, and run it in Ollama as **your own provider**.

🚀 **Ship: Copilot v0.9**: triage uses **your trained classifier** first (fast,
free, private) and falls back to an LLM when it's unsure.

⚖️ **Compare:** accuracy, speed and cost of classic ML vs LLM vs fine-tuned,
in the Comparison Lab. When is a small, purpose-built model better than an LLM?

✅ **Done when** you can explain backprop and attention in your own words, and
your classifier beats zero-shot triage on speed while staying within 5% on
accuracy.

---

## Phase 10 — Production, safety, team rollout & sharing (Weeks 24–26)

📖 **Learn**
- **OWASP Top 10 for LLM Applications** (free)
- Prompt injection from tickets, logs and web pages
- PII and secret redaction
- Secrets and config management
- Observability: logging prompts and answers locally, tracing
- Caching
- Rate limits and **provider fallback chains**
- Cost control
- Docker; CI with GitHub Actions

🚀 **Ship: Copilot v1.0**
- **Redaction layer**, on by default for cloud providers: emails, IPs, hostnames, tokens, and a custom customer-name list
- **Injection defenses**: untrusted content clearly fenced, tools read-only, tested with injection eval cases
- **Fallback chain**: e.g. Gemini free → GitHub Models → local Ollama
- `Dockerfile` + `docker compose` (app + Ollama)
- **GitHub Actions CI**: unit tests + a small eval suite against a tiny local model
- **Demo deploy** on Hugging Face Spaces (free CPU), using sample data only

👥 **Team rollout** (`notes/team-rollout.md`)
1. Pilot with 2 teammates on 3 use cases.
2. Measure time to first diagnosis, resolution time and reply drafting time, before and after.
3. Collect feedback.
4. Confirm it complies with the company AI policy.

📚 **Share it**
- Polish each phase into a chapter a beginner can follow step by step
- Tag `v1.0`
- Record a short demo (screencast)
- Write a retrospective and the **final model comparison report**: what you know about GPT vs Gemini vs DeepSeek vs Claude vs local models, backed by your eval data

✅ **Done when** v1.0 is tagged, CI is green, the demo is live (or
screencast), and the final comparison report is published in the repo.

---

## Beyond v1.0

- Integrate a ticket system (Jira / ServiceNow / your team's tool, via its API)
- A chat bot for your team (Slack / Teams)
- Reproduce web bugs automatically with Playwright scripts written by the agent
- Multi-agent setups: a planner plus specialists for logs, code and KB
- Local long-term memory of past tickets ("we've seen this before")
- Voice notes → ticket summary

---

## Free resource library

| Topic | Resource |
| --- | --- |
| LLM intuition | 3Blue1Brown, *Neural Networks* series (YouTube) |
| LLM overview | Andrej Karpathy, *Intro to Large Language Models* (YouTube) |
| Neural nets from scratch | [Karpathy, *Neural Networks: Zero to Hero*](https://karpathy.ai/zero-to-hero.html) |
| Practical deep learning | [fast.ai course](https://course.fast.ai) |
| ML basics | [Google Machine Learning Crash Course](https://developers.google.com/machine-learning/crash-course) |
| LLMs, fine-tuning, agents | [Hugging Face Learn](https://huggingface.co/learn) (LLM Course, Agents Course) |
| Hands-on ML | [Kaggle Learn](https://www.kaggle.com/learn) |
| Prompt engineering | [Anthropic courses](https://github.com/anthropics/courses) · [Learn Prompting](https://learnprompting.org) |
| Agents | Anthropic, *Building effective agents* (anthropic.com/research) |
| MCP | [modelcontextprotocol.io](https://modelcontextprotocol.io) |
| Evals | [hamel.dev](https://hamel.dev) · [promptfoo](https://promptfoo.dev) |
| LLM security | OWASP Top 10 for LLM Applications (genai.owasp.org) |
| Local models | [Ollama](https://ollama.com) |

---

*Licensed [CC BY 4.0](LICENSE-docs): share and adapt freely, with credit.
Code is [MIT](LICENSE).*
