# Build and Test

**Project:** `CHISEL`
**Upstream:** https://github.com/chipsalliance/chisel
**License:** Apache 2.0

## Quick Start

```bash
git clone https://github.com/chipsalliance/chisel
cd chisel
pip install -r requirements-anticloud.txt
python anticloud_main.py --offline --pax-local
```

## Anticloud Improvements Applied

1. PAX L5 Narrow L2 General 27B local EDA co-pilot — air-gapped fab environment
2. AIOSS tamper-evident design revision chain (ITAR-aligned)
3. AES-256 encryption for all GDS, netlists, and process PDK data
4. Single-binary EDA tool supplement deployable on secure design workstations
5. Zero-cloud: all AI inference and simulation run on local EDA servers
6. GPU/CPU equalizer: layout verification on GPU, timing analysis on CPU
7. Open PDK interface: Sky130 and IHP SG13G2 integration out of the box
8. Offline DRC/LVS runner adding to or replacing cloud verification services

## Benchmark Targets

| Metric | Target |
| --- | --- |
| Latency | Primary inference task: <5s on CPU, <1s on GPU |
| Throughput | Batch processing: >100 items/hour on single CPU server |
| Memory | <8GB RAM for standard deployment |
| Accuracy | Task-specific accuracy within 5% of cloud-API baseline |

## Build Status

Not yet measured. Run verified build and record actual figures above.
