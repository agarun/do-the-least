---
name: do-the-least
description: Implement a scoped code change with the smallest clean, idiomatic diff that fully solves the problem. Use when the user wants a high-quality implementation while avoiding over-engineering.
disable-model-invocation: true
---

# Do the least

Use this skill to implement changes with the most minimal diff needed to achieve the goal, while preserving high code quality, correctness, maintainability, cleanliness, and the codebase's idiom.

The aim is a simple, elegant diff where over-engineering is treated as a defect. Push the implementer to be disciplined about footprint. Do not merely avoid obvious bloat. Actively search for the framing of the change where unnecessary complexity and layering never need to exist, so the final implementation is direct and reads as if it was always part of the codebase.

## Process

### 1. Start from the baseline

Apply this baseline prompt:

> Implement the requested change with the smallest diff that fully solves the problem.
> If the codebase has a helper, pattern, or seam that already does most of the work, use it. Otherwise, reframe the changes and prune until landing on a minimal implementation that is thorough, direct, clean, and with correct behavior.
> Prefer deleting code over editing it, and surgically editing existing code over adding new code, when behavior and scope remain correct.
> Keep the implementation clean, minimal, and idiomatic to this codebase. Avoid over-engineering problems. Solve the problem completely, then stop.

### 2. Run the tight loop

1. Inspect the relevant code and tests.
2. Identify the existing helper, pattern, owner, or idiom that should carry the change.
3. Implement the smallest complete change.
4. Prune the draft line by line.
5. Run the smallest meaningful test, or state why no focused test applies.

### 3. Apply these lessons

1. **Reuse before writing.**
   - Inspect the code involved and its helpers, callers, or existing patterns. The codebase has potentially already solved an adjacent problem.
   - A near-duplicate of a canonical helper is a bug. Extend or call the canonical one.
   - Before writing a helper or adding a dependency, name the built-in or standard-library function that does the job. If it exists, use it.

2. **One implementation means no abstraction.**
   - Interfaces, factories, wrappers, base classes, and config earn their existence when a second concrete use arrives, not before.
   - Inline the single-use layer. It can be extracted later, in the diff that actually needs it.
   - No speculative flexibility: dead code or "for future use" code paths.

3. **Match the codebase's idiom.**
   - Follow the surrounding style, naming, error handling, and structure, even where your preferred idiom differs.
   - Prefer the shorter idiomatic form of the same logic. Drop comments that narrate what the code visibly does.
   - The change is done when a reader cannot tell where the old code ends and the new code begins.

4. **Handle the edge cases the problem names.**
   - Correct handling of the stated inputs, failures, and boundaries is part of the least, never an extra.
   - Guarding against states that cannot occur is decoration. Every guard must name the caller that can trigger it.

5. **Delete complexity, do not rearrange it.**
   - A change that moves the same moving pieces into new files did no work. Aim for fewer concepts, not fewer lines per file.
   - When a reframing makes whole branches or helpers unnecessary, take the reframing.

6. **Prune the diff before presenting it.**
   - Reread every line and ask: what breaks if I delete this? Delete every line with no answer.
   - This pass is mandatory. The first draft always contains lines that fail the question.

## Rules

### Standards (non-negotiable)

- The change fully solves the stated problem. Minimal never means incomplete: correctness, the named edge cases, and error handling that callers depend on are inside the scope, not beyond it.
- The diff stays inside the task. Drive-by refactors, reformatting, and opportunistic cleanups belong in their own change.
- New code goes in the layer that already owns the concept. Solving a local problem by scattering ad-hoc conditionals across shared code is a design failure.

### Defects

Fix these before finishing:

- A hand-rolled version of something the standard library or the existing platform ships.
- A single-use abstraction: interface, factory, wrapper, base class, config, or layer.
- Speculative flexibility: config nothing sets, flags nothing flips, parameters every caller passes identically, "for future use" code paths.
- An ad-hoc conditional bolted into a shared flow.
- Defensive handling of states no caller can produce.
- Comments narrating what the code visibly does.
- The same logic available in a shorter idiomatic form.
- A file or function that grew substantially when the new logic had a natural home elsewhere.

### When the least is bigger

- Sometimes the genuinely simplest correct solution is a larger change: the bug is in a shared helper, or the clean fix removes a workaround others depend on. If the fix belongs in those helpers or workarounds, take that path. If it changes scope, risk, or user intent, explain the tradeoff and ask before proceeding.
- Do the least means the least total complexity over the code's life, not the least typing today. Never contort a change to keep the line count down.

### Output readiness

We are ready when:

- The stated problem is fully solved, including edge cases and errors.
- No draft defect remains without a reason.
- Remaining lines survive the prune question: what breaks if I delete this?
- The smallest meaningful test passed, or the absence of a focused test is explained.
