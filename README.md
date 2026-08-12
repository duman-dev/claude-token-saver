<p align="center">
  <img src="banner.png" alt="Claude Token Saver Banner" width="100%" style="max-width: 1000px; height: auto;">
</p>

# <p align="center">Claude Token Saver</p>

<p align="center">
  <b>Claude Token Saver</b> is a collection of practical methods to reduce token usage when working with Claude and Claude Code. Tested and confirmed by developers in real workflows — no guarantees, but they work.
</p>

<h1 align="center">

[🇺🇸](https://github.com/duman-dev/claude-token-saver/blob/main/README.md) [🇷🇺](https://github.com/duman-dev/claude-token-saver/blob/main/README.ru.md) [🇩🇪](https://github.com/duman-dev/claude-token-saver/blob/main/README.de.md) [🇹🇷](https://github.com/duman-dev/claude-token-saver/blob/main/README.tr.md)

</h1>

> [!NOTE]
> These methods come from hands-on testing and observation. We've seen them work consistently, but your results may vary based on usage patterns, session length, and model version.

---

### Methods

* [**1 — Concise Prompting**](#1--concise-prompting): Tell Claude to skip filler. Less output, same result.
* [**2 — Edit Instead of Follow-Up**](#2--edit-instead-of-follow-up): Replace failed attempts instead of stacking them.
* [**3 — Context Compact**](#3--context-compact): Reset long sessions without losing progress.
* [**4 — Batch Instead of Split**](#4--batch-instead-of-split): One message for related tasks instead of many.
* [**5 — Optimizing Tokens with Skills like Caveman**](#5--optimizing-tokens-with-skills-like-caveman): Compress inputs and outputs with a dedicated token-optimization skill/proxy.

---

## 1 — Concise Prompting

Claude's default style is conversational. Every preamble, recap, and "I'd be happy to help" costs output tokens — and gets re-read on every future turn as part of history.

**Fix:** Paste this into your `CLAUDE.md` (Claude Code) or **Settings → Profile → User Preferences** (claude.ai):

```
Reply in the language user employs. Use most concise form possible.
Skip pleasantries, preambles, recaps.
No "I'd be happy to", "Great question", "Let me explain".
Drop articles, filler words wherever meaning stays clear.
Prefer short declarative sentences. If tool call needed,
run first and show only result. Do not narrate steps.
Prioritize code/data format over prose.
No explanations unless essential. Output only.
```

**Before:**
> "Great question! I'd be happy to help you with that. To fix this bug, you'll want to check the null reference on line 12. Let me explain what's happening here..."

**After:**
> "Null reference on line 12."

Same answer. A fraction of the tokens.

---

## 2 — Edit Instead of Follow-Up

When Claude misses the point, the reflex is to send a correction: *"No, I meant..."* That correction stacks onto history. The wrong answer stays. Both get re-read on every turn after.

**Fix:** Edit your original message and regenerate instead of replying.

| ❌ Follow-up | ✅ Edit |
|---|---|
| Original message stays | Original message updated |
| Wrong answer stays in history | Wrong answer discarded |
| Both re-read every turn | Only correct exchange in context |

In Claude Code: when a session goes off track, start a fresh session rather than trying to steer the same one back.

---

## 3 — Context Compact

Every message carries the full conversation history. After 15–20 messages, most of that history is irrelevant — but it still gets loaded every turn.

**Fix:** Before starting a new chat, send this prompt to get a machine-optimized handoff:

```
Summarize our entire conversation with language user employs
so I can paste it into a
new chat and continue without losing context. Include:
(1) the original goal or problem
(2) key decisions made and why
(3) any code, config, or data we settled on, verbatim, in code blocks
(4) open questions and next steps
Use short sections with headings. Skip small talk and
exploratory tangents. Optimize the summary for a future
Claude reading it cold.
```

Paste the result as the first message of a new chat. A 40-message session becomes a 200-word brief — no history dragged along.

---

## 4 — Batch Instead of Split

Each message reloads the full conversation history. Three small asks sent separately = three history loads. One message with all three = one load.

**Fix:** Combine related requests into a single message.

**Before:**
```
You: Fix the typo in this function
You: Now add a docstring
You: And add type hints
```

**After:**
```
You: Fix the typo, add a docstring, and add type hints
```

Side benefit: outputs are more consistent because Claude sees the full picture at once.

---

## 5 — Optimizing Tokens with Skills like Caveman

The methods above cut token waste from *how you use* Claude. A dedicated skill can also compress *what gets sent*, on both sides of the conversation.

[**Caveman**](https://github.com/juliusbrussee/caveman) is an open-source skill/plugin that compresses agent output (terser, "caveman-style" responses — reported ~87% reduction on some tasks) and, via its optional local proxy, compresses input before it reaches the model (JSON, logs, code diffs, and long text are trimmed to their essentials, with originals recoverable locally if needed).

**Fix:** Install the skill and enable it per session.

```bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/v1.10.0/install.sh | bash
```

Then in Claude Code:

```
/caveman [lite|full|ultra|off]
```

Savings vary by workload — treat the project's benchmarks as a starting point, not a guarantee, and compare against your own baseline before relying on it for latency- or cost-sensitive work.

---

### Contributing

Found a method that actually works and can be verified? Open an **Issue** or submit a **Pull Request**.

---

### License

MIT
