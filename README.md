# Intent Decomposition & Optimization

A Claude Code skill that pauses before executing a request to classify it — is this a normal chat task, or does it need an agent / multi-step workflow? — and, when an agent is the right call, surfaces matching skills (Claude's own catalog first, then GitHub) instead of leaving you to build one from scratch.

It's **explicit-invocation only**: it does not intercept every message, only requests like "should this be an agent task", "analyze this before doing it", "/plan", "scope this out first".

## Install (Claude Code)

```
/plugin marketplace add YuY-QK/Intent-decomposition-skill
/plugin install intent-decomposition@intent-decomposition-marketplace
```

Then invoke it in any conversation with a phrase like:

> "Before you start — should this be a normal chat task or does it need an agent?"

## Repo layout

```
.
├── .claude-plugin/
│   └── marketplace.json          # marketplace catalog (lists the plugin below)
└── plugins/
    └── intent-decomposition/
        ├── .claude-plugin/
        │   └── plugin.json       # plugin manifest
        └── skills/
            └── intent-decomposition/
                └── SKILL.md       # the skill itself
```

## Publishing checklist

1. Validate locally before pushing: `claude plugin validate .` from this directory.
2. (Optional but recommended) Install from the local folder first — `/plugin marketplace add ./gh-repo` then `/plugin install intent-decomposition@intent-decomposition-marketplace` — and actually try the skill before it's public.
3. Push these files to the root of [YuY-QK/Intent-decomposition-skill](https://github.com/YuY-QK/Intent-decomposition-skill) (the `.claude-plugin/` folder must sit at the repo root, not nested).
4. Tag a release if you want a pinned version, otherwise the marketplace tracks `main`.

## Updating

Bump `version` in both `plugin.json` and the matching entry in `marketplace.json` on every release — users only pick up updates when that string changes.

## License

MIT — see plugin.json. Update if you'd prefer a different license.
