<div align="center">
  <img src="logo.png" alt="Agnes Chess Engine Logo" width="280" />

  <h1>Agnes</h1>

  <p>
    <strong></strong>
  </p>

  <p>
    <a href="https://github.com/Azrael337/Agnes/releases">
      <img src="https://img.shields.io/github/v/release/Azrael337/Agnes?style=for-the-badge&color=8b0000&labelColor=1a1a1a" alt="Release" />
    </a>
    <img src="https://img.shields.io/badge/Language-C%2B%2B17-00599C?style=for-the-badge&logo=cplusplus&logoColor=white&labelColor=1a1a1a" alt="C++17" />
    <img src="https://img.shields.io/badge/Protocol-UCI-2ea44f?style=for-the-badge&labelColor=1a1a1a" alt="UCI" />
    <img src="https://img.shields.io/badge/Eval-NNUE%20(Acherontia)-6f42c1?style=for-the-badge&labelColor=1a1a1a" alt="NNUE" />
    <a href="LICENSE">
      <img src="https://img.shields.io/badge/License-GPLv3-blue?style=for-the-badge&labelColor=1a1a1a" alt="License" />
    </a>
  </p>
</div>

---

## Overview

**Agnes** is a UCI-compliant chess engine written from scratch in modern C++17, combining fail-soft Alpha-Beta Negamax search with an efficient HalfKAv2_hm^ NNUE evaluation network (`Acherontia`).

<br />

---

## Features

* **Evaluation:** HalfKAv2_hm^ NNUE (`Acherontia` / `88mm.nnue`) with incremental accumulator updates
* **Search:** Fail-soft Alpha-Beta Negamax with Principal Variation Search (PVS), Iterative Deepening, and Aspiration Windows
* **Selectivity:** Null Move Pruning (NMP), Late Move Reductions (LMR), Razoring, Futility Pruning, and Check Extensions
* **Move Ordering:** Transposition Table move, MVV-LVA, Killer Heuristic, and Butterfly History Heuristic
* **Parallelism:** Multi-threaded search via Lazy SMP

---

## UCI Options

| Option | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Hash` | Spin | `64` | Transposition table size in MB |
| `Threads` | Spin | `1` | Number of search threads (Lazy SMP) |
| `EvalFile` | String | `88mm.nnue` | Path to the NNUE network weights file |

---

## Building from Source

```bash
git clone [https://github.com/Azrael337/Agnes.git](https://github.com/Azrael337/Agnes.git)
cd Agnes
make
