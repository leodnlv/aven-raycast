# CLAUDE.md

Raycast extension that shells out to the `aven` CLI (local-first task manager). See `README.md` for user-facing docs.

## Commands

- `npm run dev` — `ray develop` (live-reload in Raycast)
- `npm run lint` — `ray lint` (ESLint + Prettier + manifest/icon checks)
- `npm run build` — `ray build` (type-checks and bundles)

Always run `npm run build` and `npm run lint` after touching `src/` — `ray build` runs its own `tsc` pass independent of any editor/LSP diagnostics.

## Gotchas

- **PATH**: processes spawned via `@raycast/utils`'s `useExec` (and plain `child_process`) get a bare `PATH` of `/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin` — this does NOT include `~/.cargo/bin`, where `aven` actually lives (installed via `cargo install`). Every exec call in this codebase must pass an explicit `env` with an augmented `PATH` (see `AVEN_ENV` in `src/add-task.tsx`). Forgetting this means the extension works fine when tested from a shell but silently fails to find `aven` when run for real inside Raycast.
- **`tsconfig.json` needs `"types": ["node"]`**: without it, `ray build`'s `tsc` pass fails to resolve `process`/`node:child_process`/`node:util` even though `@types/node` is installed. Don't remove this.
- **`aven workspace list` has no `--json`** — output is plain text like `default name="default"` and must be parsed by hand (see `parseWorkspaces`). `aven project list` does support `--json` and accepts `--workspace <key>` to scope results.
- **Task creation is one-shot**, not tied to `useExec`'s render-cycle revalidation — it uses a plain `execFile` (promisified) so it can run imperatively from the submit handler, with args passed as an array (never interpolate user input into a shell string).

## Verifying changes against the real CLI

`aven` is installed locally, so you can sanity-check CLI args directly, e.g.:

```
aven workspace list
aven project list --json --workspace <key>
aven add "test task" --workspace <key> --project <key> --description "..."
aven delete <task-id>   # clean up any test task you create
```
