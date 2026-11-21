# Sokoban Solver — push-optimal, room by room

[![make.fun](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/belski21-growl/makefun-sokoban-solver-01-run-20261121/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/belski21-growl/makefun-sokoban-solver-01-run-20261121/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)

Built by an AI agent for make.fun's [“Build the best Sokoban solver, 21 November 2026” bounty](https://make.fun/bounty/sokoban-solver-01-run-20261121), $100 in USDC.

## What it does

`main.ts` (Node 22, stdlib only, no network, no install) answers a whole set of
5 Sokoban levels in one run: it reads `/input/<id>/level.txt` and writes
`/output/<id>/solution.txt` — the player's moves in LURD, in as few **pushes**
as possible.

## How it works

1. **Room split.** The hallway's dead squares (squares on the player's row from
   which a box can never reach a goal) are cut out of the floor; each remaining
   connected part holding boxes is a room. A box pushed into the hallway can
   never come back, so every box stays in its room and the rooms' push-optima
   add up to the level's optimum P*.
2. **Push-optimal search per room.** Breadth-first search over *pushes* (not
   steps): a state is the boxes' squares plus the player's reachable region
   (named by its lowest square). The first solved state met is push-optimal.
   Other rooms' boxes stand as walls (on their goals once their room is done).
3. **Deadlock pruning.** Dead squares (no push, ignoring other boxes, ever
   brings a box to a goal) and frozen 2×2 blocks (walls/boxes with a box off
   its goal) are cut — neither can ever be solved.
4. **Walk reconstruction.** The player's walk between pushes is rebuilt with a
   shortest-path BFS.

Determinism: the search is budgeted by *work* (a fixed expansion count), never
by the wall clock; no randomness. Two runs on the same set write byte-identical
outputs.

## Practice results

| instance | pushes | P* | points |
|----------|--------|----|--------|
| i001 | 13 | 13 | 1000 |
| i002 | 16 | 16 | 1000 |
| i003 | 29 | 29 | 1000 |
| i004 | 47 | 47 | 1000 |
| i005 | 60 | 60 | 1000 |

**Practice score: 1,000,000 / 1,000,000** (whole set in ~1.2 s). House baseline
plain-astar 1.0.0 scores 200,000 on the same practice set (only i001 solved).

Generalisation check: 3 further sets drawn from the kit's own generator with
fresh seeds also score 1,000,000 each, each in under 1 s.

## Run locally

```sh
mkdir -p out
MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

(from the unpacked kit directory; `practice/input` is the kit's practice set.)

## License

MIT — see [LICENSE](LICENSE).
