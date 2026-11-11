# PROCESS/LAB — Industrial Process Simulator

A **fully offline**, browser-based simulation of an industrial heated-water tank. It simulates a dynamic physical process, visualizes its measurements and actuators, detects abnormal conditions, and takes automatic or operator-directed control actions with supervisory safety interlocks.

**Important:** This is an educational *simulator*, not a validated digital twin, PLC, SCADA, or safety-instrumented system. It must not be connected to or used to control real plant equipment.

## Live website (GitHub Pages)

Once GitHub Pages is enabled, the application will be available at:

`https://oia7.github.io/Industrial-Process-Simulator/`

This is the **UI link to share on Blackboard**. To view the source files and automated tests, visit the public GitHub repository at `https://github.com/oia7/Industrial-Process-Simulator`.

> The repository has been created. GitHub Pages must still be enabled and verified.



### How to publish

1. Create a **public**, empty GitHub repository named `Industrial-Process-Simulator` under account `oia7` (do not initialize it with a generated README because one is included here).
2. Extract the GitHub-ready ZIP and upload **the contents** of the extracted folder to the repository root (`index.html` must be directly at the root). Use **Add file → Upload files** to drag in the extracted files/folders, then commit to `main`.
3. Navigate to **Settings → Pages**, choose **Deploy from a branch**, select **main** and **/(root)**, and click **Save**.
4. Once Pages reports a live site, open the URL above. Confirm the tank values update, setpoints change, fault toggles activate alarms, and the E-stop works.
5. Submit **only the Pages link** on Blackboard. The source code is public to anyone who knows or finds the repository, even if only the live UI link is shared.

The `.nojekyll` file prevents unnecessary Jekyll processing. The GitHub Actions test workflow runs the simulator regression tests on push and pull requests (if Actions are enabled for the repository). No deploy workflow or secrets are required.

## Run the application

1. Extract the ZIP archive to a folder (or open the deployed GitHub Pages URL).
2. Double-click `index.html` in Microsoft Edge, Chrome, or Firefox. No server, installation, database, npm packages or internet connection is required.
3. The application starts with automatic control enabled at **5× simulation speed**.

For a single-file copy, use `Industrial_Process_Simulator.html` (included in the ZIP and also available as a separate download); it embeds all code and styling.

## What you can do

- Watch **tank level**, **temperature**, **inlet flow**, **outlet flow**, **hydrostatic head**, **pump demand**, **valve position** and **heater power**.
- Change **level**, **temperature** and **outlet demand** setpoints in Automatic mode.
- Switch to Manual mode to set pump speed, outlet opening and heater power; **independent interlocks still override unsafe demands**.
- Inject five simulated faults: inlet blockage, outlet blockage, heater stuck ON, primary level transmitter drift, and outlet valve jammed open.
- Observe active/cleared alarms, acknowledgment state, event history, live trends, and the current automated control decision.
- Test emergency stop, pause/resume, speed (1×, 5×, 15×, 30×), reset, and CSV export.
- Run with no cloud connection; simulation state is **in memory** and is lost on refresh/close. Export CSV before leaving if you want to retain history.

## Simulated process model

A 500-liter perfectly mixed tank with ambient 22 °C and an inlet water source at 22 °C. Level evolves from the mass balance and temperature from a simplified heating/cooling/mixing balance:

```
dV/dt = Q_in - Q_out
T_next = T + dt * [0.44 * heater_fraction
                   - 0.0025 * (T - 22)
                   - (Q_in / effective_volume) * (T - 22)]
Q_in = 3.15 L/s * pump_fraction * inlet_fault_factor
Q_out = 3.0 L/s * valve_fraction * sqrt(level_percent / 60) * outlet_fault_factor
```

All parameters are synthetic educational assumptions, **not calibrated equipment data**. Inflow/outflow volume is clamped to tank capacity. Hydrostatic head is estimated for 3 meters maximum water column using a linear relationship. Internal physics/control is advanced at steps no larger than 0.25 simulated seconds.

## Automatic control and diagnostic logic

- **Level:** feed-forward of expected outlet flow plus proportional-integral adjustment to inlet pump speed.
- **Temperature:** estimated heat-loss feed-forward plus proportional-integral adjustment to heater duty.
- **Process demand:** operator-configurable discharge-valve target.
- **Sensor fallback:** when primary and redundant level transmitter measurements differ by at least 8 percentage points, the level controller switches to the redundant measurement and raises an alarm.
- **Alarms:** high/high-high level, low/low-low level, high/trip temperature, actuator feedback mismatch, flow delivery mismatch, sensor disagreement, and emergency stop.

### Simulated safety thresholds

| Safety function | Trip | Automatic reset | Safety action |
|---|---:|---:|---|
| High-high level | ≥92% | ≤86% | Stop inlet pump; command outlet fully open |
| Low-low level | ≤9% | ≥15% | Disable heater; command outlet closed |
| High temperature | ≥76°C | ≤69°C | Open heater master safety contactor |
| Operator E-stop | Button engaged | Operator releases | Stop inlet pump and heater; command outlet closed |

The model includes **actuator faults**: a jammed valve may ignore the outlet-closure command, and a stuck heater relay can continue to demand heating until a *separate simulated* safety contactor cuts power. This makes fault scenarios observable while avoiding false claims about physical protection. Alarm acknowledgment does **not** clear the underlying abnormal condition.

## Files

- `index.html` — accessible HTML interface and process schematic.
- `styles.css` — responsive dark industrial dashboard styling.
- `sim.js` — process model, control logic, diagnostics, safety interlocks, history.
- `app.js` — dashboard rendering, interactions, chart, CSV exporter.
- `tests/sim.test.js` — Node.js core regression tests.

## Testing

Node.js 18+ is needed **only for testing**, not for running the application:

```bash
node --test tests/sim.test.js
```

Checks include nominal regulation, flow restrictions, temperature protection, safety overrides, sensor fallback, valve position fault, emergency stop, pause and validation.

## Scope and limitations

- Does not connect to real sensors, PLCs, Modbus, OPC UA, MQTT, or process equipment.
- No historical persistent storage, user authentication, network service, or external notification system.
- Simplified first-order process physics, idealized sensing, and supervisory PI-like control rather than instrument-certified control algorithms.
- Safety functions are illustrative; there is no certified functional safety engineering, HAZOP, SIL evaluation, IEC 61508/61511 compliance or formal model validation.
- Browser chart/history keeps up to 900 samples and the event log retains the most recent 400 events per session.

## Screenshots

![Mobile layout](docs/screenshots/mobile.png)