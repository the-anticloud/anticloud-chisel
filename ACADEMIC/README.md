# Academic Use — CHISEL

**Project:** CHISEL  
**Category:** SEMICONDUCTOR  
**Upstream:** see BENCH.json  
**Pinned commit:** `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `41a5324414876d0664c268a5918301b159868439c0654ad27d932e64368ac58d`  
**Date:** October 2026

## Scope

CHISEL is available for academic research under the Apache 2.0 terms of the
Anticommons 0.1.0 dual licence. Citation details are in `23_HOW_TO_CITE`.

## What is available to researchers

- The full upstream source, pinned at `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`
- The 16-check assurance register with per-check evidence and hashes
- The AIOSS ledger attesting the project artifacts
- Benchmark output in `BENCH.json`

## Reproducing the result

```
python tools/run_bench.py --out BENCH.json
```

Then recompute any row's SHA3-256 from `ISOLATED_LAB_RESULTS/04_Evidence/`.

## Contact

lois@0-1.gg · 0-1.gg
