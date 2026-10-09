# AI Chessathon Agent

**Python 3.12 · python-chess · Numba · Bitboards · Search Algorithms**

A chess engine developed for the AI Chessathon, built around a custom search and evaluation stack with a separate Numba-compiled bitboard implementation for performance experiments. The project focuses on move-generation correctness, search efficiency, and reliable execution under strict competition time and memory limits.

## What It Does

The agent receives a position in FEN format and returns a legal chess move in UCI notation. The engine combines established search techniques with custom evaluation, move ordering, and time management.

- **Search:** Iterative deepening, negamax, alpha-beta pruning, principal variation search (PVS), and quiescence search.
- **Evaluation:** Tapered material and piece-square table evaluation.
- **Move Ordering:** MVV-LVA, with additional pruning and reduction techniques in the optimized implementation.
- **Performance Experiments:** A separate bitboard engine compiled with Numba to reduce Python overhead in move generation and search.
- **Reliability:** Legal-move validation, fallback handling, time management, and automated testing.

## Engine Architecture

### Reference Engine

The reference implementation uses `python-chess` for board representation and move generation, with custom modules for search, evaluation, move ordering, engine state, and time management.

The modular design provides a baseline for testing changes to the search algorithm and comparing the behavior of alternative implementations.

### Numba Bitboard Implementation

A separate experimental engine uses flat bitboard representations and Numba-compiled search routines. It has been developed alongside the reference engine rather than replacing it outright.

Implemented components include:

- Magic-bitboard move generation and make/unmake operations.
- Incremental Zobrist hashing.
- Incremental material and piece-square table evaluation.
- Negamax, principal variation search, and quiescence search.
- Transposition tables and MVV-LVA move ordering.

The implementation has been checked against standard perft positions and the reference engine at fixed search depths.

More advanced features, including killer moves, history heuristics, null-move pruning, and late move reductions, have been identified as further integration work for this implementation.

## Performance and Testing

On the measured middlegame benchmark, the Numba implementation achieved a median of approximately **1.1 million search nodes per second at a median depth of 7**. The reference implementation measured approximately **19,600 nodes per second at depth 5** on the same benchmark.

The two implementations were also compared at fixed depths from 4 to 7 on a test position, producing identical scores and best moves under the tested configuration.

Additional validation includes:

- **Perft Testing:** Six standard chess positions, with exact results through depth 5 and depth 6 for the starting position.
- **Hashing Verification:** Incremental Zobrist hashing checks against full recomputation.
- **Evaluation Verification:** Incremental evaluation checks against the reference evaluator.
- **Tactical Testing:** Fixtures for mate-in-one and mate-in-two positions.
- **Reliability Testing:** Automated time-management and fallback-guard tests.
- **Match Testing:** A subprocess harness for playing games against baseline agents.

These results establish correctness and performance for the tested cases, not a general playing-strength rating. Further match testing is needed to establish the competitive effect of individual search and evaluation changes.

## Engineering Constraints

The agent is designed for a restricted competition runtime:

- **Runtime:** Python 3.12.
- **Compute:** One CPU core and 2 GB RAM.
- **Connectivity:** No network access or GPU.
- **Time Control:** 120 seconds per side with a 0.5-second increment.
- **Initialization:** A separate initialization budget.
- **Submission:** A ZIP archive with `agent.py` at the root and a 50 MB uncompressed size limit.

A custom build script validates imports, module naming, archive contents, native binary restrictions, and submission size. The development harness and test suite remain separate from the files intended for submission.

## Development Approach

Correctness comes before optimization. The reference engine provides a baseline for checking move generation and search behavior, while the bitboard implementation is developed and verified independently.

Performance changes are evaluated through benchmarks and, where appropriate, self-play and sequential probability ratio testing (SPRT).

Known limitations are tracked explicitly, including:

- Weaknesses in converting some winning endgames.
- Incomplete feature parity between the two implementations.
- Compilation costs associated with larger Numba kernels.

## Tech Stack

| Category | Technologies |
|---|---|
| Language | Python 3.12 |
| Board Representation | `python-chess`, custom bitboards |
| Performance | Numba |
| Search | Negamax, alpha-beta pruning, PVS, quiescence search, transposition tables |
| Testing | pytest, perft, tactical positions, benchmark comparisons, self-play |
| Tooling | Custom ZIP builder, smoke tests, match harness |
```
