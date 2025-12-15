---
name: integration-operation-segregation
description: Apply the Integration–Operation Segregation Principle (IOSP) to keep orchestration (integration) separate from work (operations) using plain functions and simple data objects.
---

# Integration–Operation Segregation (IOSP)

## Overview

Keep orchestration separate from work. A function either **integrates** (orchestrates other functions) or **operates** (does the heavy lifting) — never both.

**Core principle:** One function, one level of detail. Integration stitches steps together; operations perform the steps.

**Violating the letter of the rules is violating the spirit of the rules.**

## When to Use

**Always:** 
- New features
- Major refactorings
- Behavior changes
- API integrations and data pipelines

**Exceptions (ask your human partner):**
- Throwaway prototypes
- One-off scripts that will not be maintained

Thinking “mix just this once”? Stop. That’s rationalization.

## The Iron Rule

```
NO FUNCTION MIXES INTEGRATION AND OPERATION
```

If it does, split it. Now.

## Roles

### Integration (orchestration)
- Coordinates the high-level workflow by calling **your** functions (other integrations or operations).
- Contains **no** inline calculations, parsing, transformation, I/O calls, or library/framework calls.
- Uses basic control flow only to choose **which** operation to call and **in what order**.
- Expresses business logic as a composition of operations and (if needed) smaller integrations.
- Input/Output: passes and returns simple data objects.

### Operation (work)
- Performs the detailed task: computation, transformation, validation, parsing, persistence calls, external API calls, etc.
- Calls standard library or third‑party APIs as needed, but **does not call your own custom functions**.
- Has a single, clear purpose at a single level of detail.
- Is side‑effect free when possible; when side effects are required (e.g., I/O), they are localized and explicit.
- Input/Output: consumes and returns simple data objects.

## Core Rules

- Separate integration from operation: every function is either integration **or** operation — never both.
- Keep logic out of integration control flow: if a condition or loop body needs logic, delegate to a tiny operation.
- One level of detail per function: integrations are high-level, operations are low-level.
- Small and focused functions: one reason to change (SRP).
- Use plain functions and simple data objects; no framework ceremony is required.
- **Never** encode the words “Integration” or “Operation” in function names or documentation — name by domain intent.

> **Explicit references:**  
> - *Domain-Driven Design (DDD):* Integration functions map to application services/handlers; operations map to domain services/pure domain functions. Keep integrations at the application boundary; keep operations in the domain/policy layer.  
> - *Clean Architecture:* Place integrations in the use‑case/interactor layer (outer policy), and operations in the inner policy/domain layer. Keep dependencies flowing inward.

## Refactoring Playbook (mechanical steps)

1. **Scout:** Locate functions that both coordinate and compute (big/nested/branchy functions). 
2. **Mark:** Highlight orchestration statements vs. work statements.
3. **Extract operations:** For each logical chunk of work (calculation, parse, check, call), extract a pure operation function.
4. **Extract conditions:** Replace complex `if`/`while` conditions in integrations with tiny boolean‑returning operations.
5. **Flatten loops:** Replace loop bodies in integrations with calls to a single operation per iteration.
6. **Inline external calls:** Move library/framework calls from integrations into operation functions.
7. **Tidy signatures:** Pass only the data each operation needs; return simple results (avoid global state and hidden dependencies).
8. **Rename for intent:** Give integrations business names (use‑case verbs); give operations capability names (action + subject).
9. **Re‑read at one level:** After extraction, an integration should read as a clear sequence of named steps with no inner logic.
10. **Repeat:** Continue splitting until each function’s role is unmistakable.

## Testing Strategy

- **Integration tests (few):** Verify that integrations call the right operations in the right order and handle branching. Avoid mocking operations you own unless necessary to isolate side effects.
- **Operation tests (many, focused):** Unit‑test each operation’s behavior exhaustively. These are easy to write because operations are independent and narrow.
- Keep tests close to their targets and name by behavior. Favor real code over mocks; mock external boundaries only.

## Naming & Documentation

- Name by **intent**, not by role. Use domain terminology (e.g., `calculate_total`, `select_shipping_method`).
- Do **not** mention “integration” or “operation” in names or docstrings.
- Keep docstrings short and action‑oriented; prefer clear function names and focused signatures.

## Data and Dependencies

- Prefer simple, immutable data objects: 
  - Python: tuples, `NamedTuple`, `dataclass` (as a plain DTO).
  - C#: `record`/`record struct` or simple DTOs.
- Pass dependencies explicitly into operations (function parameters). Avoid global/singleton state.
- Avoid DI frameworks in operations; integrations provide any collaborators explicitly.
- Use pure functions where possible; localize side effects in dedicated operations.

## Architecture Mapping

- Keep integrations at the edges where policies are assembled (use cases, request/command handlers, schedulers).
- Keep operations in the inner policy/domain layer as stable utilities or domain capabilities.
- Define clear module boundaries: integrations depend on operations; operations do not depend on integrations.
- External adapters (DB, HTTP, filesystem) are invoked **from operations** dedicated to that boundary.

> **Explicit references:**  
> - *DDD:* Application services (integration) coordinate domain operations; domain services and entities expose operation‑level behavior.  
> - *Clean Architecture:* Use cases (integration) invoke enterprise/business rules (operations); adapters implement I/O behind operation functions.

## Verification Checklist

- [ ] No function mixes orchestration with detailed work.
- [ ] Every complex condition/loop in an integration calls an operation for the logic inside.
- [ ] Each operation has a single, testable behavior with narrow inputs/outputs.
- [ ] Integrations read as a sequence of named steps at one level of abstraction.
- [ ] External API/library calls live inside operations, not integrations.
- [ ] Tests: few integration tests; many, focused operation tests.
- [ ] Function and doc names express intent; no “Integration/Operation” wording.
- [ ] Data passed as simple objects; no hidden dependencies or globals.
- [ ] Dependencies flow inward: integrations depend on operations; never the reverse.

## Red Flags — STOP and Refactor

- Function both orchestrates and computes.
- Integration contains inline parsing/validation/I/O.
- Complex boolean conditions or loop bodies inside an integration.
- Operation calls your custom functions or mixes several concerns.
- Hard‑to‑test functions that require many mocks (hint: roles are mixed).
- Vague names that describe *how* instead of *what*. 

## When Stuck

| Problem | Try |
|--------|-----|
| Integration full of logic | Extract one operation per logical chunk (condition, calculation, call). |
| Operation too broad | Split by single responsibility and level of detail. |
| Many mocks needed | Move I/O and external calls into dedicated operation functions; keep integrations thin. |
| Hard to name a function | Scope is mixed; split until a precise name appears. |
| Tests complicated | Your function mixes roles; separate integration from operations. |

## Final Rule

```
One function → one role → one level of detail
```

No exceptions without your human partner’s permission.
