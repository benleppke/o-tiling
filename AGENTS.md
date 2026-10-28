# AGENTS.md

Rules for AI coding agents (Claude Code, Copilot, Cursor, Gemini, Codex, and similar) working on O-tiling, a GNOME Shell auto-tiling extension written in TypeScript. It is a fork of Pop Shell with many additions. Read [CONTRIBUTING.md](CONTRIBUTING.md) too. Everything there applies to you.

**Core principle:** make the simplest correct change that fits the existing code. Do not code for problems nobody has shown.

## 1. Before you write code

- The task must have an issue. If there is none, stop and tell the user to open one.
- Read the relevant existing code first. Find the smallest part that needs to change.
- If a requirement is unclear, ask. Do not guess behavior, file locations, or conventions.
- If the task is clear, do not ask pointless questions.

## 2. Where things live

| Path | What it holds |
|---|---|
| `src/extension.ts` | Main extension class. Very large (4000+ lines). Read only the ranges you need. Never rewrite or reformat it wholesale. |
| `src/engine/` | Tiling engine: tree (`forest`, `fork`, `node`), `auto_tiler`, `stack`, `presets`, `tiling`. |
| `src/core/` | Small base pieces (`ecs`, `arena`, `events`). |
| `src/window/` | Per-window logic: focus, movement, grab ops, tint. |
| `src/system/` | Settings, keybindings, scheduler, executor, D-Bus. |
| `src/ui/` | Panel, overview, animations, theme consistency. |
| `src/floating_exceptions/` | Floating exception app list and its dialog. |
| `src/prefs.ts` | Preferences window. |
| `src/utils/` | Shared helpers: `log.ts`, `result.ts`, `geom.ts`, `rectangle.ts`, and others. Look here before writing a new helper. |
| `schemas/` | GSettings schema. |
| `keybindings/` | Keybinding XML files. |
| `stylesheet.css` | Shell styles. |
| `metadata.json` | Supported Shell versions and UUID. |
| `dist/` | Build output. Ignored by git. Never edit it. |

## 3. Commands

Use `pnpm`. Do not use npm or yarn, and do not change `pnpm-lock.yaml` unless a dependency change was requested.

```
pnpm install
pnpm run lint          # eslint src
pnpm run type-check    # tsc --noEmit
pnpm run build         # compile to dist/
make install           # build zip and install locally
pnpm run log           # follow gnome-shell logs for o-tiling
```

`scripts/debug.sh` builds, installs, and launches a nested GNOME Shell for debugging. `make shexli` runs the shexli checker on the zip.

Run `lint` and `type-check` before you report anything as done. Most agents cannot run a live GNOME session, so say so in your report.

## 4. Keep changes small

- One task, one patch. Do not touch unrelated code.
- No drive-by refactors, renames, or formatting changes.
- No new dependencies unless there is no reasonable alternative.
- Do not modernize old code or replace working code because another style looks cleaner.
- Do not change settings keys, the schema, or keybindings without a concrete reason. Users have saved values.
- Do not bump versions or edit release files unless asked. CI checks version consistency.

## 5. Reuse before you create

- Search the codebase for an existing function, constant, or utility before writing a new one.
- Extend what exists. Do not add a parallel version.
- Match the style of the file you are editing.

## 6. No overengineering

Do not add:

- Wrapper functions, helper classes, or interfaces with a single use
- Config options nobody asked for
- Defensive checks and fallbacks for cases that cannot happen
- Temporary variables that only rename a value
- Type annotations in places where the surrounding code has none

Prefer direct, readable code.

## 7. Errors, logging, comments

- No `try/catch` by default. Do not catch only to log and rethrow. Never swallow errors silently.
- Handle an error only when the code can recover or add useful context. Follow how the nearby code does it.
- For logging, use the helpers in `src/utils/log.ts`. Do not add `console.*` or logging without a concrete reason.
- If the code is obvious, write no comment. Never restate the code. When a comment is needed, explain why, and keep it short.
- No doc blocks on trivial functions.

## 8. GNOME API rules

This is where AI tools go wrong most often.

- **Never assume an API exists.** Methods, signals, properties, and parameters change between Shell versions.
- Supported versions are in `metadata.json`. Check the exact version you rely on against the GNOME Shell source, GJS docs, or the typings in `@girs/gnome-shell`. Types alone do not prove runtime behavior.
- If you cannot verify an API, say so. Do not present a guess as fact.
- **`disable()` must undo everything `enable()` did.** Disconnect signals, remove timeouts, destroy actors, restore patched methods.
- Patched GNOME methods go through the existing `injections_add` and `injections_remove` pattern in `extension.ts`. Do not invent a second mechanism.
- Each feature handler in `src/ui/` (and `src/system/window_buttons.ts`) has its own `enable()` and `disable()`. Follow that shape for new ones.
- `ext_soft_disable()` also has cleanup steps. If you add state, check both paths.
- Do not leave anything running after the extension is disabled.
- No obfuscated code and no unnecessary subprocess calls. The extension is reviewed on extensions.gnome.org.
- Do not break older supported Shell versions to use a newer API.

## 9. Adding or changing a setting

A new setting usually touches more than one place. Check each:

1. `schemas/org.gnome.shell.extensions.o-tiling.gschema.xml`
2. `src/prefs.ts` (the UI)
3. `ExtensionSettings` in `src/system/settings.ts`, and the code that reads the setting
4. Reset All Settings behavior, if relevant

Do not add a setting when a constant is enough.

## 10. Security

- No hardcoded tokens, keys, or credentials.
- No unsafe command execution or insecure file handling.
- Do not weaken an existing check to make a fix easier.

## 11. Testing honesty

- Do not say something was tested unless it was run.
- Do not say an API was verified unless you checked a source.
- Keep three things separate in your report: code inspection, API verification, runtime testing.
- For a bug fix, check the original failure and the nearby cases that could break.
- List exactly what you could not test. The human will test it.

## 12. Git and PRs

- Stage only files related to the change.
- Commit messages use the repo style: `fix:`, `feat:`, `refactor:`, `chore:`. Short and accurate.
- Do not push, force-push, or open a PR unless the user asked.
- A PR description needs: the linked issue, what changed and why, how it was tested, and what was not tested.
- Disclose AI assistance in the PR. A human must review and understand every line before submitting.

## 13. Your final report

- Short and precise.
- State the root cause when relevant.
- List the files you changed.
- Say what you verified and how, and what you did not.
- Do not claim certainty about unverified behavior.
- Do not call a fix complete if important testing is missing.

## Stop and ask when

- The task needs an architecture change.
- The fix has side effects beyond the reported issue.
- You cannot verify an API the fix depends on.
- Two reasonable approaches would change behavior differently.
