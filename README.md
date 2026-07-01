# Low Power LoRa Weather Station

A solar-powered, battery-free LoRa sensor node designed to run indefinitely on a supercapacitor charged by a small solar cell. Built around the Semtech **SX1262** LoRa transceiver (915 MHz) and the ultra-low-power **MSPM0L110x** MCU, this board is intended as a compact environmental monitoring platform. The entire board measures just **23 x 25 mm**, with the back side fully covered by the solar cell.

<p align="center">
  <img src="images/Low%20Power%20LoRa%20Front%20View.png" width="450" alt="Front View">
</p>

## Overview

Most LoRa sensor nodes rely on a battery, which eventually needs to be replaced or recharged. This project avoids that entirely by harvesting energy from a small solar cell into a supercapacitor, using a dedicated energy harvesting IC to manage charging and a MOSFET load switch to keep quiescent draw as low as possible. The result is a small, maintenance-free node that can be deployed and left alone.

This repository covers the hardware only — schematic, PCB layout, and fabrication files. No firmware is included.

## Key Features

- **LoRa connectivity** via the SX1262 transceiver, matched for the 915 MHz band
- **External antenna** via an MMCX connector
- **Solar energy harvesting** using the BQ25504 harvester IC and a KXOB121K04F-TR solar cell
- **Supercapacitor energy storage** (no battery, no battery replacement)
- **MOSFET load switch** controlled by the MCU to cut power to the rest of the board when sleeping
- **I2C environmental sensing**: temperature/humidity (SHT4x) and a barometric pressure sensor (LPS22HB)
- **Low-power MCU**: MSPM0L110x, chosen for its wide operating voltage range and low BOM cost (~$1.50)
- **SWD programming header**, plus test points for easy bring-up and debugging
- 4-layer PCB with a split power plane for cleaner routing
- **Extremely compact**: 23 x 25 mm board, with the entire back side covered by the solar cell

## How It Works

1. The solar cell charges a supercapacitor through the BQ25504 energy-harvesting IC, which is designed to extract power efficiently even from small/weak sources like a photovoltaic cell.
2. A P-MOSFET, switched by the MCU, gates power (VSYS) to the SX1262 and sensors, keeping the idle/sleep current as low as possible between transmissions.
3. The MSPM0L110x wakes periodically, reads the temperature/humidity and pressure sensors over I2C, and transmits the data over LoRa using the SX1262 and its 915 MHz matching network, out through the MMCX antenna connector.
4. The system was sized so that the supercapacitor can supply the ~15–30 mJ needed for a transmit burst, although the supercapacitor selected is highly overrated for the task.

## Hardware

| Component | Part | Notes |
|---|---|---|
| LoRa Transceiver | SX1262 | Reference design used for RF matching network, tuned for 915 MHz |
| Antenna | MMCX connector | External antenna required |
| MCU | MSPM0L110x | I2C + SPI, wide voltage range, low cost |
| Programming | SWD | Via on-board header |
| Energy Harvester | BQ25504 | Manages charging from the solar cell |
| Solar Cell | KXOB121K04F-TR | Digikey; custom KiCad footprint + 3D model |
| Energy Storage | CXP-3R0335R-TW Supercapacitor | Selected for >0.1 F capacitance and <0.2 Ω ESR |
| Load Switch | P-MOSFET | Controlled by MCU to gate VSYS |
| Humidity/Temp Sensor | SHT4x | I2C |
| Pressure Sensor | LPS22HB | I2C |
| Antenna Switch | PE4259 | RF switch for the SX1262 front end |
| PCB Size | 23 mm x 25 mm | Back side fully covered by the solar cell |

### 3D Renders

<p align="center">
  <img src="images/Low%20Power%20LoRa%20Front%20View.png" width="320" alt="Front View">
  <img src="images/Low%20Power%20LoRa%20Bottom%20View%203D.png" width="320" alt="Bottom View">
</p>

### PCB Layers

<p align="center">
  <img src="images/Low%20Power%20LoRa%20Layer%201.png" width="220" alt="Layer 1">
  <img src="images/Low%20Power%20LoRa%20Layer%202.png" width="220" alt="Layer 2">
  <img src="images/Low%20Power%20LoRa%20Layer%203.png" width="220" alt="Layer 3">
  <img src="images/Low%20Power%20LoRa%20Layer%204.png" width="220" alt="Layer 4">
</p>

Full schematic: [`Low Power LoRa Schematic.pdf`](images/Low%20Power%20LoRa%20Schematic.pdf)

## Repository Structure

```
.
├── KiCAD/
│   ├── Low Power LoRa.kicad_pcb     # PCB layout
│   ├── Low Power LoRa.kicad_prl     # Project local settings
│   ├── Low Power LoRa.kicad_pro     # Project file
│   └── Low Power LoRa.kicad_sch     # Schematic
├── fabrication/
│   ├── bom.csv                      # Bill of materials
│   ├── gerbers.zip                  # Gerber files for PCB fabrication
│   └── pick_and_place.csv           # Pick-and-place file for assembly
├── images/
│   ├── Low Power LoRa Bottom View 3D.png
│   ├── Low Power LoRa Front View.png
│   ├── Low Power LoRa Layer 1.png
│   ├── Low Power LoRa Layer 2.png
│   ├── Low Power LoRa Layer 3.png
│   ├── Low Power LoRa Layer 4.png
│   └── Low Power LoRa Schematic.pdf
├── LICENSE
└── README.md
```

## Ordering the Board

Everything needed to fabricate and assemble the board is in the [`fabrication/`](fabrication/) folder:

- **`gerbers.zip`** — Gerber files for PCB fabrication
- **`bom.csv`** — Bill of materials
- **`pick_and_place.csv`** — Pick-and-place file for automated assembly

You will also need an MMCX-compatible 915 MHz antenna, since none is included on the board itself.

## Programming

The MSPM0L110x is programmed via the on-board SWD header. Connect a compatible SWD programmer/debugger to flash and debug the MCU.

## Acknowledgments

- SX1262 RF matching network based on Semtech's reference design.
- BQ25504 layout guidance based on Texas Instruments' reference design.
- Review from r/PrintedCircuitBoard.
- Thank you to the Macondo team at Hackclub!

## License

See [`LICENSE`](LICENSE) for details.
