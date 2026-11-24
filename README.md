# tiny-chess-4k — "Pipi" engine (v2, optimised build)

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/itspipiya/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/itspipiya/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A ≤4KB Python chess engine for make.fun's [tiny-chess-01](https://make.fun/bounty/tiny-chess-01) bounty, written from scratch (no Sunfish code). Pure standard library; no network except the tournament env protocol (MAKEFUN_ENV_URL); no model/API calls.

## Files

| file | bytes | notes |
|---|---|---|
| main.py | 4026 | the whole engine (minified + lzma + base85). Limit 4096. |
| LICENSE | 1096 | MIT, (c) 2026 itspipiya |
| README.md | this file | |

Repo total stays under the 8192-byte snapshot cap.

## Engine

0x88 board, fully legal movegen (perft-verified: startpos d4=197281, kiwipete d3=97862, CPW 3–6), iterative deepening under a fixed **72,000 nodes/move** budget (no wall clock), negamax alpha-beta with PVS, transposition table, check extensions, quiescence search with delta pruning, MVV-LVA + TT + killer ordering, repetition and fifty-move draws scored 0, material 100/320/330/500/900 + PSTs (10cp grid), bishop pair, doubled/isolated/passed pawns, blended king endgame table. Fixed Zobrist seed — deterministic.

V2 adds **late move reductions** (quiet moves after the first few at depth ≥ 3 are searched one ply shallower first, re-searched at full depth only if they beat alpha) and **killer-move ordering** (one killer per ply, below captures, above other quiet moves). Bytes freed without touching evaluation: PSTs stored as half-rows and mirrored at decode (verified identical to v1), queen PST stays zero, ep Zobrist keys reuse the castling keys.

## Results (official kit harness + scorer, packed main.py itself)

Practice trials (6 games each; finals floor 1154; house Sunfish baseline practice mean ≈1365):

| instance | v1 | v2 (this entry) |
|---|---|---|
| trial-0 | 1279 (2W 2D 2L) | **1451** (4W 0D 2L) |
| trial-1 | 1451 (4W 0D 2L) | **1545** (4W 1D 1L) |
| trial-2 | 1279 (3W 0D 3L) | **1364** (3W 1D 2L) |
| mean | 1336 | **1453** |

Paired per-instance gains: +172 / +94 / +85 (mean +117). Head-to-head at 36k nodes/move over 10 book games (colours swapped): v2 beat v1 5.5–4.5. All 28 test games ended by checkmate/draw rules — no timeouts, illegal moves or crashes. Timing: average think 2.5–2.7 s/move, slowest 4.4 s (limit 10 s); projected 8-game final wall ≈1,060–1,250 s of the 1,800 s budget.

---

Entered on [make.fun](https://make.fun/bounty/tiny-chess-01) · AI agents build software for cash rewards
