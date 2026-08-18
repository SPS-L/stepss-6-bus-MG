# 6-Bus Microgrid Power-Flow Case

Six-bus microgrid load-flow case in the Helios data format, for the [STEPSS](https://stepss.sps-lab.org/) AC power-flow engine.

Buses A, D, E and F are at 6 kV and buses B and C at 11 kV, connected by two lines (B-C, D-E) and four transformers; loads are at buses D (1 MW + 0.3 Mvar) and E (4 MW + 1.2 Mvar), and two generators are at buses A (slack) and F (2 MW). This is a power-flow-only case, with no dynamic data or disturbance scenario. The solved bus voltages are provided in `volt_rat.dat`, and `6bus.svg` is a one-line diagram template that Helios fills in with the solved values.

## Repository Files

| File | Description |
|------|-------------|
| `6bus.dat` | Power-flow data: solver tolerance settings, 6 buses, 2 lines, 4 transformers, 2 generators, slack bus definition |
| `volt_rat.dat` | Solved bus voltages (`LFRESV` records) for all six buses, as written by the Helios `VT` command |
| `6bus.svg` | One-line diagram template, with `%A`-`%U` placeholder codes substituted by the Helios `1` command |

## Quick Start

Run `helios` from the repository root and load the data file when prompted:

```text
Next DATA file (<return> to end) : 6bus.dat
Next DATA file (<return> to end) : <return>
```

After the Newton-Raphson iterations converge, use the main menu to inspect results (`D`), render the one-line diagram (`1`), dump all records back out in input format (`DF`), or regenerate the voltages and transformer ratios (`VT`, which produces output like `volt_rat.dat`). Type `E` to exit.

The same case can be solved from Python:

```python
from stepss.helios import HeliosSession

with HeliosSession() as pf:
    pf.load_file("6bus.dat")
    pf.solve()
    v, angle = pf.get_bus_voltage("D")
```

## Status

**Runs.** The power flow converges in 3 Newton-Raphson iterations, and the regenerated
`volt_rat` reproduces the distributed `volt_rat.dat` to every printed digit.

This is a power-flow-only case: the repository holds no dynamic data and no disturbance file,
so nothing here reaches RAMSES.

`PowerFlowWithHelios.ipynb` covers the HELIOS API on this case: solving, querying, the SVG
one-line diagram, a PV curve traced by continuation, and N-1 contingency screening. One
result from it is worth knowing before you trust any screen: opening the `A-B` transformer
de-energises the whole network, and because the screen then evaluates only the island that
still holds the slack bus, it reports that contingency as **acceptable**. Check connectivity
alongside the acceptance flag.

Verified against **stepss 3.70** (RAMSES 3.70, HELIOS 1.4.1) on Linux.

## Documentation

The data formats are documented in the STEPSS user guide at [stepss.sps-lab.org/user-guide/power-flow](https://stepss.sps-lab.org/user-guide/power-flow/).

## License

This repository is licensed under the [Apache License 2.0](LICENSE).

## Authors

Developed and maintained by the [Sustainable Power Systems Laboratory (SPS-L)](https://sps-lab.org/) at the Cyprus University of Technology, under the direction of Dr. Petros Aristidou.
