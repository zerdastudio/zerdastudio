# A kingdom without a king doesn't go forward

*How I run a team of AI coding agents with a human in charge, and what five weeks of an author–critic loop look like
in numbers. Data from 15 October to 20 November 2026.*

## The short version

I don't let AI agents change a system on autopilot. I run them the way a king runs a kingdom: every agent has a role,
rules and limits, they hand work to each other through fixed channels, and **the final decision is always mine**. I
call it *manual orchestration*: human-in-the-loop, with the operator included. I give the order and approve the
result; between the two, the agents work on their own.

The core of it is a pair: an **author agent** that builds, and a **critic agent** that checks. In five weeks the critic
sent back **476 verdicts** on the author's work. **190** passed as submitted. **286** did not: 220 passed only with
notes or corrections, 66 were sent back. Behind those numbers are real faults caught before they shipped, three of which
are told below.

## The setup

- **Several coding agents from different providers**, working in the same system: Claude Code, Codex, Gemini
  (Antigravity) and Kimi Code. On 20 November: 16 active agents, of which 9 on Claude Code, 3 on Codex, 3 on Antigravity
  and 1 on Kimi Code.
- **Squads, not a crowd.** Agents are grouped in squads with fixed roles: who writes, who criticises, who verifies.
  Squads run under their own system users, usually one per squad and provider.
- **Mail, not chat.** Agents talk through file-based mailboxes. A letter about a piece of work names its files with
  the SHA-256 fingerprint of each one, so a review refers to an exact version.
- **Models through APIs, too.** The system behind this has seven model providers configured (Anthropic,
  OpenAI, Google, Mistral, DeepSeek, Cohere, OpenRouter), with fallback chains per role and a ledger of what the calls
  cost. The routing part is public, with spending caps and the cost of every call:
  [`llm-router`](https://github.com/zerdastudio/llm-router).
- **A human at the top.** The squads never ship a change on their own. I decide what gets built, and I approve
  every change to the system before it goes out.

## Beyond the pair: the kingdom

The author and the critic are one squad among several. On 20 November the agent registry lists 16 active agents in
six groups, each with its own job:

| group | job | active agents |
|---|---|---|
| core squad | builds and reviews the system and the other squads; installs what I approve | 3 (all Claude Code) |
| special squad | builds and reviews new squads when the core squad hands them over, one member per provider | 4 (Claude Code, Codex, Antigravity, Kimi Code) |
| research team | keyword research and checks for the page factory | 4 (2 Claude Code, Codex, Antigravity) |
| video squad | video production | 3 (2 Claude Code, Codex) |
| business strategy | markets, offers and prices; commissions the research | 1 (Claude Code) |
| assistant | tells me what is waiting for my decision | 1 (Antigravity) |

Another 17 agents are installed and started on request: a documents squad, one agent per marketplace, a
critic and a researcher for the market team, spare agents from other providers, and a few more. Under the squads
runs the automatic part: a page factory that turns a search phrase into a published page, checks it and repairs it
on its own. The squads build and change it; it runs by itself.

## Why not fully automatic

Fully automatic pipelines can fail quietly when nobody owns them: a model is confident, a test is green, and nobody
looks. Automation has its place: the pipelines I build do run on their own,
under their own checks. What does not run on its own is the work of changing them. With a person in the loop and a
second agent whose only job is to disagree, a fault has to get past two different readers before it
reaches me, and I still have the last word.

It costs more per step. My bet is that it costs less per result: a fault the critic finds comes back with a
precise case to fix, while a fault that ships is found by a client.

## How the loop works

1. **The author builds** a piece of work: a design, code with its tests, an install package, a public text.
2. **The author writes to the critic**: what changed, the fingerprints of the files, and where to look first.
3. **The critic re-runs the checks itself** instead of trusting the author's numbers, and answers with a **verdict**:
   *passes*, *passes with notes*, or *does not pass*. Every defect gets a label (D1, D2…) and, whenever it can be
   measured, a **reproducible case**:
   the command, what was expected, what came out, and a counter-proof showing the case also passes on the fixed version.
4. **The author fixes and sends a new version.** Every verdict names the SHA-256 of the file it was about, so any
   later change shows up as a new fingerprint and needs a new verdict.
5. **I decide.** The critic's "passes" is a condition for my approval, not a substitute for it.

## The numbers (15 October – 20 November 2026)

Counted by a script that reads every letter from the critic, identifies the recipient from the letter itself and
reads the verdict line. Letters to other agents, forwarded letters and letters whose recipient is unclear are left out.

| | letters |
|---|---|
| letters from the critic to the author | 529 |
| with a clear verdict | **476** |
| passed as submitted | 190 (40%) |
| passed with notes or corrections | 220 (46%) |
| sent back (not passed, or only partly) | 66 (14%) |
| sent back, with at least one labelled defect | 54 of 66 |

Read with care: many "passed as submitted" verdicts are the second or third round of a piece that was sent back first,
so the share of work that passes **at the first attempt** is lower than 40%.

## Three faults the loop caught

**1. An email reader that would have trusted any server.** I was designing a small script that reads a mailbox over
IMAP. The design said "IMAP over SSL". The critic ran Python's `imaplib.IMAP4_SSL` without an explicit context on
Python 3.12 and 3.14 and printed the result: `verify_mode=CERT_NONE`, `check_hostname=False`. The connection is
encrypted, but the server's certificate is not checked. The fix is one argument,
`ssl_context=ssl.create_default_context()`, and the critic's case now fails on the old line and passes on the new one.
Caught at the design stage, before a line of code existed.

**2. A test suite that tested nothing important.** The first version of that same script came with a green test
suite. The critic wrote twelve *mutants*, small deliberate bugs in the code, and ran the suite against each: **all
twelve survived**. The verdict was "does not pass", with 14 labelled defects. In the second round every defect had a
test that catches it, and the critic re-ran 22 mutants against the new suite: all caught.

**3. A false claim in this very showcase.** The first plan for this public profile said that every agent runs under
its own system user. The critic wrote a script that reads the plan and the agent registry: two pairs of agents share
a user. The sentence would have described a protection that does not exist. It was corrected before anything was
published, and that is why the setup above says "usually one per squad and provider".

## Limits

- The numbers describe one author–critic pair in one organisation, over five weeks. They are not a benchmark.
- The critic is an AI agent too. It catches a different kind of fault than a human reviewer, not every fault.
- My own time as the person in charge is not counted here, and neither is the cost of the model calls.

## Work with me

I build and run systems like this for other people's work: multi-provider LLM integration in Python, squads of
coding agents from different providers, and the person in charge who keeps them honest.
More on my [GitHub profile](https://github.com/zerdastudio).
