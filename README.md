# cursor-utils

Utilities, snippets, and conventions for building with Cursor.

## Version

Current collection version: `v0.1.1` (from `VERSION`). Update this number when making structural or documentation changes.

## Repository layout

- `snippets/` – language-specific code snippets ready to paste into Cursor prompts or insert via code actions.
  - `python/` – Python helpers, patterns, and quickstarts.
  - `javascript/` – JavaScript/TypeScript snippets and browser-focused utilities.
- `commands/` – ready-to-use Cursor commands (with descriptions and sample invocations).
- `rules/` – reusable Cursor rulesets (e.g., coding standards, review checklists, prompt presets).
- `utilities/` – small scripts or tools that support Cursor workflows (formatting helpers, lint wrappers, etc.).
- `templates/` – prompt, issue, or PR templates tailored for Cursor-driven projects.
- `docs/` – higher-level guides explaining how to apply the snippets, commands, and rules together.
- `VERSION` – version identifier for the repository layout and documentation.

## Contribution guidance

1. Choose the best home for a new item based on the layout above.
2. Add a short README or inline comments inside the relevant folder to explain intent and usage.
3. Keep filenames descriptive (e.g., `async_retry.py`, `api-request.ts`, `code-review.rules.md`).
4. Update the top-level `README.md` and bump `VERSION` when changing structure or adding new top-level categories.
5. Prefer small, focused examples that are easy to copy into Cursor.

### Submitting changes (including via Codex)

- Use a feature branch for your changes and commit with clear messages.
- Run any relevant tests or linters before opening a PR and note the commands in the PR description.
- Open a pull request for review. Codex users can do this directly from the CLI by committing locally and invoking the PR helper command once the branch is ready.
- In the PR description, summarize what changed and whether `VERSION` was bumped (it should be bumped for structural or documentation updates like README edits).

## Future additions

- Populate language folders with starter snippets (testing harnesses, API client skeletons, error handling patterns).
- Add command catalogs with expected inputs/outputs and safety notes.
- Provide rule bundles for common stacks (backend services, front-end apps, data workflows).
- Document typical Cursor workflows in `docs/` with links to related snippets and commands.
