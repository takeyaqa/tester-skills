---
name: design-pairwise-tests
description: Design pairwise test patterns with deterministic generation through PICT. Use this skill when the user wants pairwise coverage, all-pairs coverage, or a concrete pairwise test matrix from explicit factors, levels, and optional constraints. Also use it when the user explicitly asks for n-wise or t-way coverage stronger than pairwise. Prefer this skill for requests about pairwise test design, interaction coverage, t-way generation, or PICT-based pattern generation, but do not use it for generic exploratory testing or broad combinatorial analysis that is not centered on generated pairwise or n-wise patterns.
---

# Design Pairwise Tests

## Goal

Use the model to structure and review a pairwise test model by default. Always use `pict-cli` to generate the actual combination set. Do not let the LLM invent pairwise or n-wise combinations because that would introduce nondeterministic results where the CLI can provide deterministic output. Support n-wise coverage only when the user explicitly asks for stronger interaction coverage than pairwise.

## Compatibility

- Requires Node.js and npm access to execute `npx pict-cli@0.2.0`.
- Treat `pict-cli` execution as mandatory whenever the task asks for generated combinations, pairwise coverage, all-pairs output, or explicit n-wise or t-way coverage.
- If `pict-cli` cannot be run, stop at the reviewed factors, constraints, and PICT model, and explicitly say the final combinations were not produced because deterministic CLI execution was unavailable.

## Workflow

### 1. Clarify the requested coverage

- Extract the test objective, factors, levels, requested coverage strength, optional constraints, and notable exclusions.
- Default to pairwise coverage unless the user explicitly asks for stronger `3-wise`, `4-wise`, `n-wise`, `t-way`, or similar coverage.
- Call out the case-count tradeoff when the user asks for stronger or exhaustive coverage, and steer toward pairwise generation when exhaustive enumeration is likely too large.
- Ask for missing factors, levels, or constraint rules before generating combinations, and treat `suspected constraints` as review items until the user confirms them or the source material makes them explicit.
- Keep the workflow focused on explicit combinatorial inputs, and avoid forcing it onto reverse-engineering tasks, exploratory testing, or non-combinatorial test design.

### 2. Build a PICT model

- Normalize the user input into one factor per line.
- Keep a mapping back to the original business labels whenever identifiers are normalized.
- Encode only explicit constraints or direct domain implications. Treat constraints as optional support around the pairwise-first model, not as the primary purpose of the skill. Do not invent business rules.
- When writing constraints, follow [references/pict-constraints-grammar.md](references/pict-constraints-grammar.md).
- For `pict-cli` execution, write string values in constraints with double quotes. Use `IF [Browser] = "Safari" THEN [OS] = "macOS";`, not an unquoted string form.
- When using `IN { ... }` or `NOT IN { ... }`, quote each string value inside the set: `IF [Browser] = "Safari" THEN [OS] IN {"macOS", "iOS"};`.
- Keep the model readable enough that the user can review it before execution.
- If the source data is incomplete, show which candidate constraints were left out of the executable model and why.

Example model skeleton:

```text
OS: Windows, macOS
Browser: Chrome, Edge, Safari
AuthMode: Password, SSO
Locale: en, ja

IF [Browser] = "Safari" THEN [OS] = "macOS";
```

### 3. Run `pict-cli`

- This step is mandatory for any request that asks for concrete combinations or claims about pairwise or n-wise coverage.
- Use pairwise generation by default.
- If the user explicitly asks for stronger coverage, check the current `pict-cli` help or documentation for the correct way to request the required n-wise or t-way strength before running the command.
- Run `npx pict-cli@0.2.0 <model-file>`.
  - When file creation is unnecessary or piping is more convenient, send the model through stdin with `npx pict-cli@0.2.0 -`.
    - Example: `printf '%s\n' <model-text> | npx pict-cli@0.2.0 -`
- For constraint-heavy models, prefer a quick validation run with the exact quoted constraint text before presenting the final combinations.
- Treat the CLI output as the only valid source of truth for generated combinations.
- Keep the exact model text that was executed so the result can be audited or rerun.
- If the command fails, inspect the error, fix the model, and rerun instead of fabricating output.
- Do not claim pairwise or n-wise coverage unless the combinations came from `pict-cli`.
- Do not substitute an LLM-generated table when `pict-cli` is unavailable or failing.

### 4. Return a tester-friendly result

- State the requested coverage strength, and whether the result is pairwise by default or an explicitly requested n-wise run.
- Return the final PICT model used for generation.
- Return the generated combination table or list from `pict-cli`.
- Explain any normalized identifiers, assumptions, and omitted cases.
- Call out modeling gaps such as missing levels, ambiguous constraints, or rules that still need human confirmation.
- Summarize why a constraint was added when it materially changes the generated cases.
- If `pict-cli` could not be executed, do not return a fabricated combination list. Return the reviewed model and clearly state that deterministic generation is still pending.

## Expected Output Shape

Prefer this structure when the user wants a concrete deliverable:

1. Requested coverage strength
2. Factors and levels
3. Constraints and assumptions
4. PICT model
5. Generated combinations
6. Open questions or caveats

## Trigger Examples

These requests should strongly match this skill:

- "There are many input parameters, and I want to test them pairwise"
- "Generate all-pairs patterns for these factors and levels"
- "Create pairwise test patterns for signup options with a few explicit constraints"
- "I need 3-wise coverage for these configuration parameters"
- "Use PICT to generate t-way interaction coverage for this factor list"
