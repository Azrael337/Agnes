<div align="center">
  <img src="logo.png" alt="Agnes chess engine" width="300">

  ![License](https://img.shields.io/badge/License-GPL_3.0-green?style=for-the-badge)
  ![C++](https://img.shields.io/badge/C++-17-purple?style=for-the-badge)
  ![Release](https://img.shields.io/badge/Release-Agnes_1.0-yellow?style=for-the-badge)
 <br>
  ![Random](https://img.shields.io/badge/Defeat_ME-🤍-red?style=for-the-badge)

# Agnes chess engine
**A simple strong chess engine in C++ using a NNUE trained on cpu and an efficient search**
<br> **Agnes — Holy in Name. Merciless on the Board** <br> _Agnes - meaning "Pure" and "Holy"_

</div>

---

## Table of Contents

- [Evaluation](#evaluation)
- [Search](#search)
- [Quiescence](#quiescence)
- [Move Ordering](#move-ordering)
- [Transposition table](#transposition-table)
- [UCI Options](#uci-options)
- [UCI Commands](#uci-commands)
- [Building](#building)
- [Getting Started](#getting-started)
- [Play Strength](#play-strength)
- [Performance](#performance)
- [License](#license)

---

## Evaluation
**The network was trained using the nnue-pytorch training pipeline, with the training process running entirely on a Ryzen 7 5825U CPU (don't try this at home!)**
<br>Training such a network on a CPU was a considerably lengthy process, but it allowed Agnes to have its own trained evaluation network rather than simply relying on an externally supplied network.

**Network Architecture:**
| Component                | Configuration   |
| ------------------------ | --------------- |
|`Feature Set`             | HalfKAv2_hm^    |
|`Feature Transformer (L1)`|       1536      |
|`Hidden Layer (L2)`       |        16       |
|`Hidden Layer (L3)`       |        32       |
|`Output Layer`            |        1        |
|`PSQT Buckets`            |        8        |
|`Layer Stacks`            |        8        |

**Some Features:**
- Agnes uses HalfKAv2_hm^, a king-relative feature representation that categorizes piece-square relationships according to the position of the king.
- One of the important components of Agnes's evaluator is its incremental accumulator system.
- King moves require refreshing the corresponding perspective because the king-relative feature representation changes.
- In addition to the neural network's positional output, Agnes incorporates a separate Piece-Square Table (PSQT) contribution. The PSQT component uses eight material-dependent buckets, allowing positional contributions to vary according to the stage of the game.

**Quantizaton:**

For efficient inference, Agnes uses quantized network parameters:
- INT8 weights for fully connected layers.
- INT32 biases and accumulators where required.
- Clipped activation functions and squared activations.
- Bucket-specific network stacks.

**The result is a custom NNUE evaluation system designed to provide Agnes with a strong positional understanding without sacrificing search efficiency**

## Search
- Fail-soft negamax with alpha-beta
- Aspiration windows (from depth 5, dynamic widening)
- Iterative deepening with IIR (internal iterative reduction)
- Razoring (quadratic margin, depth-gated)
- Reverse futility pruning (RFP) with improving & complexity terms
- Null-move pruning (NMP) with verification search
- ProbCut (with improving margin + depth reduction)
- Late move pruning (LMP)
- Futility pruning (quiet + capture, history-scaled)
- SEE-based move pruning (quiet + capture, history-aware)
- Singular extensions (with low-depth & deep-pct margins)
- Double extensions (budget-capped along a line)
- Check extensions (SEE-guarded, with check_bonus)
- Late move reductions (LMR) — SF17-style formula
- Bad-noisy futility at shallow depth
- History-driven extension/reduction adjustments

## Quiescence
- Stand-pat with delta cap
- Captures + queen promotions only (non-check)
- Full move generation when in check
- SEE-split of noisy moves (good vs bad)

## Move Ordering
- TT move first
- Noisy moves (captures, EP, queen promos) with MVV-LVA + SEE
- Killer moves (2 per ply)
- Counter-moves
- Quiet moves scored by threat-aware main history + piece-to history
- Continuation history (plies 1, 2, 4, 6, weighted)
- Pawn-structure-aware history
- Capture history + capture continuation history
- Threat-aware ordering: escape bonus / enter penalty by attacker class (pawn > minor > rook > queen hierarchy)
- Check bonus for SEE-guarded quiet checks
- Staged MovePicker with LAZY quiet generation (captures up front, quiets generated only when reached)

## Transposition table
- 4 entries × 16 B per 64 B cluster (one cache line)
- mulhi64 indexing (any cluster count)
- 5-bit generation, PV flag, int16 scores
- MADV_HUGEPAGE on Linux, sry windows
- hashfull per mil reporting


## UCI Options

| Option      | Type   | Default | Range     | Description                                        |
| ----------- | ------ | ------- | --------- | -------------------------------------------------- |
| `EvalFile`  | string | archerontia.nnue       | —         | Path to a custom NNUE evaluation file              |
| `Hash`      | spin   | 256     | 1–65536   | Transposition table size in MB                     |
| `Threads`   | spin   | 1       | 1–256     | Number of search threads (Lazy SMP)                |
| `NNUEScale` | spin   | 40      | 1–400     | NNUE evaluation scaling factor                     |

## UCI Commands
Agnes is compatible with any UCI-capable chess GUI such as Cute Chess, Banksia, ChessBase, and online platforms like Lichess and Chess.com (via bots).

## Building

### How to compile

```
cd /wherever/the/makefile/is

make "bare avx2"

# Alternatively, choose a build target:
make avx2
make avx512
make bmi2
make vnni
```

- avx2      => any CPU since ~2013
- avx512    => Ice Lake+/Zen 4+
- bmi2      => 	Intel Haswell+/AMD Zen 1–3
- vnni      => Alder Lake+/Zen 5
- native    => only for the machine you build on, don't distribute

## Getting Started

Once built, you can test the engine directly from a terminal:

```bash
./agnes
```

```text
uci
isready
position startpos moves e2e4 e7e5
go depth 18
```

For a more comfortable experience, connect Agnes to a UCI GUI (e.g. Cute Chess or Banksia) or use it for engine-vs-engine testing with tools such as `cutechess-cli`.

## Play Strength

Estimated strength is currently around **2900–3100 Elo** as per release 1.0.

Release 2.0* can be 3400 elo


## Performance

* Efficient incremental NNUE updates for minimal eval overhead
* Dynamic time management adapts to search depth and position complexity
* Thread-pool architecture enables strong scalability up to 256 cores

## License

See [LICENSE](LICENSE).

## Acknowledgement
* Agnes uses https://github.com/syzygy1/tb for syzygy tablebase
* Agnes has been inspired from many strong chess engines like 
- [Stockfish](https://github.com/official-stockfish/Stockfish)
- [Reckless](https://github.com/codedeliveryservice/Reckless)
- [Koivisto](https://github.com/Luecx/Koivisto)
- [Patricia](https://github.com/Adam-Kulju/Patricia)
- [Caissa](https://github.com/Witek902/Caissa)
