# Students — CHISEL

**Project:** CHISEL  
**Category:** SEMICONDUCTOR  
**Upstream:** see BENCH.json  
**Pinned commit:** `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `41a5324414876d0664c268a5918301b159868439c0654ad27d932e64368ac58d`  
**Date:** October 2026

## What this project gives you

A complete worked example of offline-first packaging with a cryptographic audit
chain: source pinned at `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`, a 16-check assurance suite, an evidence register
with per-check hashes, and an AIOSS ledger chain ending at `41a5324414876d0664c268a5918301b159868439c0654ad27d932e64368ac58d`.

## Learn by verifying

```
python tools/run_bench.py --out BENCH.json
```

Then take any row from `ISOLATED_LAB_RESULTS/03_Result_Register.md`, recompute
the SHA3-256 of its evidence file, and confirm it matches. If it does not match,
the record has been altered — that is the whole point of the chain.

## Licence

Apache 2.0 terms apply to study and teaching use.
