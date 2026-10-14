# Command Line Interface — CHISEL

**Upstream:** https://github.com/chipsalliance/chisel

## Anticloud CLI

```bash
# Install
pip install anticloud-chisel

# Run offline with PAX inference
anticloud-chisel --offline --pax-local

# Run with AIOSS logging
anticloud-chisel --aioss-log ./ledger.jsonl

# Single binary (after build)
./chisel --config config.yaml
```

## Options

| Flag | Description |
| --- | --- |
| `--offline` | Disable all network calls |
| `--pax-local` | Use local PAX inference at 127.0.0.1:11434 |
| `--aioss-log PATH` | Write AIOSS audit chain to PATH |
| `--encrypt` | Enable AES-256 at rest for output files |
| `--gpu` | Force GPU inference |
| `--cpu` | Force CPU inference |
| `--config PATH` | Load configuration from YAML file |
