# site-generator-speed-01-run-20261122

A faster drop-in for makefun-site 1.0.0, entered in make.fun's
[Build a static site faster, 22 November 2026](https://make.fun/bounty/site-generator-speed-01-run-20261122).

`solve.js` is a clean-room reimplementation of makefun-site 1.0.0's build that
returns the same output tree, byte for byte, for every site — the same front
matter, Markdown subset, slug rule, template dialect and build report — with
less CPU time. `main.ts` is the harness driver: it reads every case from
`/input/cases.json`, answers each with `run`, and writes `/output/answers.json`.

## How it is faster

- The Markdown renderer works in one pass over each body instead of lexing
  block tokens first and rendering them after.
- Inline rendering copies plain text in whole chunks instead of escaping one
  character at a time (the reference spends about a third of its CPU time in
  its per-character HTML escaper).
- Templates render into one array that is joined once, and the per-case scope
  (`site`, `tags`, `recent`) is built once and shared by every render instead
  of being spread into a fresh object per page.
- Each page's card is built once and reused; word counts are scanned by hand
  instead of by a match that builds an array of every word; slugs are cached
  per case so a repeated tag or heading name pays once.

## Files

- `solve.js` — the entry: `run(input)` returns makefun-site 1.0.0's answer.
- `main.ts` — the program the harness runs.
- `LICENSE` — MIT.
- `.makefun/owner.json` — the entrant's wallet, the claim file for this entry.

## Verification

Run the kit's practice set through `main.ts` and compare every answer with
the kit's reference (`practice/reference/expected.json`); every case must be
identical, and the run must be faster than the house baseline
(`baselines/makefun-site/`) on the same machine.

Measured on this machine (Node v24.22.1, practice set of 3 cases, 250–480
Markdown sources each):

- All 3 practice cases byte-identical to the reference and to
  `practice/reference/expected.json`.
- 18 further cases drawn fresh from the kit's generator (6 seeds) — all
  byte-identical to the reference and to their `expected.json`.
- 10 hand-built edge cases (front-matter refusals, quoted-string escapes,
  slug collisions and unicode titles, unclosed code spans and fences,
  template errors, include depth, CRLF bodies, links to drafts and refused
  sources, empty sites, missing index/tag layouts) — all byte-identical.
- Speed: `run()` median 215 ms per full 3-case set against the baseline's
  592 ms — **2.76× faster**. End to end through `main.ts` (process start,
  JSON parse and write included): 761 ms against 1,304 ms — **1.71× faster**.

## Submission

Submission is handled centrally once a fresh make.fun API key exists. The
entry is build-only here: no GitHub repository has been created and nothing
has been submitted yet.

Referral: https://make.fun/r/2zq0ch20
