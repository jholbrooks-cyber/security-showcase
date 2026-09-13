# Security Showcase Pipeline

End-to-end security assessment pipeline for the National Bank of Greece intentionally vulnerable Spring Boot application. Demonstrates the full `homelab/components` library across five stages.

## Pipeline Stages

```
build  →  threat-model  →  analyze  →  fuzz  →  report
```

| Stage | Jobs | What it does |
|---|---|---|
| **build** | `build-banking-app` | Clones banking-app, compiles JAR with Maven |
| **threat-model** | STRIDE, DREAD, Attack Tree, DFD | Static threat analysis — no binary needed |
| **analyze** | 8 RE jobs | Strings, crypto constants, entropy, imports, symbols, packer detection, shellcode patterns, network IOCs |
| **fuzz** | 7 fuzz jobs + summary | Starts live app, fuzzes HTTP endpoints, inputs, SQLi, XSS, auth, REST API, protocol |
| **report** | `master-report` | Aggregates every findings JSON into one DefectDojo-importable output |

## Components Used

### Threat Modeling (`homelab/components/threat-modeling`)
- `stride.yml` — STRIDE threat enumeration per component
- `dread.yml` — DREAD risk scoring for 18 built-in threats
- `attack-tree.yml` — AND/OR attack trees with MITRE ATT&CK IDs
- `dfd-threats.yml` — Data flow diagram boundary threat analysis

### Reverse Engineering (`homelab/components/reverse-engineering`)
- `re-strings.yml` — Hardcoded secrets, URLs, credentials, base64 blobs
- `re-crypto.yml` — Crypto constant fingerprinting (AES, SHA, TEA, RC4...)
- `re-entropy.yml` — Shannon entropy per section (packed/encrypted detection)
- `re-imports.yml` — Dangerous import analysis (PE/ELF)
- `re-symbols.yml` — Symbol tables, PDB paths, debug info, suspicious names
- `re-packer.yml` — UPX, VMProtect, Themida, binwalk signatures
- `re-shellcode.yml` — GetPC, NOP sleds, PEB walk, Meterpreter stubs
- `re-network.yml` — IPv4/IPv6, URLs, domains, named pipes, C2 patterns

### Fuzzing (`homelab/components/fuzzing`)
- `fuzz-http.yml` — Directory/endpoint wordlist fuzzing
- `fuzz-input.yml` — Boundary, format string, path traversal, SSTI, command injection
- `fuzz-sqli.yml` — Error-based, boolean blind, time-based SQLi
- `fuzz-xss.yml` — Reflected XSS with 30+ payloads and bypass techniques
- `fuzz-auth.yml` — Default creds, JWT alg:none, cookie flags, forced browsing
- `fuzz-rest.yml` — BOLA/IDOR, mass assignment, method abuse, OpenAPI discovery
- `fuzz-protocol.yml` — Banner grab, Redis/Elasticsearch unauth, TCP fuzz

## Output

All findings are in **DefectDojo Generic Findings JSON** format.

- Per-job: `*-findings.json` + `*-report.md` (uploaded to Nexus)
- Final: `master-findings.json` + `master-report.md` (90-day artifact retention)

## Target Application

The [banking-app](https://github.com/jholbrooks-cyber/banking-app) is a deliberately vulnerable Spring Boot app modelling the National Bank of Greece. It contains 20+ intentional vulnerabilities including SQLi, XSS, XXE, SSRF, IDOR, ITSM, insecure JWT, and exposed actuator endpoints.
