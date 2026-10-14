# Design checks and physical validation

`erc.json` and `drc.json` were regenerated from the copied GitHub project using KiCad 10.0.5 during repository preparation. Their embedded timestamps record the actual tool run; no physical measurements are implied.

| Check | Result |
|---|---|
| ERC errors / warnings | 0 / 0 |
| DRC errors / warnings | 0 / 0 |
| Unconnected items | 0 |
| Schematic parity issues | 0 |

The reports list ignored check categories inherited from the project. No violation-specific exclusions were added during repository preparation. A clean CAD report does not prove temperature rise, compensation stability, EMI, fault containment or source switchover performance.

## Repeat the CAD checks

Run these commands from the repository root, with `kicad-cli` on PATH:

```sh
kicad-cli sch erc hardware/RAPS-5_V1.kicad_sch --format json --severity-error --severity-warning --exit-code-violations -o validation/erc.json
kicad-cli pcb drc hardware/RAPS-5_V1.kicad_pcb --format json --schematic-parity --severity-error --severity-warning --exit-code-violations -o validation/drc.json
```

## Hardware validation

Follow [BENCH_VALIDATION.md](BENCH_VALIDATION.md). Record results in [bench_results.csv](bench_results.csv), including the assembly revision, substitutions, ambient conditions and instrument details. This worksheet is deliberately unfilled. Validation of the 3 A target remains pending.
