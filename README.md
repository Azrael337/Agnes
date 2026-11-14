<div align="center">
  <img src="logo.png" alt="Agnes Chess Engine Logo" width="280" />
  <h1>Agnes</h1>
    <b> A strong experimental chess engine using nnue trained on CPU </b> <br> <br>
  <p>
    <img src="https://img.shields.io/badge/Language-C%2B%2B17-00599C?style=for-the-badge&logo=cplusplus&logoColor=white&labelColor=1a1a1a" alt="C++17" />
    <img src="https://img.shields.io/badge/Eval-NNUE-6f42c1?style=for-the-badge&labelColor=1a1a1a" alt="NNUE" />
    <a href="LICENSE">
      <img src="https://img.shields.io/badge/License-GPLv3-blue?style=for-the-badge&labelColor=1a1a1a" alt="License" />
    </a>
    <br>
    <a href="https://youtu.be/ADKpTHKYVe0?si=BUfWgh6Lvx3_kPFp">
      <img src="https://img.shields.io/badge/Defeat_Me-🤍-red?style=for-the-badge&labelColor=1a1a1a" alt="Defeat Me" />
    </a>
  </p>
</div>

---

## Overview

**Agnes** is a UCI chess engine written in modern C++17, pairing a fail-soft Alpha-Beta search with the **Acherontia** NNUE evaluation network.

Development is closed. This repository hosts [releases](https://github.com/Azrael337/Agnes/releases) only — every release ships ready-to-run binaries together with the complete source code of that version.

## Features

### Evaluation

A compact HalfKAv2_hm^ NNUE network — **Acherontia**, shipped as `acherontia.nnue` — with incremental accumulator updates, so evaluation stays cheap as moves are made and unmade.

### Search

- Fail-soft Alpha-Beta Negamax with Principal Variation Search (PVS)
- Iterative Deepening with Aspiration Windows
- Transposition Table with hash-move ordering
- Null Move Pruning (NMP)
- Late Move Reductions (LMR)
- Razoring and Reverse Futility Pruning
- Futility Pruning for quiet and capture moves
- Check and Singular Extensions
- Killer moves, Butterfly History and MVV-LVA capture ordering
- Quiescence Search with Static Exchange Evaluation (SEE)
- Multi-threaded search via Lazy SMP

## UCI Options

| Option | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Hash` | Spin | `64` | Transposition table size in MB |
| `Threads` | Spin | `1` | Number of search threads (Lazy SMP) |
| `EvalFile` | String | `88mm.nnue` | Path to the NNUE network weights file |

## Releases

Grab the latest version from the [releases page](https://github.com/Azrael337/Agnes/releases). Each release contains:

- Prebuilt binaries
- The full source code of that release
- The network file (`acherontia.nnue`)

To build from a released source, unzip it and run:

```bash
make
```

Place `88mm.nnue` next to the binary (or point `EvalFile` at it) and you're ready to go.

## Acknowledgements

- **[Stockfish](https://github.com/official-stockfish/Stockfish)** — for NNUE and the publicly documented search ideas Agnes builds upon.
- **[cutechess-cli](https://github.com/cutechess/cutechess)** and **[fastchess](https://github.com/fastchess-org/fastchess)** — for the tooling that tests engines like this one.
