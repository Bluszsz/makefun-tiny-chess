# Tiny Chess — tiny-chess-01

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Bluszsz/makefun-tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Bluszsz/makefun-tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A deterministic chess engine in a single `main.py` (3,331 bytes) for the
[make.fun](https://make.fun) `tiny-chess-01` competition ($500). Python
3.13, standard library only. It plays through the env over HTTP
(`MAKEFUN_ENV_URL`): `POST /game`, `POST /move`, `POST /resign`.

## Engine

- 0x88 board, full legal move generation (castling, en passant, promotion)
- Alpha-beta + quiescence (captures/promotions, all evasions when in check)
- Transposition table (Zobrist, deterministic LCG), MVV-LVA + killer
  ordering, check extension
- Piece-square tables (symmetric, 4 files stored) + bishop pair +
  endgame-king table swap + passed-pawn bonus
- Fixed budget: 40,000 nodes/move, max depth 8 — no wall clock, no
  unseeded randomness, so replays are byte-identical

`main.py` is the kit's `makefun-compress/v1` packing of the readable
source `source.py` (LZMA stream embedded as a latin-1 string literal);
the stub and the payload together are the 3,331 bytes. The readable
source is sent to make.fun with the transcript bundle, not committed.
`main.py` reads, imports or execs no other file in the repository.

## Practice results (local, kit env server)

Engine lineage (40k nodes/move + passed-pawn bonus), 6-game practice
gauntlet per trial, floor Elo 1154:

| trial | Elo | W–D–L |
| --- | --- | --- |
| trial-0 | 1191 | 2–1–3 |
| trial-1 | 1654 | 5–0–1 |
| trial-2 | 1364 | 3–1–2 |
| **mean** | **1403** | |

House baseline (sunfish 4K) is ~1365 mean on the same gauntlet.
Average think time ~1.5–1.9 s/move, max ~3.2 s (limit 10 s/move).

Note: the trials above were run with the pre-golfing build. Fitting the
4,096-byte cap required dropping the queen/bishop piece-square tables,
late-move reductions and the history heuristic from the shipped build;
those cuts are small but unquantified — no full trial of the packed
build completed (a local trial run was interrupted by a runtime
restart mid-way, after 181 error-free moves).

## Licence

MIT — see [LICENSE](LICENSE).
