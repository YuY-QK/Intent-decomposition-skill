**English** | [简体中文](README.zh-CN.md)

# Intent Decomposition & Optimization

A Claude Code skill that pauses before executing a request to classify it — is this a normal chat task, or does it need an agent / multi-step workflow? — and, when an agent is the right call, surfaces matching skills (Claude's own catalog first, then GitHub) instead of leaving you to build one from scratch.

It's **explicit-invocation only**: it does not intercept every message, only requests like "should this be an agent task", "analyze this before doing it", "/plan", "scope this out first".

## Install

### Claude Code (one-click via plugin marketplace)

```
/plugin marketplace add YuY-QK/Intent-decomposition-skill
/plugin install intent-decomposition@intent-decomposition-marketplace
```

### Other SKILL.md-compatible tools (Codex CLI, Cursor, Windsurf, Gemini CLI, etc.)

The plugin/marketplace mechanism above is Claude Code-specific, but the skill itself is a plain [Agent Skills](https://agentskills.io) `SKILL.md` file, which many tools read natively. Copy the `skills/intent-decomposition/` folder into wherever your tool looks for skills:

```
# Codex CLI
cp -r skills/intent-decomposition .codex/skills/

# Cursor
cp -r skills/intent-decomposition .cursor/skills/

# Windsurf
cp -r skills/intent-decomposition .windsurf/skills/

# Claude Code (manual, without the plugin system)
cp -r skills/intent-decomposition ~/.claude/skills/
```

Check your tool's docs for its exact skills folder — the file itself needs no changes.

Then invoke it in any conversation with a phrase like:

> "Before you start — should this be a normal chat task or does it need an agent?"

## Repo layout

```
.
├── .claude-plugin/
│   └── marketplace.json          # marketplace catalog (Claude Code plugin path)
├── plugins/
│   └── intent-decomposition/
│       ├── .claude-plugin/
│       │   └── plugin.json       # plugin manifest (Claude Code plugin path)
│       └── skills/
│           └── intent-decomposition/
│               └── SKILL.md
└── skills/
    └── intent-decomposition/
        └── SKILL.md               # plain copy for any SKILL.md-compatible tool
```

The two `SKILL.md` files are identical — one is wrapped for the Claude Code plugin system, the other is a plain copy for tools that just expect a skills folder.

## Publishing checklist

1. Validate locally before pushing: `claude plugin validate .` from this directory.
2. (Optional but recommended) Install from the local folder first — `/plugin marketplace add ./gh-repo` then `/plugin install intent-decomposition@intent-decomposition-marketplace` — and actually try the skill before it's public.
3. Push these files to the root of [YuY-QK/Intent-decomposition-skill](https://github.com/YuY-QK/Intent-decomposition-skill) (the `.claude-plugin/` folder must sit at the repo root, not nested).
4. Tag a release if you want a pinned version, otherwise the marketplace tracks `main`.

## Updating

Bump `version` in both `plugin.json` and the matching entry in `marketplace.json` on every release — users only pick up updates when that string changes.

## License

MIT — see plugin.json. Update if you'd prefer a different license.
