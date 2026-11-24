# tiny-chess-01 submission v2 — "Pipi" engine, optimised build

A ≤4KB Python chess engine for make.fun tiny-chess-01, written from scratch
(no Sunfish code). Pure standard library; no network except the tournament
env protocol (MAKEFUN_ENV_URL); no model/API calls. V2 replaces the first
submission (kept in ../submission) with a stronger search.

## Files

| file | bytes | notes |
|---|---|---|
| main.py | 4082 | the whole engine (minified + lzma + base85). Limit 4096. |
| LICENSE | 1096 | MIT, (c) 2026 itspipiya |
| README.md | this file | |

Repo total stays under the 8192-byte snapshot cap.

## What changed vs v1

- **Late move reductions (LMR)**: quiet moves after the first few at
  depth ≥ 3 are searched one ply shallower first and re-searched at full
  depth only if they beat alpha.
- **Killer-move ordering**: one killer per ply, ordered below captures and
  above other quiet moves.
- Byte budget freed without touching evaluation: PSTs are stored as
  half-rows and mirrored at decode (tables are file-symmetric; decoded
  values verified identical to v1), the queen PST stays zero, the ep
  Zobrist keys reuse the castling keys, plus assorted micro-squeezes.
- Unchanged from v1: 0x88 board, fully legal movegen (perft-verified:
  startpos d4=197281, kiwipete d3=97862, CPW 3–6), iterative deepening
  under a fixed **72,000 nodes/move** budget (no wall clock), negamax
  alpha-beta with PVS, transposition table, check extensions, quiescence
  search with delta pruning, MVV-LVA + TT + killer ordering, repetition
  and fifty-move draws scored 0 in search, material 100/320/330/500/900
  + PSTs (10cp grid), bishop pair, doubled/isolated/passed pawns, blended
  king endgame table. Fixed Zobrist seed — deterministic.

## Results (official kit harness + scorer, packed main.py itself)

Practice trials (6 games each; finals floor 1154; house Sunfish baseline
practice mean ≈1365):

| instance | v1 (previous) | v2 (this entry) |
|---|---|---|
| trial-0 | 1279 (2W 2D 2L) | **1451** (4W 0D 2L) |
| trial-1 | 1451 (4W 0D 2L) | **1545** (4W 1D 1L) |
| trial-2 | 1279 (3W 0D 3L) | **1364** (3W 1D 2L) |
| mean | 1336 | **1453** |

Paired per-instance gains: +172 / +94 / +85 (mean +117). Head-to-head at
36k nodes/move over 10 book games (colours swapped): v2 beat v1 5.5–4.5
(4W 3D 3L). All 28 test games ended by checkmate/draw rules — no timeouts,
illegal moves or crashes.

Timing (local runs): average think 2.5–2.7 s/move, slowest move 4.4 s
(limit 10 s). Projected 8-game final wall ≈ 1,060–1,250 s of the 1,800 s
budget, house thinking included.

## Not yet done for a real submission

- A human must confirm before submitting (per instructions).
- Submission channel: public GitHub repo + commit, or `makefun bundle`
  upload; must attach AI-session transcripts and pass the transcript
  gate (≥30% of code lines traceable, main.py edited ≥2 times).
- Account @itspipiya still needs a Solana wallet linked before payout.
