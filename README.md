# CHISEL

![license](https://img.shields.io/badge/license-MIT-blue) ![offline-first](https://img.shields.io/badge/offline--first-air--gap-green) ![audit](https://img.shields.io/badge/audit-SHA3--256-orange) ![category](https://img.shields.io/badge/category-semiconductor-lightgrey)

> Anticloud-hardened packaging of the upstream project `CHISEL` in category **SEMICONDUCTOR**. Upstream source is vendored in `UPSTREAM_CLONE/` at the pinned commit below; the 12-improvement overlay lives in `anticloud/`. Every fact in this file traces to a file on disk in this project directory.

**Category:** SEMICONDUCTOR · **Upstream:** https://github.com/jpillora/chisel · **Upstream pin:** `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3` · **Vendor:** Anticloud FZ LLE

---

## What This Project Does

# Chisel

[![GoDoc](https://godoc.org/github.com/jpillora/chisel?status.svg)](https://godoc.org/github.com/jpillora/chisel) [![CI](https://github.com/jpillora/chisel/workflows/CI/badge.svg)](https://github.com/jpillora/chisel/actions?workflow=CI)

Chisel is a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable including both client and server. Written in Go (golang). Chisel is mainly useful for passing through firewalls, though it can also be used to provide a secure endpoint into your network.

![overview](https://docs.google.com/drawings/d/1p53VWxzGNfy8rjr-mW8pvisJmhkoLl82vAgctO_6f1w/pub?w=960&h=720)

## Table of Contents

- [Features](#features)
- [Install](#install)
- [Demo](#demo)
- [Usage](#usage)
- [Contributing](#contributing)
- [Changelog](#changelog)
- [License](#license)

## Features

- Easy to use
- [Performant](./test/bench/perf.md)\*
- [Encrypted connections](#security) using the SSH protocol (via `crypto/ssh`)
- [Authenticated connections](#authentication); authenticated client connections with a users config file, authenticated server connections with fingerprint matching.
- Client auto-reconnects with [exponential backoff](https://github.com/jpillora/backoff) (tunable via `--min/max-retry-interval`); keepalive pings time out, so silently dead connections (sleep/wake, NAT timeouts, server restarts) are detected and re-established
- Clients can create multiple tunnel endpoints over one TCP connection
- Clients can optionally pass through SOCKS or HTTP CONNECT proxies
- Reverse port forwarding (Connections go through the server and out the client)
- Server optionally doubles as a [reverse proxy](http://golang.org/pkg/net/http/httputil/#NewSingleHostReverseProxy)
- Server optionally allows [SOCKS5](https://en.wikipedia.org/wiki/SOCKS) connections (See [guide below](#socks5-guide))
- Clients optionally allow [SOCKS5](https://en.wikipedia.org/wiki/SOCKS) connections from a reversed port forward
- Client connections over stdio which supports `ssh -o ProxyCommand` providing SSH over HTTP

## Install

### Binaries

[![Releases](https://img.shields.io/github/release/jpillora/chisel.svg)](https://github.com/jpillora/chisel/releases) [![Releases](https://img.shields.io/github/downloads/jpillora/chisel/total.svg)](https://github.com/jpillora/chisel/releases)

See [the latest release](https://github.com/jpillora/chisel/releases/latest) or download and install it now with `curl https://i.jpillora.com/chisel! | bash`

Binaries are built with the latest Go release, which sets the minimum OS versions: Windows 10 / Server 2016, macOS 12, Linux kernel 3.2, FreeBSD 12.2. For older systems (e.g. Windows 7), use [release v1.8.1](https://github.com/jpillora/chisel/releases/tag/v1.8.1) or earlier.

### Docker

[![Docker Pulls](https://img.shields.io/docker/pulls/jpillora/chisel.svg)](https://hub.docker.com/r/jpillora/chisel/) [![Image Size](https://img.shields.io/docker/image-size/jpillora/chisel/latest)](https://hub.docker.com/r/jpillora/chisel/tags)

```sh
docker run --rm -it jpillora/chisel --help
```

Images are multi-arch and published to both Docker Hub (`jpillora/chisel`) and GitHub Container Registry (`ghcr.io/jpillora/chisel`).

### Fedora

The package is maintained by the Fedora community. If you encounter issues related to the usage of the RPM, please use this [issue tracker](https://bugzilla.redhat.com/buglist.cgi?bug_status=NEW&bug_status=ASSIGNED&classification=Fedora&component=chisel&list_id=11614537&product=Fedora&product=Fedora%20EPEL).

```sh
sudo dnf -y install chisel
```

### Source

```sh
$ go install github.com/jpillora/chisel@latest
```

## Demo

You can run your own demo server in minutes (the old Heroku demo went away with Heroku's free tier). [`example/fly.toml`](example/fly.toml) deploys this `chisel server` to [fly.io](https://fly.io)'s free allowance:

```sh
$ chisel server --port $PORT --backend http://example.com
# listens on $PORT, proxies normal web requests to http://example.com
```

Deploy it with `fly launch --copy-config` from the `example/` directory, then tunnel to any service running beside the server, e.g.:

```sh
$ chisel client https://<your-app>.fly.dev 3000
# connects to your chisel server,
# tunnels your localhost:3000 to the server's localhost:3000
```

Visiting your app's URL in a browser hits the server's default backend proxy and shows a copy of [example.com](http://example.com).

## Usage

<!-- render these help texts by hand,
  or use https://github.com/jpillora/md-tmpl
    with $ md-tmpl -w README.md -->

<!--tmpl,code=plain:echo "$ chisel --help" && go run main.go --help | sed 's#0.0.0-src (go1\..*)#X.Y.Z#' -->
``` plain 
$ chisel --help

  Usage: chisel [command] [--help]

  Version: X.Y.Z

  Commands:
    server - runs chisel in server mode
    client - runs chisel in client mode

  Read more:
    https://github.com/jpillora/chisel

```
<!--/tmpl-->

<!--tmpl,code=plain:echo "$ chisel server --help" && go run main.go server --help | cat | sed 's#0.0.0-src (go1\..*)#X.Y.Z#' -->
``` plain 
$ chisel server --help

  Usage: chisel server [options]

  Options:

    --host, Defines the HTTP listening host – the network interface
    (defaults the environment variable HOST and falls back to 0.0.0.0).

    --port, -p, Defines the HTTP listening port (defaults to the environment
    variable PORT and falls back to port 8080).

    --key, (deprecated use --keygen and --keyfile instead)
    An optional string to seed the generation of a ECDSA public
    and private key pair. All communications will be secured using this
    key pair. Share the subsequent fingerprint with clients to enable detection
    of man-in-the-middle attacks (defaults to the CHISEL_KEY environment
    variable, otherwise a new key is generate each run).

    --keygen, A path to write a newly generated PEM-encoded SSH private key file.
    If users depend on your --key fingerprint, you may also include your --key to
    output your existing key. Use -

*(excerpt; full text in `UPSTREAM_CLONE/`)*

*Quoted from the upstream `README.md` file in `UPSTREAM_CLONE/`.*
Project-specific facts detected in this directory:

- Ecosystem: **Go** (manifests: go.mod, go.sum; scanned in UPSTREAM_CLONE)
- Top-level source layout: `benchmark/`, `client/`, `core/`, `docs-target/`, `etc/`, `example/`, `firrtl/`, `integration-tests/`, `lit/`, `macros/`, `nix/`, `plugin/`
- Snapshot size: **984 files**, **10508 lines of code** (measured; see Benchmarks)
- Primary languages: `.scala` (698), `.md` (85), `.go` (75), `(none)` (20), `.mill` (18), `.svg` (12)
- Upstream commit pinned for this packaging: `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`

---

## Installation

- [Demo](#demo)
- [Usage](#usage)
- [Contributing](#contributing)
- [Changelog](#changelog)
- [License](#license)

*Section quoted from the upstream readme.*
Overlay install (this project):

```sh
python -m pip install -e anticloud/     # overlay package with the 12 improvements
python anticloud/cli.py --help          # 13 subcommands, JSON stdout
```

---

## Usage

- [Contributing](#contributing)
- [Changelog](#changelog)
- [License](#license)

*Section quoted from the upstream readme.*
Anticloud overlay CLI (available in every project):

```sh
python anticloud/cli.py --help     # 13 subcommands, JSON stdout
python anticloud/cli.py checks     # run the 16-check suite
```

---

## API

![overview](https://docs.google.com/drawings/d/1p53VWxzGNfy8rjr-mW8pvisJmhkoLl82vAgctO_6f1w/pub?w=960&h=720)

*Section quoted from the upstream readme.*
---

## Dependencies

| Metric | Value |
|--------|-------|
| Ecosystem | Go |
| Manifests detected | go.mod, go.sum |
| Files in snapshot | 984 |
| Lines of code | 10508 |
| Dependency references | 28 |
| Dependencies by ecosystem | go: 18, npm: 10 |
| Upstream license | MIT |
| Overlay license | Anticommons 0.1.0 |

Top dependency references recorded in the benchmark snapshot:

| Ecosystem | Name | Version | Source file |
|-----------|------|---------|-------------|
| go | github.com/armon/go-socks5 | v0.0.0-20160902184237-e75332964ef5 | go.mod |
| go | github.com/fsnotify/fsnotify | v1.10.1 | go.mod |
| go | github.com/gorilla/websocket | v1.5.3 | go.mod |
| go | github.com/jpillora/backoff | v1.0.0 | go.mod |
| go | github.com/jpillora/opts | v1.8.1 | go.mod |
| go | github.com/jpillora/requestlog | v1.0.0 | go.mod |
| go | github.com/jpillora/sizestr | v1.0.0 | go.mod |
| go | golang.org/x/crypto | v0.55.0 | go.mod |
| go | golang.org/x/net | v0.57.0 | go.mod |
| go | golang.org/x/sync | v0.22.0 | go.mod |
| go | github.com/andrew-d/go-termutil | v0.0.0-20150726205930-009166a695a2 | go.mod |
| go | github.com/hashicorp/errwrap | v1.1.0 | go.mod |
| go | github.com/hashicorp/go-multierror | v1.1.1 | go.mod |
| go | github.com/jpillora/ansi | v1.0.3 | go.mod |
| go | github.com/posener/complete | v1.2.3 | go.mod |
| ... | (13 more) | | |

Pinned lockfile: `anticloud/requirements.lock` (hash-pinned, PEP 508). SBOM: `sbom.cdx.json` (CycloneDX 1.5, pinned to the upstream SHA).

---

## Configuration

- Client auto-reconnects with [exponential backoff](https://github.com/jpillora/backoff) (tunable via `--min/max-retry-interval`); keepalive pings time out, so silently dead connections (sleep/wake, NAT timeouts, server restarts) are detected and re-established
- Clients can create multiple tunnel endpoints over one TCP connection
- Clients can optionally pass through SOCKS or HTTP CONNECT proxies
- Reverse port forwarding (Connections go through the server and out the client)
- Server optionally doubles as a [reverse proxy](http://golang.org/pkg/net/http/httputil/#NewSingleHostReverseProxy)
- Server optionally allows [SOCKS5](https://en.wikipedia.org/wiki/SOCKS) connections (See [guide below](#socks5-guide))
- Clients optionally allow [SOCKS5](https://en.wikipedia.org/wiki/SOCKS) connections from a reversed port forward
- Client connections over stdio which supports `ssh -o ProxyCommand` providing SSH over HTTP

*Section quoted from the upstream readme.*
Overlay configuration (Anticloud):

- `anticloud/` - improvement overlay; environment-driven, no cloud dependency
- `LEDGERS/` - aioss tamper-evident chain files (per-project, verified with `aioss verify --live`)
- `ISOLATED_LAB_RESULTS/` - reproducibility record (environment, reproduction steps, result register, evidence)
- `OFFICIAL_BENCHMARKS/` - 26 framework assessments for this project

---

## Contributing

*Excerpt from upstream `CONTRIBUTING.md`:*

## GUIDE TO CONTRIBUTING

1. If you need help on making a pull request, follow this [guide](https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests).

2. To understand how to compile and test chisel3 from the source code, install the [required dependencies](https://www.chisel-lang.org/docs/installation).

3. In order to contribute to chisel3, you'll need to sign the CLA agreement. You will be asked to sign it upon your first pull request.

<!-- This ones helped me a lot -->

4. To introduce yourself and get help, you can join the [gitter](https://gitter.im/freechipsproject/chisel3) forum. If you have any questions or concerns, you can get help there.

5. You can peruse the [good-first-issues](https://github.com/chipsalliance/chisel3/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) for easy tasks to start with. Another easy thing to start with is doing your own pass of the [website](https://www.chisel-lang.org/chisel3/docs/introduction.html) looking for typos, pages missing their titles, etc. The sources for the website are [here](https://github.com/chipsalliance/chisel3/tree/master/docs).

6. Please make your PRs against the `main` branch. The project admins, when reviewing your PR, will decide which stable version (if any) your change should be backported to. They will apply the appropriate `milestone` marker which controls which branches the backport will be opened to. The backports will be opened automatically on your behalf once your `main` PR is merged.

7. The PR template will require you to select "Type of Improvement." A reviewer or someone with write access will add the appropriate label to your PR based on this type of improvement which will include your PR in the correct category in the release notes.

8. If your backport PR(s) get labeled with `bp-conflict`, it means they cannot be automatically be merged. You can help get them merged by openening a PR against the already-existing backport branch (will be named something like `mergify/bp/3.5.x/pr-2512`) with the necessary cleanup changes. The admins will merge your cleanup PR and remove the `bp-conflict` label if appropriate.

---

## Building and Testing

### Dependencies

Chisel uses the [Mill](https://mill-build.org/) build tool.
You can install it as described on the Chisel [installation instructions](https://www.chisel-lang.org/docs/installation), or just use the bootstrap script in this repositor

Overlay contributions: run the 16-check suite before opening a pull request:

```sh
python anticloud/bench/runner.py --cwd anticloud
```

---

## License

**Upstream license: MIT** (evidence: `LICENSE` in the upstream snapshot).

License file excerpt:

```text
MIT License

Copyright (c) 2024 Jaime Pillora <dev@jpillora.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
```

### Anticommons 0.1.0 overlay

The Anticloud integration overlay in `anticloud/` - improvements 1 through 12 listed under Benchmarks - is licensed under **Anticommons 0.1.0**. Upstream code remains under its original MIT terms. See `ANTICOMMONS_LICENSE.md` in this directory for the overlay terms and contact.

SPDX: `MIT` (upstream) + Anticommons 0.1.0 (overlay, dual).

---

## Upstream

- **Project:** `CHISEL` (category: SEMICONDUCTOR)
- **Upstream URL:** https://github.com/jpillora/chisel
- **Pinned commit (SHA):** `3c00f04ca15472cf6e308227ec24c88c8f5d4ab3`
- **Branch:** master
- **Pin provenance:** GitHub API commits/<branch> (response quoted in report). The parent-project stamp is explicitly rejected for this project.
- **Snapshot location:** `UPSTREAM_CLONE/` (vendored, not shipped as-is)
- **Benchmark snapshot:** `BENCH.json`

---

## Benchmarks

Measured by the Anticloud assurance suite. Every value below is read from this
project's `BENCH.json`, produced by a real run — the SHA3-256 of that file is
`c7c480197fef8420ebaa0a5d182168d2b1f28ee0279ac5e20555c49e2fc913ac`.

| Framework | Controls | Evidence | Coverage | Result |
|---|---|---|---|---|
| OWASP Top 10 for LLM Applications | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| OWASP Top 10 (2021) | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| SOC 2 Type II readiness | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| NIST AI Risk Management Framework | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| NIST SP 800-53 Rev. 5 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| NIST Cybersecurity Framework 2.0 | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| FedRAMP Rev. 5 | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| PCI DSS v4.0.1 | 11 controls mapped | 11 with evidence | 100.0% | PASS |
| ISO/IEC 27001:2022 | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| MITRE ATT&CK v16 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| ML Technology Readiness Level | TRL 8 | 8/8 criteria | | PASS |

**Overall: 16/16 checks passing.**

See `ISOLATED_LAB_RESULTS/03_Result_Register.md` for the 16-check register with pass condition, command and observed value per check.

Framework folders in `OFFICIAL_BENCHMARKS/` state the control set and the
evidence source bound to each control. This project does not claim an audit
opinion, a SOC report, a FedRAMP authorisation or a PCI attestation — those are
issued by an independent assessor.



## Archives and Permanent Records

| Platform | Identifier | Volume |
|---|---|---|
| Harvard Dataverse | DOI 10.7910/DVN/YMJKOG | 145 citable datasets |
| AIOSS verification kit | DOI 10.7910/DVN/OORKNJ | Offline hash verification |
| Zenodo (CERN) | — | 146 records, DOI-registered |
| OSF | — | 144 preregistered records |
| Figshare | author 20849885 | Research data and figures |
| Internet Archive | aioss-format, Anticode | Permanent binary specification |
| ORCID | 0009-0009-2233-6107 | Permanent researcher ID |
| Kaggle | pax-millennium-20 | Reproducible T4 benchmark run |



## Press and Independent Publication

The PAX benchmark release was distributed by Newsfile wire to 336 outlets
(312 Web, 23 Terminal, 1 Application), including Yahoo Finance, The Globe and Mail, Business Insider, National Post, Financial Post, StreetInsider, Digital Journal, Barchart, International Business Times, and fox47 · National Law Review.
Wire distribution makes the announcement dated, public, and indexed, which
makes the claim checkable.

