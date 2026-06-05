# Log Everything

Bias toward **observable code**. When writing or editing code, add logging and timing
freely — favor too much over too little. Logs are the default way to understand what code
did without re-reading it, so don't ask permission to add them; just include them.

## Principles

- **Log at boundaries and decisions.** Entry/exit of any non-trivial operation; before and
  after every I/O, network, API, or DB call; each branch that matters (`took cached path
  because age=2h`, `retrying 2/3`); and every error path with the *inputs* that caused it —
  not just the error message.
- **Time anything that can be slow or fail.** Wrap I/O, external calls, parses, and batch
  loops with start→elapsed timing. Treat each as a lightweight span: `<scope> <what> in <ms>`.
  The timing matters as much as the message.
- **Use levels, not raw prints.** Reach for the repo's logger at `debug`/`info`/`warn`/`error`
  so verbosity is a dial (`LOG_LEVEL` / `DEBUG` env). Verbose play-by-play goes to `debug`.
  Bare `console.log` / `print` only in throwaway scripts.
- **Prefix every line with a scope tag** so logs stay greppable: `[parse]`, `[upload:retry]`,
  `[sync]`.
- **Match the repo's existing logging.** If it uses pino / winston / structlog / a `Logger`,
  use that and its format. Don't sprinkle `console.log` into a structured-logging codebase.
- **Never log secrets or PII.** No tokens, keys, account numbers, dollar amounts, names, or
  raw request bodies. Log shapes, counts, ids, and lengths instead: `{ rows: 42, acct: "…1234" }`.
- **Don't flood hot paths.** Tight loops and per-item logs go at `debug` or sampled — never
  `info`.

## Examples

TypeScript / JS (use the repo's logger if it has one, else prefixed `console`):

```ts
const t0 = performance.now();
console.debug(`[parse] start file=${path} bytes=${buf.length}`);
const result = await parse(buf);
console.info(
  `[parse] done file=${path} fields=${result.fields.length} in ${(performance.now() - t0).toFixed(1)}ms`,
);
```

Python (use the `logging` module, not bare `print`):

```python
t0 = time.perf_counter()
log.debug("[sync] start entity=%s files=%d", entity, len(files))
...
log.info("[sync] done entity=%s ok=%d in %.1fms", entity, ok, (time.perf_counter() - t0) * 1000)
```

Shell (timestamp to stderr so it doesn't pollute stdout/pipes):

```bash
echo "[$(date +%T)] uploading $f ($(wc -c <"$f") bytes)" >&2
```

Remove logging only if it would leak sensitive data or sits in a measured hot path.
