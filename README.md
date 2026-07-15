
# Processor Performance Analysis Using SimpleScalar

A simulation-based project analyzing processor performance — cache configuration, branch prediction, branch target buffering, and pipeline width — using the SimpleScalar `sim-outorder` simulator, concluding with a constrained optimum-processor design exercise.

> **On the data in this repo:** each folder contains a representative subset of configuration files, output logs, and screenshots — not the complete set of every run performed, to keep the repo a manageable size. For the full set of runs and screenshots, refer to the appendix which is included in the documentation folder. For the key results and full write-up, see the project report.

---

## Part-I — Cache Configuration Analysis

Four memory-system configurations of increasing size — **Small, Medium, Large, Excessive** — were built using the `-cache:il1`, `-cache:dl1`, `-cache:dl2`, and `-cache:il2` options of `sim-outorder`, and evaluated on `go.ss` and `gcc.ss` for `sim_cycle` and `sim_CPI`.

**Folder contents:** sample configuration files and output-log screenshots for the Small/Medium/Large/Excessive runs (`gcc.ss`), plus the execution-time and CPI comparison charts.

| Configuration | sim_cycle (gcc.ss) | sim_CPI (gcc.ss) |
|---|---|---|
| Small | 1,035,253 | 1.0353 |
| Medium | 882,049 | 0.8820 |
| Large | 746,351 | 0.7464 |
| Excessive | 671,412 | 0.6714 |

Execution cycles and CPI both drop steadily as the memory system scales up.

## Part-II — Branch Predictor and BTB Analysis

**(a) Branch predictors:** Bimodal (table sizes 512/1024/2048), 2-level (history width 4/6/8/10 bits; 2nd-level entries 64/256/1024), and a Combination predictor, compared on `ijpeg.ss` (`large.ppm` input) via `bpred.dir_hits` / `bpred.misses`.

**(b) Branch Target Buffer (BTB):** compared by `addr_hits` — varying number of sets (16/32/64), associativity (1/2/4), and a fixed-total-size comparison (64×4, 128×2, 256×1). Increasing sets or associativity improves `addr_hits`, with diminishing returns at the higher end.

**Folder contents:** predictor and BTB configuration/output samples, plus comparison charts for bimodal size, 2-level width, combination predictor, and BTB sets/associativity/fixed-size.

## Part-III — Detailed Architectural Analysis

Single-benchmark (`ijpeg.ss`) deep dive:

- Branch predictor comparison (bimod, taken, not-taken, 2-level) — taken/not-taken show far higher miss rates than bimod/2-level.
- IFQ size vs. IPC — IPC rises sharply from IFQ 2→4, then flattens (~2.2–2.25 IPC from IFQ 4–16).
- Decode width and issue width vs. execution cycles — cycles fall steeply up to issue width 4, then flatten.
- In-order vs. out-of-order issue — out-of-order roughly halves total cycles vs. in-order.
- L1 D-cache / I-cache hit rate vs. `nsets`, block size, and associativity — all three improve hit rate, block size most clearly (D-cache: 98.81% → 99.58% → 99.79%; I-cache: 99.91% → 99.96% → 99.98%).

**Folder contents:** sample configuration/output files for the IFQ, decode-width, issue-width, and in-order/out-of-order sweeps, plus the D-cache and I-cache hit-rate charts (vs. nsets, block size, associativity).

## Part-IV — Optimum Processor Design

Constraints: bimod-only prediction, in-order issue, 512 bytes total memory, 7 total units split across IFQ / decode width / issue width.

**Folder contents:** the evaluated configuration comparisons and the final selected-configuration output.

| Parameter | Value |
|---|---|
| IFQ | 4 |
| Decode Width | 2 |
| Issue Width | 1 |
| **sim_cycle** | **1,316,840** |

Configuration C (above) gave the minimum cycle count among the configurations tried, by a narrow margin over the alternatives.

---

## Tools

- **Simulator:** SimpleScalar `sim-outorder`, run on Ubuntu via an Oracle VM
- **Data extraction:** `grep` on redirected simulator output (`sim_cycle`, `sim_CPI`, `bpred_*`, cache stats, etc.)
- **Analysis:** Excel for tabulation and graphing
