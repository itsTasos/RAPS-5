# RAPS-5 V1 — Prototype bench validation

Prepared 2026-10-02. **Not executed: requires an assembled board and test equipment.** ERC/DRC and trace widening do not replace these measurements. Record the PCB revision, assembly substitutions, copper weight, ambient temperature and instrumentation before testing.

## Equipment and setup

- Two independent, current-limited bench supplies with common negative connections. Start at 8 V, with no output load and a low current limit; increase the limit cautiously if capacitor charging prevents startup. Use roughly 4 A limits per input for full-load tests, respecting wiring and instrument ratings.
- Electronic load supporting at least 3 A continuous at 5 V; calibrated DMMs for each input, local output and BUS.
- Oscilloscope with short ground springs or appropriate differential probes. Measure BUS at J3 and converter ripple at the local output capacitors. Use 20 MHz bandwidth for the ripple screen and full bandwidth for overshoot/ringing observations.
- Thermocouples or calibrated thermal imaging for U1/U101, D3/D103, L1/L101, Q1/Q101, Q2/Q102, beads, connectors and critical vias. Insulate contact sensors electrically.
- A controlled means of disconnecting either input. Do not use an unbounded battery short or reverse a LiPo as an initial fault test.

Use the intended 2 oz outer copper and specified components. Check continuity, diode polarity, connector polarity and the WSON exposed-pad solder joints before power. Keep the load disconnected during initial startup.

## Measurement matrix

Test A alone, B alone, and both sources. For single-source cases, the selected path must support the complete load. Do not assume the sources share current evenly.

| Input voltage per active source | Load-current steps |
|---|---|
| 6.7 V | 0.1 A, 1 A, 2 A, 3 A |
| 8.4 V | 0.1 A, 1 A, 2 A, 3 A |
| 16.8 V | 0.1 A, 1 A, 2 A, 3 A |
| 25.2 V | 0.1 A, 1 A, 2 A, 3 A |

For both-source tests include A=6.7 V/B=25.2 V and A=25.2 V/B=6.7 V, then repeat with similar voltages. Slowly ramp each input through startup/shutdown to record UVLO; nominal thresholds are about 6.5 V/6.2 V, not acceptance guarantees.

Record input current, local 5.20V_A/5.15V_B, BUS voltage, ripple, LED/PG state and temperatures at each step. Keep the 3 A tests running to thermal equilibrium: at least 30 minutes and until the hottest measured part changes less than 1 °C in 5 minutes; extend to 60 minutes if necessary. Repeat under intended enclosure, airflow and maximum ambient conditions before deciding the continuous-current rating.

## Proposed screening criteria

These are initial prototype screens, not certification requirements. The attached avionics specifications must take precedence if stricter. A screen failure requires investigation and possibly a design change; do not silently loosen limits.

- Local regulated outputs within ±2% of their nominal setpoints in steady state. A/B regulator tolerances can overlap, so do not require guaranteed A priority.
- BUS steady state within 4.90–5.30 V at the connector for the test matrix. Record MOSFET/copper drop separately; a load with a tighter minimum voltage may need a different OR-ing MOSFET or revised setpoints.
- Output ripple screen: no more than 100 mV peak-to-peak at 20 MHz bandwidth, with probing artifacts excluded.
- Case-temperature screen: no measured power-component case above 80 °C at 25 °C ambient. Stop a test at 90 °C case temperature, sustained oscillation, uncontrolled current or abnormal output voltage. Case temperature is not junction temperature: estimate junction temperatures using each part's applicable thermal information and the actual PCB, and retain margin to its ratings.
- No sustained oscillation or repeated restart under any steady load. Observe input and output ringing as well as BUS ripple.

## Load steps, startup and switchover

1. Capture startup into no load and into 3 A. Record peak BUS voltage, rise time, PG timing and any restart.
2. Step the load between 0.1 A and 3 A, initially with approximately 1 ms transitions, then with the intended load's actual slew rate. Capture both directions for each input voltage and each source configuration. Inspect converter outputs and BUS for ringing or sustained oscillation; revise compensation if measurements demand it.
3. With both inputs applied and 3 A load, disconnect A, reconnect A, disconnect B and reconnect B. Repeat at the asymmetric voltage pairs. Capture BUS droop/overshoot and interruption duration. Initial transient screen: remain within 4.75–5.50 V; a load's brownout/absolute maximum limits override this range. Measure the actual interruption duration rather than assuming the ideal-diode controller makes it zero.
4. Measure reverse current into the inactive local converter/output branch using instrumentation that does not materially change the circuit. Compare it with the specified controller and MOSFET leakage behavior at the measured temperature and the load's requirement. Avoid adding a shunt with significant series drop in the control sense path.
5. Ramp BUS through the output supervisor threshold and record falling and rising thresholds, hysteresis and release delay. Confirm the three LEDs correspond to their respective PG signals. LEDs do not identify load-current ownership.
6. After ordinary operation passes, perform current-limited fault tests with bench supplies and protected instrumentation. Record fuse/protection behavior. A buck high-side short is not contained by this architecture: there is no independent output overvoltage disconnect.

## Result record

Use `outputs/bench_results.csv`. Leave measurements and Pass/Fail blank until physically measured. Attach scope screenshots and temperature logs, including probe locations. Final approval of 3 A continuous operation requires satisfactory steady state, thermal and transient results under the actual installation conditions.
