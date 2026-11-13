# claude-data-skills

Production-grade [Claude Code](https://claude.com/claude-code) / Codex **skills for data work** — querying, lineage, governance, and compliance — built on a discipline we call **constrained skill engineering**.

> A skill is not a prompt. A skill is a contract between the agent and the platform: what it may do, what it must never do, and what it should ask before acting. This repo treats skill files as production artifacts — versioned, reviewable, and measured.

## The pattern

Skills here follow three rules that came out of running data skills against real analysts daily:

1. **Thin main doc, fat references.** `SKILL.md` stays under ~300 lines and contains routing logic only. Domain detail lives in `references/*.md`, loaded on demand. Result: the skill is cheap on every turn, deep when needed.
2. **Hard constraints over vibes.** Each skill carries a small set of non-negotiable rules (e.g. *"PII queries are aggregated in-flight, never written to disk"*, *"a match below the confidence threshold returns near-misses instead of a guess"*). Constraints live in the skill, not in the operator's memory.
3. **Clarify before act.** Skills route by *intent* first — "explore schema" and "query data" and "trace lineage" are different skills, so each stays simple and testable. A skill that asks one good question up front saves five wrong tool calls.

```
skills/
└── query-table/          # natural-language → governed table query
    ├── SKILL.md          # routing + constraints (the contract)
    └── references/
        ├── pagination.md # backend pagination gotchas
        └── drilldown.md  # when to aggregate vs. detail
```

## Skills

| Skill | Status | What it does |
|---|---|---|
| `query-table` | draft | NL → table discovery → bounded query → in-conversation aggregation |
| `trace-lineage` | draft | upstream/downstream tracing with blast-radius summaries |
| `metric-lookup` | draft | business term → governed metric definition (term mismatch is the #1 NL data failure) |
| `data-quality` | planned | freshness/volume/duplicate checks as agent-callable routines |
| `compliance-screen` | planned | sensitive-column classification + export guardrails |

All skills are backend-agnostic: they call MCP tools (see [data-platform-mcp](https://github.com/wang-zhenlei/data-platform-mcp)) and never assume a specific warehouse.

## Install

```
git clone https://github.com/wang-zhenlei/claude-data-skills ~/.claude/skills/claude-data-skills
```

Skills are plain markdown — read them before you trust them, then pin the commit you audited.

## Contributing

The interesting contribution here is **constraints**: what did you have to forbid before a data skill became safe to hand to analysts? PRs that add a rule with a war story are the best PRs.

## License

[MIT](LICENSE)
