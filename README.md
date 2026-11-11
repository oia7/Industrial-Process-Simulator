# PROCESS/LAB — Industrial Process Control Simulator

A browser-based educational simulator for monitoring and automatically controlling an industrial heated-water tank.

**[Launch the live interface](https://oia7.github.io/Industrial-Process-Simulator/)** (requires GitHub Pages to be enabled in repository Settings).

## Features

- Dynamic 500 L tank simulation with inlet pump, outlet valve and heater
- Live monitoring of tank level, temperature, flow and pressure
- Automatic and manual process control with safety interlocks
- Fault injection, abnormal-condition detection, alarms and event history
- Emergency stop, reset and configurable simulation speed
- Trend charts and CSV data export

## Run

Open [index.html](index.html) in your browser, or access the GitHub Pages URL above once publishing is enabled. No installation, backend, database, external API or build tool is required.

The complete HTML, CSS and JavaScript application is embedded in **index.html** so that the hosted page and application source are available together. An additional multi-file development package (separate CSS/JavaScript, unit tests and screenshots) is available as a download in the original project handoff, but is **not yet included in this repository**.

## Publish on GitHub Pages

1. Go to **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select branch **main**, folder **/(root)** and **Save**.
4. When GitHub displays the live site URL, open https://oia7.github.io/Industrial-Process-Simulator/ and verify the dashboard operates.

Submit the **GitHub Pages URL** to Blackboard to show the running UI. The source code is public in this repository.

## Safety limitation

This is an **educational process simulator**, not a validated PLC, SCADA system, digital twin, or certified safety controller. It must not be used to control physical industrial equipment. Data is simulated in the browser and is not persisted; export the CSV before closing if you need a record.
