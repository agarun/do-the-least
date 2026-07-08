---
name: do-the-least
description: Implement a change with the smallest clean, minimal, idiomatic diff that fully solves the problem. Avoids over-engineering.
disable-model-invocation: true
---

# Do the least

Use this skill to implement changes with the most minimal diff needed to achieve the goal, while retaining high code quality, maintainability, and cleanliness.

The aim is a simple, elegant diff where over-engineering is treated as a defect. This skill should push the implementer to be disciplined about footprint. Do not merely avoid obvious bloat. Actively search for the framing of the change where unnecessary complexity and layering never needs to exist at all, so the final implementation is direct and reads as if it was always part of the codebase.

## Process

### 1. Apply the prompt below:

Start from the baseline:

> Implement the requested change with the smallest diff that fully solves the problem.
> If the codebase has a helper, pattern, or seam that already does most of the work, use it. Else, reframe the changes until landing on a minimal inmplementation that is thorough, direct, clean, and with correct behavior.
> Prefer deleting code over editing it, and surgically editing existing code over adding new code, if possible.
> Keep the implementation clean, minimal, and idiomatic to this codebase. Avoid over-engineering problems. Solve the problem completely, then stop.

### 2. Apply these lessons:

Apply the baseline prompt, plus these numbered lessons:

1. **Reuse before writing.**
   - Check for the existing helper, utility, or pattern first. The codebase has potentially already solved an adjacent problem.
   - A near-duplicate of a canonical helper is a bug. Extend or call the canonical one.
   - Before writing a helper or adding a dependency, name the built-in that does the job. If it exists, use it.

2. **One implementation means no abstraction.**
   - Interfaces, factories, wrappers, base classes, and config earn their existence when a second concrete use arrives, not before.
   - Inline the single-use layer. It can be extracted later, in the diff that actually needs it.

3. **Match the codebase's idiom.**
   - Follow the surrounding style, naming, error handling, and structure, even where your preferred idiom differs.
   - The change is done when a reader cannot tell where the old code ends and the new code begins.

4. **Handle the edge cases the problem names.**
   - Correct handling of the stated inputs, failures, and boundaries is part of the least, never an extra.
   - Guarding against states that cannot occur is decoration. Every guard must name the caller that can trigger it.

5. **Delete complexity, do not rearrange it.**
   - A change that moves the same moving pieces into new files did no work. Fewer concepts, not fewer lines per file.
   - When a reframing makes whole branches or helpers unnecessary, take the reframing.

6. **Prune the diff before presenting it.**
   - Reread every line and ask: what breaks if I delete this? Delete every line with no answer.
   - This pass is mandatory. The first draft always contains lines that fail the question.

## Rules

### Standards (non-negotiable)

- The change fully solves the stated problem. Minimal never means incomplete: correctness, the named edge cases, and error handling that callers depend on are inside the scope, not beyond it.
- The diff stays inside the task. Drive-by refactors, reformatting, and opportunistic cleanups belong in their own change.
- New code goes in the layer that already owns the concept. Solving a local problem by scattering checks across shared code is a design failure.

### Flag in your own draft

Treat any of these in the working diff as a defect to fix before finishing:

- A hand-rolled version of something the standard library or platform ships.
- An abstraction with one implementation: a single-use interface, a factory with one product, a wrapper that only delegates, a layer with one caller.
- Speculative flexibility: config nothing sets, flags nothing flips, parameters every caller passes identically, "for future use" code paths.
- An ad-hoc conditional bolted into a shared flow to serve one case.
- Defensive handling of states no caller can produce.
- Comments narrating what the code visibly does. Keep only comments stating what the code cannot say, such as a non-obvious constraint.
- The same logic available in a shorter idiomatic form.
- A file or function that grew substantially when the new logic had a natural home elsewhere.

### Remedies to prefer

When a flag fires, prefer these moves, in order:

- Delete the code and let the problem be solved by what already exists.
- Replace the code with the standard library or platform equivalent.
- Inline the single-use layer into its only caller.
- Move the logic to the module that already owns the concept.
- Collapse duplicate branches into one flow.
- Reframe the change so the conditional or mode never needs to exist.

### When the least is bigger

- Sometimes the genuinely simplest correct solution is a larger change: the bug is in a shared helper, or the clean fix removes a workaround others depend on. Say so explicitly and propose it before building it.
- Do the least means the least total complexity over the code's life, not the least typing today. Never contort a change to keep the line count down.

## Output

When presenting the finished change:

1. State the problem and the approach in one or two sentences, including what existing code the change builds on.
2. Present the diff. It should already have survived the prune pass in lesson 7.
3. Name the test that proves the new behavior and show that it passes.
4. If a plausible smaller or larger framing was considered and rejected, say why in one sentence. Otherwise say nothing about alternatives.
5. Report the net size of the change: files touched, rough lines added and removed, dependencies added (which should almost always be zero).

Do not pad the output with restated requirements, tour-guide commentary on the diff, or speculative follow-up work. The bar for done: the problem is fully solved, the tests pass, and nothing in the diff can be deleted without breaking something.
