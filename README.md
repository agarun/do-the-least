# do-the-least

Fix your bug or build your feature with the least sufficient change. Works best with small changes where you weren't planning on over-engineering and need to reign in the beast.

## Install

```bash
npx skills add agarun/do-the-least
```

## What it does

Constrains how a change is built: find the smallest diff that fully solves
the problem, reuse what exists, write it in the codebase's idiom, then prune
line by line.

## Use

The skill never fires on its own, so you must invoke it:

```
fix this bug but /do-the-least
(claude) /do-the-least add 'theme' picker to user settings
(codex) $do-the-least add 'theme' picker to user settings
```
