# RAPS-5 V1 — Design notes

This document describes the V1 design intent and nominal calculations. Values are not measurements from assembled hardware. The native KiCad files and BOM define the actual implementation.

## Circuit and expected operation


| Function | Implementation |
|---|---|
| Battery inputs | J1 BAT A, J2 BAT B; each pin 1 positive, pin 2 GND; 2S–6S LiPo |
| Protection per input | 5 A Littelfuse 0451005.MRL; DMP6023LE-13 reverse PMOS; 12 V gate clamp; SMBJ26CA TVS |
| Input filter | 22 µF / 50 V bulk, 742792410 bead, two 4.7 µF / 50 V ceramics, local 2.2 µF / 50 V VIN bypass |
| Converter per path | TPS54561DPRR, a 60 V / 5 A TPS54560 equivalent with power-good output; approximately 400 kHz |
| Buck power stage | 10 µH XAL8080-103MED; STPS5L60U 5 A / 60 V catch diode; two 47 µF / 16 V ceramics |
| Setpoints | A: 5.200 V nominal, 55.0 kΩ / 10.0 kΩ; B: 5.152 V nominal, 54.4 kΩ / 10.0 kΩ; 0.1% feedback resistors |
| OR-ing | Independent LTC4412ES6 controllers and DMP6023LE-13 PMOS devices; drain at local output, source at BUS; CTL grounded |
| Common output | J3, pin 1 BUS, pin 2 GND; 220 µF / 10 V polymer plus two 22 µF / 16 V ceramics |
| Status | Green PATH A OK and PATH B OK from converter PG; green OUTPUT OK from TPS3808G01 supervisor |
| Output supervision | 112 kΩ / 10.0 kΩ divider gives 4.941 V nominal falling threshold; 1 nF CT gives approximately 6.2 ms release delay |

Each converter is designed to carry the complete 3 A load. OR-ing blocks BUS backfeed into an inactive converter. The LEDs indicate voltage qualification, not which source carries current. PG_A, PG_B, and PG_BUS are available as test points with local pullups; check signal compatibility before connecting external logic.

The 50 mV nominal offset favors A but does **not** guarantee source priority across regulator reference and resistor tolerances. At light load, BUS is approximately the higher setpoint minus the ideal-diode regulation drop. At 3 A, MOSFET resistance and copper loss become significant: 35 mΩ gives about 105 mV drop and 0.315 W dissipation in the active OR-ing MOSFET at its specified test conditions. Resistance increases with temperature. Therefore 5.20 V is the pre-OR setpoint, and loaded BUS may be around 5.1 V. Check that the avionics load accepts the measured range.

The EN divider sets conservative 2S undervoltage behavior: approximately 6.5 V startup and 6.2 V shutdown, including nominal EN pin currents. Regulation is not promised down to a deeply discharged 2S battery at 6.0 V. The upper normal input is 25.2 V for a fully charged 6S pack. Confirm actual UVLO thresholds during bring-up.

At 25.2 V input, 5.2 V output, 400 kHz and 10 µH, nominal inductor ripple is about 1.03 A peak-to-peak. Peak current at 3 A load is about 3.52 A. The selected inductor has substantial saturation-current margin. Select the specified output ceramics or replacements with at least 30 µF effective capacitance each at 5.2 V. The 10 kΩ / 22 nF / 220 pF compensation is a prototype starting point; validate stability with the connected OR-ing stage and BUS capacitance.

## PCB implementation

The 50 × 40 mm board uses four copper layers and a nominal 1.6 mm FR-4 stack. Components populate both sides. Four 2.2 mm mounting holes accept M2 hardware.

| Layer | Copper | Main role |
|---|---|---|
| F.Cu | 2 oz | Switching stages and local regulated-output pours |
| In1.Cu | 1 oz | Common ground plane with ordinary pad/via clearances |
| In2.Cu | 1 oz | Separate protected-input and BUS pours, signal routing |
| B.Cu | 2 oz | Controls, bulk capacitors and ground pours |

Main outer power routes are typically 1.0–1.5 mm wide. Some short clearance-limited sections are 0.50–0.80 mm. Thin 0.20 mm traces serve control, sensing, bootstrap and test-point connections rather than the main load path. Switching nodes remain on the top layer near each converter, catch diode and inductor. Exposed pads use thermal vias; main power transitions use multiple plated barrels.

The intended copper weights and adequate via plating are part of the thermal design assumptions. Track width alone does not establish continuous-current capability: confirm temperature rise with the intended enclosure, airflow and load.

## Protection boundaries

- Each input has its own 5 A fuse, reverse-polarity PMOS, 12 V gate clamp, TVS and filter.
- D2/D102 are **SMBJ26CA bidirectional TVS diodes**. The previous unidirectional SMBJ26A part number was corrected to match the existing circuit and footprint pad connections.
- Independent LTC4412/PMOS stages block BUS backfeed into an inactive converter under intended operation.
- The inputs share ground. This is redundancy of the positive supply paths, not galvanic isolation.
- The output has no independent overvoltage disconnect. A shorted buck high-side switch can put battery voltage on the BUS.
- The common BUS, ground and output connector remain common failure points.

## Status and test points

PATH A OK and PATH B OK use the buck converters' power-good outputs. OUTPUT OK uses the TPS3808G01 supervisor. These signals indicate voltage qualification, not source current contribution.

Battery, regulated-rail, BUS, GND and power-good test points support initial bring-up and fault investigation. PG_A, PG_B and PG_BUS use local pullups near the 5 V domain; verify compatibility before connecting external logic. The BUS supervisor has a nominal falling threshold of 4.941 V and approximately 6.2 ms release delay.

## Validation plan

Start with one current-limited input and no avionics load. Verify each path separately, then both together. Measure local outputs and BUS, ripple, load-step response, startup/shutdown, reverse current and source-disconnection transients. Test the full 3 A target on either channel alone at input-voltage and ambient extremes.

See the [bench procedure](../validation/BENCH_VALIDATION.md) and [measurement worksheet](../validation/bench_results.csv). No physical bench tests have been executed.

## Primary component references

[TPS54561](https://www.ti.com/lit/ds/symlink/tps54561.pdf), [LTC4412](https://www.analog.com/media/en/technical-documentation/data-sheets/ltc4412.pdf), [DMP6023LE](https://www.diodes.com/datasheet/download/DMP6023LE.pdf), [TPS3808](https://www.ti.com/lit/ds/symlink/tps3808.pdf), [STPS5L60](https://www.st.com/resource/en/datasheet/stps5l60.pdf), [742792410 bead](https://www.we-online.com/components/products/datasheet/742792410.pdf), [Coilcraft XAL8080](https://www.coilcraft.com/en-us/products/power/shielded-inductors/molded-inductor/xal/xal8080/).
