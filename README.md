# ESP32 LoRa Solar Node – MPPT, Boost/Buck, INA219, USB-C

A solar-powered ESP32 + LoRa (RFM95W) node board. It charges a single-cell Li-ion battery from a solar panel with MPPT (CN3791), generates stable 5V and 3.3V rails, monitors battery voltage and current (INA219), and programs over USB-C (CH340C with auto-reset).

**Board:** 2-layer PCB, 75 × 70 mm, 1.6 mm  
**Design tool:** KiCad 10  
**Revision:** 3 (schematic title block: rev 1, 2026-10-13)

## Features

- ESP32-WROOM-32 module
- LoRa radio: RFM95W-915S2 (915 MHz) with U.FL antenna connector
- Solar MPPT charger: CN3791 for a 1S Li-ion/LiPo battery (4.2 V)
- Solder jumpers to set the solar panel MPPT voltage: 5 V / 9 V / 12 V / 18 V
- P-MOSFET (AOD4185) in the solar charge path
- INA219 current/voltage monitor with a 10 mΩ shunt on the battery line
- MT3608 boost converter (battery → 5 V)
- STI3408B buck converter (5 V → 3.3 V)
- USB-C programming port with CH340C and automatic reset/boot (DTR/RTS)
- Charge and done LEDs from the CN3791
- 2 × I2C ports and 3 × ADC sensor ports (JST-EH), each with selectable sensor supply (5 V or 3.3 V)
- Reset and Boot buttons
- Test points for 3.3 V, 5 V and 4.2 V
- 0 Ω links in the power path (useful for measuring current or isolating a section)

## Power Path

```
Solar panel ──► Q1 (P-MOSFET) ──► CN3791 MPPT ──► INA219 shunt ──► Battery (4.2 V)
                                                                       │
                                  ESP32 / LoRa / sensors ◄── 3.3 V ◄── 5 V (MT3608 boost) ◄─┘
                                                              STI3408B buck
```

## Pin Mapping

| ESP32 Pin | Function |
|---|---|
| GPIO18 | LoRa SCK |
| GPIO19 | LoRa MISO |
| GPIO23 | LoRa MOSI |
| GPIO4 | LoRa NSS (CS) |
| GPIO14 | LoRa RESET |
| GPIO26 | LoRa DIO0 |
| GPIO27 | LoRa DIO1 |
| GPIO21 | I2C SDA (INA219, J2, J3) |
| GPIO22 | I2C SCL (INA219, J2, J3) |
| GPIO32 | ADC port 1 (J7) |
| GPIO33 | ADC port 2 (J8) |
| GPIO34 | ADC port 3 (J9) |
| GPIO0 | Boot button / auto-reset |
| EN | Reset button / auto-reset |
| GPIO1 / GPIO3 | TX / RX to CH340C |

Each ADC input has a 10 kΩ / 15 kΩ divider, so a 5 V sensor output is scaled to about 3 V.

## Connectors

| Ref | Name | Description |
|---|---|---|
| J1 | USB-C | Programming and serial |
| J4 | U.FL | LoRa antenna |
| J5 | Battery | 1S Li-ion/LiPo (GND, BATT+) |
| J6 | Solar panel | Panel input (VCC, GND) |
| J2, J3 | I2C | SCL, SDA, 3.3 V, GND |
| J7, J8, J9 | ADC | Signal, sensor supply, GND |

## Solder Jumpers

| Ref | Function |
|---|---|
| 5V0, 9V1, 12V2, 18V3 | Select the solar panel MPPT voltage. Close only the one that matches your panel. |
| JP1, JP3, JP4 | Select the supply of ADC port 1, 2 and 3: 5 V or 3.3 V |

## Main Components

| Ref | Part |
|---|---|
| U1 | ESP32-WROOM-32 |
| U2 | RFM95W-915S2 (LoRa) |
| U3 | MT3608 (boost) |
| STI3408B1 | STI3408B (buck) |
| U4 | INA219 |
| U5 | CN3791 (MPPT charger) |
| U6 | CH340C (USB-UART) |
| Q1 | AOD4185 (P-MOSFET) |
| Q4, Q5 | BC847 (auto-reset) |
| RSHUNT1 | 0.01 Ω, 2512 |
| L1, L3 / L2 | 4.7 µH / 22 µH |

## Usage Notes

- Close **only one** MPPT jumper (5V0, 9V1, 12V2 or 18V3), based on your solar panel's maximum power voltage.
- Use a single-cell Li-ion/LiPo battery. The CN3791 in this design charges to 4.2 V.
- The RFM95W-915S2 covers the 915 MHz band. Check the allowed LoRa frequency in your country before use, and always attach an antenna before transmitting.
- Upload firmware over USB-C. Auto-reset is handled by the CH340C, so no button presses are needed.
- INA219 is on the I2C bus together with J2 and J3, so avoid address conflicts with your sensors.
- The INA219 address (0x40) is the default. Check the A0/A1 pins in the schematic if you changed it.

## Repository Files

- `esp32-lora-mppt-usbtypec-rev3.kicad_pro` – KiCad project
- `esp32-lora-mppt-usbtypec-rev3.kicad_sch` – schematic
- `esp32-lora-mppt-usbtypec-rev3.kicad_pcb` – PCB layout

Open the project with KiCad 10 or newer. Custom symbols and footprints (CN3791, STI3408B) must stay in the project folder.

## License

Add your license here (e.g. MIT, CERN-OHL-P).
