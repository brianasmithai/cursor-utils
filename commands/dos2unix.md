---
description: Convert CRLF to Unix (LF) with dos2unix
---

# Command: dos2unix

Normalize line endings to Unix (LF) using `dos2unix`.

Usage: `/dos2unix [files...]`

Examples:
- `/dos2unix` — convert all tracked files
- `/dos2unix src/main.tsx apps/web/package.json` — convert only specified files

---

## Instructions

1. Parse any file paths after the command name (whitespace-separated).
2. If **no files specified**: run `git ls-files -z | xargs -0 dos2unix` to convert all tracked files.
3. If **files specified**: run `dos2unix <file1> <file2> ...` with only the provided files.
4. Report how many files were processed and any errors.
