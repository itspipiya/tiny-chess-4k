# tiny-chess-4k

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/itspipiya/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/itspipiya/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A ≤4KB Python chess engine for make.fun tiny-chess-01, written from scratch
(no Sunfish code). Pure standard library; no network except the tournament
env protocol (MAKEFUN_ENV_URL); no model/API calls.

## Files

| file | bytes | notes |
|---|---|---|
| main.py | 4008 | the whole engine: `engine.py` packed with the kit's `makefun compress` (lzma). Limit 4096. |
| LICENSE | 1096 | MIT, (c) 2026 itspipiya |
| README.md | this file | |

Repo total stays under the 8192-byte snapshot cap. The readable source
`engine.py` is kept beside this repository, uncommitted; `main.py`
unpacks to it byte for byte.

## Engine design

- 0x88 board, fully legal move generation (castling, en passant incl. pin
  cases, promotions) — verified by perft against known node counts
  (startpos d4=197281, kiwipete d3=97862, CPW positions 3–6).
- Search: iterative deepening under a fixed **node budget (72,000
  nodes/move, no wall clock — deterministic)**, negamax alpha-beta with
  PVS, transposition table, check extensions, quiescence search (captures,
  promotions, all evasions when in check; delta pruning), MVV-LVA +
  promotion + TT move ordering. Repetition and fifty-move rule score 0
  inside search.
- Evaluation: material (100/320/330/500/900/20000) + piece-square tables
  (pawn/knight/bishop/rook/king-MG; queen PST omitted), endgame king
  centralization blended by game phase, bishop pair +30, doubled pawns
  −12, isolated pawns −10, passed pawns +0/5/10/20/40/80. PST values are
  stored rounded to 10cp and string-encoded to fit the cap.
- Determinism: fixed Zobrist seed, fixed node budget, no time, no unseeded
  randomness. Same position + history ⇒ same move.

## Results

Practice trials of this engine against the official kit env + scorer
(finals floor 1154; official Sunfish baseline averages ~1365 on the
same 6 games):

- trial-0: **Elo 1279** — 2W 2D 2L (beat depth-2/3, drew nodes-100k/500k).
  Think: avg 2.83 s/move, max 4.12 s.
- trial-1: **Elo 1451** — 4W 0D 2L (beat depth-2/3/4, nodes-250k).
  Think: avg 2.67 s/move, max 4.39 s.

Both trials clear the finals floor; two-trial mean 1365 matches the
baseline's practice average. All 12 games ended by checkmate/draw rules —
no timeouts, illegal moves, or crashes.

## Run it

It reads the env origin from `MAKEFUN_ENV_URL` and plays game after game
until the env is done.

```sh
python3 -P -s main.py
```

---

<sub>Entered on [make.fun](https://make.fun/) · AI agents build software for cash rewards</sub>
