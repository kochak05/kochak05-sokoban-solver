# Sokoban Solver

Push-optimal Sokoban solver for make.fun's `sokoban-solver-01-run-20261121`
("Build the best Sokoban solver, 21 November 2026"). One program, `main.ts`
(Node 22, built-ins only), answers all 5 instances of a set in one run.

## How it works

Each level is a hallway with 1–4 rooms off it. A box pushed out of a room's
door into the hallway can never be pushed back, so every box stays in its
room and the level's push-optimal count is the sum of the rooms' optima.
The solver therefore splits each level into rooms (cutting the hallway's
dead squares out of the floor) and solves every room on its own,
push-optimally, then concatenates the room solutions — the player walks on
from where the last room left it.

Each room is solved by breadth-first search over pushes: a state is the
boxes' squares plus the player's reachable region (named by its lowest
square), so the first solved state met is push-optimal. Two deadlocks are
pruned: boxes on dead squares (no pull, ignoring the other boxes, brings
one to a goal) and frozen 2×2 blocks of walls and boxes holding a box off
its goal.

Deterministic by construction: fixed work budgets (250,000 states per
search, 1,000,000 on a second-chance retry), fixed iteration orders, no
clock, no randomness. Two runs on the same input write the same bytes.

## Run

```sh
node main.ts
```

Reads `$MAKEFUN_INPUT_DIR` (default `/input`) — `<id>/level.txt` per
instance — and writes `$MAKEFUN_OUTPUT_DIR` (default `/output`) —
`<id>/solution.txt`, the player's moves in LURD.

## Practice score

`node cli/makefun.mjs grade-local --kit . --bot <repo> --driver process`
on the kit's practice set: **1,000,000** — all 5 instances solved in P*
pushes (i001: 13, i002: 16, i003: 29, i004: 47, i005: 60), 5/5 answered.
Whole-set runtime ~1s on one CPU, far inside the 60s wall budget; L1
regrade reproduced byte for byte.
