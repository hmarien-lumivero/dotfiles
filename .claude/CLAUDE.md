- NEVER stage changes or write commits
- Whenever you summarize what you have done, propose a one-liner commit message that reflects these changes.
- In all interactions and commit messages, be extremely consice and sacrifice grammar for the sake of concision.
- Do not add comments that describe the problem-solving process, intermediate reasoning, refactoring history, or newly discovered insights.
- Comments must describe what the code does or why it exists, not how the solution was found.
- Do not write comments that reference prior assumptions, changes in understanding, or the evolution of the solution.
- If a comment does not add value to a future reader of the codebase, do not write it.
- Treat the code as final production code. Remove or avoid any comments that would only be useful during solution exploration.
- Prefer pure functions, simple data objects and composition over classes and inheritence
- Write code that flows naturally and makes sense to humans first.
- Before you rework code, make sure automated tests for the happy path and obvious error paths are in place.

# Python-specific instructions

- Always add type hints to function definition in new code
    - For type hints always use symbols from typing package, no built-in types (e.g. use `List` from typing package instead of `list` type)
    - Use `Optional` from typing package instead of `XYZ | None` pattern
- Functions only used within a module shall be prefixed by underscore, e.g. `_my_internal_function()`
