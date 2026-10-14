# Technical Architecture — CHISEL

**Upstream:** [https://github.com/chipsalliance/chisel](https://github.com/chipsalliance/chisel)
**License:** Apache 2.0
**Category:** SEMICONDUCTOR
**Anticloud Integration:** PAX L5 Narrow L2 General 27B + AIOSS + Offline-First

## Upstream Description

Scala-based chip design language

## Anticloud Architectural Changes

1. PAX L5 Narrow L2 General 27B local EDA co-pilot — air-gapped fab environment
2. AIOSS tamper-evident design revision chain (ITAR-aligned)
3. AES-256 encryption for all GDS, netlists, and process PDK data
4. Single-binary EDA tool supplement deployable on secure design workstations
5. Zero-cloud: all AI inference and simulation run on local EDA servers
6. GPU/CPU equalizer: layout verification on GPU, timing analysis on CPU
7. Open PDK interface: Sky130 and IHP SG13G2 integration out of the box
8. Offline DRC/LVS runner adding to or replacing cloud verification services

## Integration Points

- **PAX Inference Socket:** Local HTTP endpoint at `127.0.0.1:11434/v1/chat` — same OpenAI-compatible API, zero cloud
- **AIOSS Hook:** Every write operation calls `aioss_append(event, payload)` before commit
- **Encryption Layer:** All file I/O routed through `anticloud_crypto.encrypt_at_rest()`
- **Single Binary Build:** `pyinstaller anticloud_chisel.spec` or `go build -o chisel`

## Deployment Modes

| Mode | Hardware | Notes |
| --- | --- | --- |
| Edge CPU | Raspberry Pi 4 / Intel NUC | Full feature set, PAX on CPU |
| Desktop GPU | RTX 3060 / A10 | PAX GPU inference, <1s latency |
| Server | 2× A100 | Full batch throughput |
| Air-gapped | Any x86/ARM | Zero network dependency |