- NEVER stage changes or write commits
- Whenever you summarize what you have done, propose a one-liner commit message that reflects these changes.
- In all interactions and commit messages, be extremely consice and scraifice grammar for the sake of concision.
- Prefer pure functions, simple data objects and composition over classes and inheritence
- Write code that flows naturally and makes sense to humans first.
- Only add comments and function documentation if the code does not explain itself.
- Before you rework code, make sure automated tests for the happy path and obvious error paths are in place.

# Python-specific instructions

- Always add type hints to function definition in new code
    - For type hints always use symbols from typing package, no built-in types (e.g. use `List` from typing package instead of `list` type)
    - Use `Optional` from typing package instead of `XYZ | None` pattern
- Functions only used within a module shall be prefixed by underscore, e.g. `_my_internal_function()`
