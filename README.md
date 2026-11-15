# Daniel Accatino

I build multi-agent AI systems in Python that run on their own, watch their spending, and check their own work.

**What I've built**

- **A multi-agent system in production, in Python.** One side turns a research query into a published web page:
  agents plan it, build it, check it, and repair what fails. The other is a reasoning engine where several model
  roles argue a question and keep a long-term memory. It has run against live providers for months.
- **A team of AI coding agents on one machine.** About thirty agent roles on Claude Code, Codex, Gemini and Kimi,
  isolated under separate Linux users, each with its own working folders. A launcher starts and records the
  unattended runs, and work moves between author, reviewer and verifier as written handoffs.

**Pieces of it, rewritten as standalone libraries** (MIT, tested in CI, on PyPI)

- [llm-router](https://github.com/zerdastudio/llm-router): fallback chains, a spending cap per role, reserved before
  each call, and a ledger of every attempt, inside your own Python process.
- [vector-memory](https://github.com/zerdastudio/vector-memory): long-term memory for an agent in one SQLite file,
  built so it does not quietly rot.

```bash
pip install zerda-llm-router vector-memory
```

**Also:** [ai-csv-batch](https://github.com/zerdastudio/ai-csv-batch) (MIT, with offline tests): run every row of a CSV
through Claude, GPT or Gemini, with retries, resume after a crash and a cost log.

**What I can build for you**

- Agents that work through your documents, data or back-office tasks, with a human check where it matters.
- LLM integrations across several providers, with fallback, spending caps and a cost log.
- Setups where several AI agents work in parallel without stepping on each other.

**Contact:** contact.zerdastudio@gmail.com · based in Italy · English and Italian
