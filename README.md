# PoC: `ModuleInfo::lines_data` range panic on malformed PDB

Reproducer for the issue filed against getsentry/pdb ("lines_data panics with
range end index out of range on malformed module info stream").

- `poc.bin` — 1,019,904 bytes, MSF 7.00 container, sha256 below. Deterministic:
  panics at `src/modi/mod.rs` in `lines_data` on every run
  (`range end index 1179403879 out of range for slice of length 984`).
- Found by coverage-guided fuzzing of a downstream consumer
  (symbolic-symcache -> symbolic-debuginfo -> pdb-addr2line -> pdb2 0.10.2,
  which carries the same `lines_data` code as this repository's master).
- Trigger: the DBI module info record declares `symbols_size`/`lines_size`
  values far larger than the actual module info stream; `ModuleInfo::parse`
  stores them unvalidated and `lines_data` slices `stream[start..start+size]`.

```
sha256: 1989a50a05fbbd1dce788642ba6beeae9d25af83e654aa8f60e9932e9f4794e9
```
