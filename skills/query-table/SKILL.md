# query-table

Turn a natural-language data question into a bounded, permission-checked query against governed tables — and aggregate the answer in-conversation.

## When this skill applies

- The user describes a business question ("last quarter returns by region") without naming a table.
- The user names a table and asks for filtered/aggregated rows.

## Hard constraints (non-negotiable)

1. **Never guess a table.** Discovery first: search by business term, show candidates when ranking is close, let the user pick.
2. **Never page beyond the cap.** Backends differ; see `references/pagination.md` before pulling more rows.
3. **Never persist row data.** Aggregate in-conversation; results are answers, not exports. PII-sensitive columns are summarized, never listed.
4. **State the grain.** Every aggregate answer states its time grain and filters. An answer without a scope is a wrong answer.

## Flow

1. Parse intent → target entity (region/product/time) and measure.
2. `search_tables` → candidates; if top score is low or candidates tie, show 2–3 and ask.
3. `get_table_detail` → confirm fields/partitions fit the question.
4. Build the bounded query (single table, WHERE-first, aggregate early).
5. Answer with numbers + scope + caveats.

## References

- `references/pagination.md` — backend pagination behaviors and the "one big page" tradeoff.
- `references/drilldown.md` — when to re-query vs. slice what you already have.
