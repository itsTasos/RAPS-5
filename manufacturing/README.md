# Prototype manufacturing files

These exports describe the V1 experimental board. Physical validation is pending; consult the [bench plan](../validation/BENCH_VALIDATION.md) before operational use.

## PCB specification

| Item | Specification |
|---|---|
| Outline | 50 × 40 mm nominal; use Edge.Cuts geometry |
| Stack | Four layers, 1.6 mm FR-4 |
| Copper | F.Cu 2 oz / In1.Cu 1 oz / In2.Cu 1 oz / B.Cu 2 oz |
| Finish | ENIG |
| Assembly | Components on both sides; J1/J2/J3 through-hole |
| Mounting | Four 2.2 mm holes for M2 hardware |

`gerbers/` contains copper, mask, silkscreen, paste, outline, the Gerber job file, separate plated/non-plated drills, drill maps and a drill report. Keep the intended copper weights and verify drill plating with the fabricator. The job file's bounding dimensions include the plotted outline stroke; the native Edge.Cuts geometry is authoritative.

`BOM.csv` lists individual references, values, footprints and key manufacturer part numbers. Generic resistors and capacitors still require sourcing against the stated tolerance, package and voltage rating. Input capacitors are rated 50 V where specified. Output ceramics must retain sufficient effective capacitance under DC bias.

**D2 and D102 must be SMBJ26CA (bidirectional).** The earlier SMBJ26A listing was incorrect for the existing pad connections. Do not substitute the unidirectional part without redesigning and checking the circuit.

`placement.csv` uses KiCad's absolute origin, millimetres, Y-negative convention and explicit side/rotation fields. Normalize coordinates to the assembler's convention and confirm bottom-side rotations. Cross-check against the [assembly PDF](../docs/drawings/RAPS-5_assembly.pdf).

The native project is the source of truth. After editing it, rerun ERC/DRC and regenerate the schematic, BOM, placement and fabrication exports together; do not mix exports from different revisions.
