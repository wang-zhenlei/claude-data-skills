# Pagination behaviors (the war stories)

Bounded retrieval is the difference between a data skill and a data incident. Observed failure modes, generalized:

## 1. The page that isn't

Some backends accept a `pageNo` parameter and ignore it — every "page" returns the first N rows. Symptom: row 10,001 looks suspiciously like row 1. Mitigation: on first multi-page pull, checksum page 1 vs page 2; if identical, stop and shard by `WHERE` key ranges instead of paging.

## 2. The silent cap

APIs that cap at 10k rows *without saying so*. The result set looks complete because it *is* — the first 10k. Mitigation: always run a `COUNT` (or metadata row estimate) before trusting a "complete" pull, and compare.

## 3. Overlap across pages

Offset-based paging over changing data can duplicate or skip rows across page boundaries. Mitigation: order by a stable unique key, or pull with a single large page when the total is bounded and the backend tolerates it.

## Decision rule

- total ≤ cap → **one big page** (fewest round trips, no boundary bugs)
- total > cap → **shard by key range**, not by offset
- total unknown → **count first**, then decide
