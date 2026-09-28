# Aven for Raycast

A Raycast extension that integrates with [aven](https://github.com/), the local-first CLI/TUI task manager, letting you create tasks without leaving Raycast.

## Requirements

- The `aven` CLI installed and available on your `PATH` (e.g. `~/.cargo/bin/aven`).
- At least one workspace configured in `aven` (`aven workspace list`).

## Commands

### Add Task

Creates a new task in `aven`.

- **Workspace** — dropdown populated from `aven workspace list`.
- **Project** — dropdown populated from `aven project list --json --workspace <workspace>`, scoped to the selected workspace.
- **Title** — required.
- **Description** — optional Markdown description.

On submit, the extension runs:

```
aven add "<title>" --workspace <workspace> --project <project> [--description "<description>"]
```

## Development

```
npm install
npm run dev     # ray develop
npm run lint     # ray lint
npm run build    # ray build
```
