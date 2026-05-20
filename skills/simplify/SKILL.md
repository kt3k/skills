---
name: simplify
description: |
  Read source code and find opportunities to simplify, deduplicate, and consolidate.
  Apply changes after user approval.
---

### Arguments (optional)

- `<scope>`: A file path, directory, or free-form description of the range to
  review (e.g. `src/foo.ts`, `the auth module`, `lines 100-200 of bar.py`).

### Steps

1. **Resolve the scope**:
   - If an argument is provided, use it as the review scope.
   - If no argument is provided:
     - Check the current git branch (`git rev-parse --abbrev-ref HEAD`).
     - If the current branch is NOT the main branch (e.g. `main`/`master`),
       default to **Current branch diff** (changes in the current branch vs
       the main branch) without prompting.
     - Otherwise, ask the user explicitly which scope to use,
       using `AskUserQuestion` with these options:
       - **Whole codebase** — review all source code in the repository
       - **Current branch diff** — review changes in the current branch vs the main branch
       - **Last commit** — review changes in the most recent commit (`git show HEAD`)
       - **Other** — let the user specify a custom scope (free-form input)
   - Do NOT proceed until the scope is determined.

2. **Review**: Read the source code in the resolved scope and identify areas
   that can be simplified, duplicated parts, and opportunities for refactoring
   or abstraction.

3. **Apply changes after user approval.**

4. **Commit & push (branch diff scope only)**: If the resolved scope was
   **Current branch diff** and changes were applied:
   - Automatically create a commit with the applied changes (no extra
     confirmation needed — the user already approved the changes in step 3).
   - If the current branch has a remote tracking branch
     (`git rev-parse --abbrev-ref --symbolic-full-name @{u}` succeeds), push
     to that remote.
   - If there is no remote tracking branch, skip pushing (do NOT create a new
     remote branch automatically).
