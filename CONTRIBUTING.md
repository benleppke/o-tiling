# Contributing to O-tiling

Thanks for wanting to help. O-tiling is a small, free GNOME Shell extension, and it is maintained by one person in their spare time. A few simple rules keep the project healthy and keep reviews fast for everyone.

**The short version:** open an issue first, keep your PR small, test it on a real GNOME session, and understand every line you submit.

## 1. Start with an issue

Please do not open a pull request out of nowhere. Open an issue first, even for a small fix.

- **Bug?** Say what happened, what you expected, and how to reproduce it.
- **Feature idea?** Explain the problem you want solved, not just the solution.
- **Not sure?** Open an issue and ask. Nobody will mind.

Wait for a reply before you start coding. A quick "yes, go ahead" saves you from writing a PR that cannot be merged.

PRs that arrive with no linked issue, no explanation, and a big pile of changes will be closed without review. This is not personal. It is the only way to keep the project manageable.

## 2. Bug reports

Include these so the bug can be fixed instead of guessed at:

- O-tiling version
- GNOME Shell version (`gnome-shell --version`)
- Distro, and Wayland or X11
- Steps to reproduce
- Logs, if any: `journalctl -f -o cat /usr/bin/gnome-shell`
- Other extensions you have enabled (they often clash)

Try disabling your other extensions first. If the bug disappears, tell us which one caused it.

## 3. Keep PRs small and focused

- One PR, one problem.
- No unrelated cleanup, renaming, or reformatting mixed into a fix.
- Do not rewrite working code just because you prefer another style.
- Do not add new dependencies unless there is no reasonable alternative.
- If your change is big, split it up, or talk about it in the issue first.

Small PRs get reviewed quickly. Large ones sit for weeks.

## 4. AI tools

Using AI to help you write code is fine. Submitting code you do not understand is not.

Before you open a PR, make sure you:

- Read every line and can explain why it is there.
- Checked that every GNOME or GJS API you use really exists in the Shell version you target. AI tools invent APIs often.
- Ran the code yourself on a real GNOME session.
- Removed filler: pointless comments, extra abstractions, needless try/catch, unused helpers.

If a maintainer asks "why did you do it this way?" and the answer is "the AI wrote it", the PR will be closed. Mass-generated or copy-pasted PRs with no real purpose will be closed too.

## 5. Code guidelines

- Match the style of the surrounding code.
- Prefer the smallest change that fixes the problem.
- Reuse existing functions and utilities before writing new ones.
- Add comments only to explain **why**, never to repeat **what**.
- Do not swallow errors silently, and do not add logging without a reason.
- Never hardcode secrets or tokens.

## 6. GNOME extension rules

O-tiling is published on extensions.gnome.org, so it has to follow their review rules. In practice:

- **`disable()` must undo everything `enable()` did.** Restore patched methods, disconnect signals, remove timeouts, destroy actors.
- Do not leave anything running after the extension is disabled.
- No obfuscated or minified code, and no unnecessary subprocess calls.
- Verify APIs against the official GNOME docs or the GNOME Shell source, and mention which version you checked.
- Supported Shell versions are listed in the latest release notes. Do not break them for a nicer API on a newer one.

Version-specific code is fine when needed, but explain it in the PR.

## 7. Test your change

Say what you actually tested. For example:

- Which GNOME version and distro
- Wayland, X11, or both
- Enable, disable, and re-enable the extension
- Lock and unlock the screen
- Multiple monitors and multiple workspaces, if your change touches tiling

If you could not test something, say so. That is completely fine. Claiming a test you did not run is not.

## 8. Commits and PR description

Commit messages follow the style already used in the repo:

```
fix: short description of what changed
feat: short description of the new thing
refactor: ...
chore: ...
```

Keep them short and accurate. Your PR description should include:

- The issue it closes (`Closes #123`)
- What changed and why
- How you tested it
- Screenshots or a short recording for any visual change

## 9. Getting set up

1. Fork the repo and clone your fork.
2. Install dependencies with `pnpm install`.
3. Build and install the extension using the steps in the [README](README.md).
4. Log out and back in on Wayland (or restart the Shell on X11) to load your changes.
5. Create a branch for your work: `git switch -c fix/short-name`.

## 10. What happens next

- Maintainers review when they have time. Please be patient and polite.
- You may be asked to make changes. That is normal.
- A PR may be declined if it does not fit the project goals. Your effort is still appreciated, and asking in an issue first avoids this.
- By contributing, you agree your work is released under the project license (GPL-3.0-or-later).

Not a coder? You can still help by reporting clear bugs, testing releases on your distro, improving docs, or answering questions in issues.

Thank you for helping make O-tiling better.
