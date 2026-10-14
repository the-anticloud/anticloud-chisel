# Educators — CHISEL

**Project:** CHISEL  
**Category:** SEMICONDUCTOR  
**Upstream:** see BENCH.json  
**Pinned commit:** `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `41a5324414876d0664c268a5918301b159868439c0654ad27d932e64368ac58d`  
**Date:** October 2026

## Teaching with CHISEL

The project is usable as a worked example of offline-first packaging with a
cryptographic audit chain. It ships with the assurance suite, the evidence
register and the ledger, so students can verify claims rather than take them on
faith.

## Suggested exercises

1. Run `python tools/run_bench.py --out BENCH.json` and read the 16 results.
2. Recompute the SHA3-256 of a row's evidence file and compare to the register.
3. Walk the AIOSS chain from genesis to head `41a5324414876d0664c268a5918301b159868439c0654ad27d932e64368ac58d` and confirm every link.
4. Break one artifact and observe the chain fail to verify.

## Licence for teaching

Apache 2.0 terms apply to academic and teaching use. See `24_ANTICOMMONS_LICENSE`.
