# MCB-Datalogger-with-memory-and-clock
# MCU Datalogger with Memory and Real-Time Clock

A compact, low-power data-logging board designed in KiCad (v9.0.4). The board is built around an ATmega328P-AU microcontroller, a DS1337S real-time clock (RTC) with a 32.768 kHz crystal, and two 24LC1025 I²C EEPROM devices for extended timestamped data storage. The design is a 2-layer custom PCB sized 87.63 mm × 39.116 mm and is powered by a coin-cell battery (BT1) for RTC backup. Data can be accessed via I²C or UART; the MCU is programmable by ICSP or a UART bootloader.

---

## Overview

This project is a lightweight, modular data logger intended for timestamped sensor data collection. It provides:

- Reliable timekeeping (DS1337S with coin-cell backup)
- Extended non-volatile storage (two 24LC1025 EEPROMs)
- Standard interfaces for integration (I²C, UART, GPIO, ICSP)
- A compact 2-layer PCB designed in KiCad 9.0.4

---

## Features

- Microcontroller: ATmega328P-AU (16 MHz)
- Real-time clock: DS1337S with 32.768 kHz crystal
- Non-volatile memory: 2 × 24LC1025 EEPROM (I²C)
- Connectivity: I²C header, UART (RX/TX) header, GPIO header, 6-pin ICSP
- Indicators: 2 status LEDs
- Power: Coin-cell holder (BT1) for RTC backup
- Board: 2-layer PCB; 87.63 mm × 39.116 mm; 4 mounting holes
- KiCad version: 9.0.4

---

## Hardware Architecture

High-level blocks:

- MCU (ATmega328P-AU): central controller for data acquisition, timestamping, and storage
- RTC (DS1337S): battery-backed real-time clock (I²C)
- EEPROMs (2 × 24LC1025): extended I²C non-volatile storage
- Headers: I²C, UART, GPIO, and ICSP for programming and peripherals
- Power: Coin-cell for RTC backup; decoupling and power filtering on-board

Notable electrical details:

- MCU clock: 16 MHz crystal (Y2) with C2 = 22 pF and C3 = 22 pF
- RTC: DS1337S with 32.768 kHz crystal; pull-ups R1 = 10 kΩ, R2 = 10 kΩ; decoupling C1 = 0.1 µF
- EEPROMs: Two 24LC1025 on I²C with pull-ups R3 = 4.7 kΩ and R4 = 4.7 kΩ
- Status LEDs: D1 (R5 = 330 Ω), D2 (R7 = 330 Ω)
- Power decoupling: C4 = 0.1 µF

---

## Schematic

- Schematic Page 1 — Main MCU, RTC, EEPROM

  ![Schematic - Page 1](./docs/schematic_page1.png)

- Schematic Page 2 — Connectors

  ![Schematic - Page 2](./docs/schematic_page2.png)

Refer to the KiCad schematic files in the repo for full net names, pin labels, and design notes.

---

## PCB Layout

- Top view:

  ![PCB Layout - Top View](./docs/pcb_top_view.png)

### PCB summary

| Property | Value |
|---|---:|
| Layers | 2 |
| Dimensions | 87.63 mm × 39.116 mm |
| Mounting holes | 4 |
| Silkscreen | Labeled pins (I²C, UART, GPIO, ICSP) |
| Unrouted nets | 0 |
| KiCad version | 9.0.4 |

---

## Connectors / Pinout

- I²C header (4-pin): SDA, SCL, VCC, GND
- UART header (3-pin): RX, TX, GND (VCC optional)
- GPIO header: multiple MCU GPIO pins broken out (see schematic)
- ICSP (6-pin): MISO, MOSI, SCK, RESET, VCC, GND

---

## Bill of Materials (BOM)

| Ref | Qty | Part | Notes |
|---|---:|---|---|
| U1 | 1 | ATmega328P-AU | 28-pin TQFP |
| U2 | 1 | DS1337S | RTC (I²C) |
| U3, U4 | 2 | 24LC1025 | I²C EEPROM |
| Y2 | 1 | Crystal | 16 MHz (MCU) |
| Y1 | 1 | Crystal | 32.768 kHz (RTC) |
| BT1 | 1 | Coin cell holder | CR2032 or equivalent (observe polarity) |
| C1, C4 | 2 | 0.1 µF | Ceramic decoupling caps |
| C2, C3 | 2 | 22 pF | Crystal load caps |
| R1, R2 | 2 | 10 kΩ | RTC pull-ups |
| R3, R4 | 2 | 4.7 kΩ | EEPROM pull-ups |
| R5, R7 | 2 | 330 Ω | LED current-limiting |
| D1, D2 | 2 | LED | SMD or THT per footprint |
| J1 (I²C) | 1 | Header | 4-pin |
| J2 (UART) | 1 | Header | 3-pin |
| J3 (GPIO) | 1 | Header | n-pin |
| J4 (ICSP) | 1 | 6-pin ICSP | 2×3 2.54 mm |
| Mounting holes | 4 | M3 | Mechanical mounting |

Full, exportable BOM (CSV) and placement files are provided in the repo (see /bom and /fab folders if present).

---

## How to Use

Assembly:

- Solder SMD components first (MCU, RTC, EEPROMs, resistors, capacitors), then through-hole parts (headers, coin cell holder).
- Observe polarity for LEDs and coin cell.
- Place crystals and load capacitors as shown in schematic.

Power:

- Supply VCC according to the chosen components (3.3 V or 5 V depending on populated parts). Confirm the RTC and EEPROM voltage ranges before applying power.
- Coin cell provides RTC backup via the VBAT connection on the DS1337S. Ensure correct polarity and secure holder.

Basic operation:

- The MCU collects sensor data (I²C/GPIO), requests current time from the DS1337S, and writes time-stamped entries to the EEPROM(s).
- Retrieve logs via UART (connect a USB‑to‑TTL adapter: RX→TX, TX→RX, GND→GND) at the baud rate set by the firmware.
- Optionally access EEPROMs directly over I²C from a host microcontroller or logic analyzer.

I²C notes:

- On-board pull-ups are provided (R1/R2 for RTC, R3/R4 for EEPROM). If your external bus already includes pull-ups, verify combined pull-up resistance is within recommended limits.
- Addressing: consult the 24LC1025 datasheet for device addressing and block management if both EEPROMs use similar address pins.

---

## Programming the MCU

ICSP (recommended for initial programming and bootloader burn):

- Connect a 6-pin ISP programmer (USBasp, AVRISP, etc.) to the ICSP header: MISO, MOSI, SCK, RESET, VCC, GND.
- Example avrdude command (USBasp):

  avrdude -c usbasp -p m328p -U flash:w:firmware.hex

UART (if a bootloader is installed):

- Use a USB‑to‑TTL adapter to the UART header. Typical Arduino/Optiboot settings for 16 MHz apply if using Arduino core.
- To burn a bootloader via Arduino IDE, connect an ISP programmer and select the appropriate board/frequency, then Tools → Burn Bootloader.

Firmware recommendations:

- Implement time queries to DS1337S via I²C and write structured records to EEPROM with checksums.
- Implement wear-leveling or a ring-buffer log scheme to maximize EEPROM lifespan.
- Provide a simple UART command set for log extraction and configuration.

---

## Testing & Validation

- Verify MCU oscillator and boot behavior after programming.
- Confirm RTC register reads/writes and timekeeping across coin-cell swaps.
- Use an I²C scanner to validate device addresses for RTC and EEPROMs.
- Test UART responses and data export functionality.

---

## Future Improvements

- Add optional power domain/regulator for external supplies with coin-cell backup for RTC.
- Add battery charging and low-battery detection circuitry.
- Add footprints for common sensors (temperature, humidity, pressure) on-board.
- Implement hardware write-protect for EEPROMs and optional encryption of stored logs.
- Provide a 3D-printable enclosure and mounting guides.

---

## Contributing

Contributions are welcome. Please:

- Open an issue to discuss non-trivial changes before submitting a PR.
- Submit focused pull requests with updated KiCad files, Gerbers, and BOM when hardware changes are included.
- Keep commit messages clear and include rationale for electrical or layout changes.

---

## Safety

- Coin cells are a choking hazard and can be dangerous if swallowed—keep away from children.
- Handle ESD-sensitive parts with appropriate precautions.
- Verify coin-cell polarity before installing.

---

## License

This project is provided under the MIT License. See LICENSE for details.

---

## Contacts

For questions or collaboration, open an issue or contact the repository owner (YUKESH0620) via GitHub.

---



