---
name: intent-decomposition-optimization
description: Explicit-invocation skill that pauses before executing a request to analyze whether it's better handled as a normal chat interaction or requires an autonomous AI agent / multi-step tool workflow. Trigger ONLY when the user explicitly asks for this analysis — phrases like "should this be an agent task", "analyze this request first", "do I need an agent for this", "decompose this task", "/plan", or "scope this out before doing it". Do NOT trigger automatically on ordinary requests — this skill is opt-in, not a default gate on every message. When triggered and an agent workflow is recommended, this skill also searches for and surfaces relevant existing skills (Claude's own catalog first, then GitHub) rather than having the user build one from scratch.
---

# Intent Decomposition & Optimization

A skill for classifying a request *before* acting on it, so the user gets routed to the right mode of work — plain chat, or an agent/skill-based workflow — instead of Claude just diving in.

This skill only runs when the user explicitly invokes it. If invoked, **do not execute the underlying task in the same turn** — run the analysis first, show it to the user, and let them decide how to proceed (even if the answer turns out to be "just do it in chat").

## Step 1: Understand the underlying request

Ask the user (if not already given) what they're actually trying to accomplish — the real request they want analyzed. If they already stated it in the same message that invoked this skill, use that; don't force a redundant question.

## Step 2: Classify

Evaluate the request against these dimensions. None of them alone is decisive — weigh them together.

**Leans "normal chat" when the request is:**
- Single-turn or a short back-and-forth (answer a question, draft/edit text or code, explain something, one-off analysis)
- Fully answerable from the conversation, provided files, or a handful of tool calls (search, fetch, one file edit)
- Not dependent on external systems staying in sync, repeating on a schedule, or running unattended
- Something the user will review and iterate on interactively

**Leans "needs an agent" when the request involves:**
- Many interdependent steps across multiple tools/systems (e.g., read from one system, transform, write to another, verify, repeat)
- Autonomous execution over an extended task without step-by-step supervision (e.g., "go build X and don't stop until it's done/tested")
- Recurring or scheduled/background execution
- Orchestrating or coordinating multiple sub-tasks or sub-agents
- A domain-specific, repeatable workflow that would benefit from being codified once and reused (the kind of thing a "skill" packages up)
- State/memory that needs to persist and be acted on across many turns or sessions

## Step 3: Present the verdict

Give the user a short, direct verdict — not a hedge-everything essay:

- **Classification**: "normal chat" or "agent workflow" (or "hybrid: mostly chat, but step X benefits from an agent")
- **Why**: 1–3 concrete reasons tied to the dimensions above, specific to their request — not the generic checklist
- **If normal chat**: say so and ask if they want you to just proceed now
- **If agent workflow**: move to Step 4 before doing anything else

Don't pad this with disclaimers or repeat the full checklist back at the user.

## Step 4: Find relevant skills (only if agent workflow was recommended)

Look for existing, reusable skills that match the task **in this order** — don't skip straight to GitHub:

1. **Claude's own catalog first.** Call `search_skills` and `search_plugins` with keywords drawn from the task. If relevant results come back, note which are already enabled vs. installable, and use `suggest_skills` / `suggest_plugin_install` to surface them (respecting the one-card-per-conversation limit noted in those tools' own instructions — don't call both if one already covers it well).
2. **GitHub, if the internal catalog doesn't fully cover it.** Web-search for existing, well-maintained skills/agent definitions relevant to the task — e.g. `web_search("<task domain> claude skill github")`, `web_search("awesome claude code skills")`, `web_search("<task domain> claude agent skill")`. Prefer repos that look actively maintained (recent commits, real README, some stars/adoption) over random single-file gists. Actually fetch a couple of the most promising READMEs with `web_fetch` to confirm they genuinely fit before recommending them — don't recommend based on the search snippet alone.
3. Present 2–4 real, verified options (not fabricated links) with `link_preview_display_v0` — for each, one line on what it does and why it fits this task. If nothing solid turns up on GitHub, say so plainly rather than padding the list with a weak match.

## Step 5: Hand off

After presenting the classification (and skills, if applicable), ask how the user wants to proceed — e.g. do the task now in chat despite being agent-shaped, install/use one of the surfaced skills, or take the GitHub option elsewhere. Don't assume; let them choose the next step.

## Ground rules

- This is a decision-support step, not a gate that blocks the user from just asking you to do the thing normally — if they push back on the classification, defer to what they actually want.
- Never fabricate GitHub repos, star counts, or maintenance status — only report what a search/fetch actually returned.
- Keep the verdict concise. The value of this skill is a fast, honest routing decision, not a long report.
