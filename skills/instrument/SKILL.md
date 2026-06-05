---
name: instrument
description: Deliberately retrofit logging and timing into existing code — a systematic observability sweep over a file, module, or function. Use when the user says "instrument this", "add logging", "add timing", "make this observable", "log everything here", or wants to debug something opaque by lighting it up with logs. The on-demand companion to the log-everything rule.
---

# Instrument

A deliberate pass to make opaque code observable — distinct from logging code you happen to
be editing. The user points at a target; you light up its seams with leveled logs and timing
spans, then verify it still runs and the output is useful.

## Workflow

1. **Scope the target.** Use what the user named (file / module / function). If vague, take the
   entry points plus the slowest or most failure-prone paths. State what you're about to
   instrument before you touch anything.
2. **Detect the repo's logger.** Grep for an existing one (pino, winston, structlog, `slog`, a
   `log`/`logger` import) and match it and its format. Only fall back to leveled `console` /
   the `logging` module if there is none. Never sprinkle `console.log` into a structured-logging
   codebase.
3. **Find the seams** (below) and add a log + timing at each.
4. **Verify.** Run the file or its tests, or typecheck/lint if running isn't possible. Confirm
   nothing broke and show a sample of the new log output — don't claim it works without it.
5. **Keep it clean.** Verbose play-by-play goes to `debug` behind `LOG_LEVEL` / `DEBUG`. Don't
   flood hot paths at `info`.

## Seams to hit

- **Function entry/exit** of every non-trivial function — args in (shapes/ids, not values),
  result out.
- **Before & after I/O** — network, API, DB, filesystem — with elapsed time.
- **Branches that matter** — log which path and why (`cache hit age=2h`, `falling back to X`).
- **Loops / batches** — count + total time at the end; per-item only at `debug`.
- **Error paths** — log the *inputs* that caused the failure, not just the message.

## Conventions (from the log-everything rule)

- **Time as spans:** `<scope> <what> in <ms>`. The timing matters as much as the message.
- **Prefix every line** with a scope tag: `[parse]`, `[upload:retry]`.
- **Never log secrets or PII** — tokens, keys, account numbers, dollar amounts, names, raw
  bodies. Log shapes / counts / ids / lengths instead.

```ts
const t0 = performance.now();
console.debug(`[sync] start entity=${entity} files=${files.length}`);
const r = await sync(files);
console.info(`[sync] done entity=${entity} ok=${r.ok} in ${(performance.now() - t0).toFixed(1)}ms`);
```

```python
t0 = time.perf_counter()
log.debug("[parse] start file=%s bytes=%d", path, len(buf))
result = parse(buf)
log.info("[parse] done file=%s fields=%d in %.1fms", path, len(result.fields), (time.perf_counter() - t0) * 1000)
```

Full rule: `rules/log-everything.md` in this repo.

## What not to do

- Don't change behavior — instrumentation is additive. No refactors riding along unless asked.
- Don't log inside tight numeric loops at `info` — measure once around the loop.
- Don't leave secrets, request bodies, or full payloads in the logs.
- Don't report success without running it and showing real output.
