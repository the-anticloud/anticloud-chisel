# CMMC 2.0 Level 2 (NIST SP 800-171)

**Project:** CHISEL · **Category:** SEMICONDUCTOR · **Vendor:** Anticloud FZ LLE

## Model component (PAX L5 Narrow L2 General 27B — quoted with provenance)

| Module | Key Metric | Value |
|--------|-----------|-------|
| CRDT | lww_set_us | 0.51 |
| CRDT | merge_10k_ms | 0.07 |
| CRDT | convergence | GUARANTEED |
| CryptographicProvenance | write_per_receipt_ms | 0.059 |
| CryptographicProvenance | verify_1k_chain_ms | 2.53 |
| ZeroTrustLocalMesh | ca_gen_ms | 367.5 |
| ZeroTrustLocalMesh | cert_issue_ms | 333.8 |
| ZeroTrustLocalMesh | tls_version | 1.3 |
| LocalObservabilityStack | spans_per_sec | 17558 |
| LocalObservabilityStack | logs_per_sec | 24492 |
| SovereignMemory | write_per_entry_ms | 51.94 |
| SovereignMemory | encryption | AES-256-GCM |
| AIOSS_AuditChain | entries_per_sec | 232665 |
| AIOSS_AuditChain | hash_algo | SHA3-256 |

*Run ID loiskleinner/pax-benchmark-runner v3, 2026-10-02, Kaggle T4 x2 (2x 16GB VRAM), Ubuntu 22.04, Python 3.12. These are PAX L5 Narrow L2 General 27B model-level results — shared model component, not this project's verdict.*

## Project-level basis (this project's own measured checks)

| Basis check | Status | Observed |
|---|---|---|
| 10_nist_sp_800_53 | PASS | 12/12 controls evidenced (100.0%) |
| 05_git_health | PASS | head=203308428af7d3e2854eef867f53091b86a42c56 commits=1 clea |

## Verdict

CONTROLS EVIDENCED — all basis checks PASS

Verdict derives only from the basis checks above (actual runs, register recorded). Nothing here is copied from another project's assessment.

## Cryptographic integrity

- BENCH.json sha3-256: `b06621fc32d5d88692a2fc34741a4e3a6aa82d97ca575f19794af98d77c9dbce`
- Result register sha3-256: `74bb83302c231b8695f89f0c6eaf970159315cc5b2fc75509448804f55f4393e`
- Generated: 2026-10-06T18:12:24Z

*Anticloud FZ LLE · AIOSS Ledger Genesis: 8b4a8a4f6312dfbe885de8280716985637c163fd2a4b5590341d56db1cc4e560*

> Self-assessment only. No external audit has been performed. A SOC 2 / NIST independent audit is committed post-funding.
