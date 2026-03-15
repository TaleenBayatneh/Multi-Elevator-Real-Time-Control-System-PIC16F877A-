# Multi-Elevator Real-Time Control System (PIC16F877A)

A real-time embedded elevator control project built in assembly for the **PIC16F877A**.

This project uses a **master-slave architecture**:
- `MASTER.asm` controls elevator logic and scheduling.
- `SLAVE_F0.asm`, `SLAVE_F1.asm`, and `SLAVE_F2.asm` handle floor I/O (buttons + LCD updates) over I2C.

It is designed for simulation in **Proteus** and development with **MPLAB (MPASM)**.

## Features

- Controls **3 elevators** across **3 floors** (GF, 1st, 2nd)
- Real-time main loop with sensor scanning and request processing
- Master gathers:
  - Elevator sensors (door closed/open, IR, overweight)
  - Cabin buttons
  - Floor call buttons (from slave nodes)
- Elevator states include:
  - Idle
  - Moving Up / Down
  - Door Open / Door Close
- I2C communication between master and floor slave controllers
- LCD status updates from master to floor slaves
- Door timer and buzzer handling logic

## System Architecture

- **Master MCU** (`MASTER.asm`)
  - PIC16F877A configured as I2C master
  - Runs dispatch/scheduling and state updates for 3 elevators
  - Polls slave floor nodes for button states
  - Sends elevator status packets for LCD display

- **Slave MCUs** (`SLAVE_F0.asm`, `SLAVE_F1.asm`, `SLAVE_F2.asm`)
  - PIC16F877A configured as I2C slaves
  - I2C addresses:
    - F0: `0x20` (master-side write/read base uses `0x40`/`0x41`)
    - F1: `0x21` (master-side `0x42`/`0x43`)
    - F2: `0x22` (master-side `0x44`/`0x45`)
  - Read local button inputs
  - Receive elevator status and update local LCD

## Project Files

- `MASTER.asm` – Main controller firmware
- `SLAVE_F0.asm` – Floor 0 slave firmware
- `SLAVE_F1.asm` – Floor 1 slave firmware
- `SLAVE_F2.asm` – Floor 2 slave firmware
- `MASTER.HEX`, `SLAVE_F0.HEX`, `SLAVE_F1.HEX`, `SLAVE_F2.HEX` – Prebuilt firmware images
- `New Project.pdsprj` – Proteus project

## Build

### Option 1: Use provided HEX files (quick start)
Load existing `.HEX` files directly in Proteus.

### Option 2: Rebuild from ASM
1. Open each `.asm` file in MPLAB (MPASM toolchain).
2. Build firmware for **PIC16F877A**.
3. Replace/refresh corresponding `.HEX` files in Proteus.

> Note: All source files are configured with XT oscillator and watchdog disabled in `__CONFIG`.

## Run in Proteus

1. Open `New Project.pdsprj` in Proteus.
2. Assign the correct `.HEX` to each MCU instance:
   - Master MCU → `MASTER.HEX`
   - Floor 0 MCU → `SLAVE_F0.HEX`
   - Floor 1 MCU → `SLAVE_F1.HEX`
   - Floor 2 MCU → `SLAVE_F2.HEX`
3. Verify I2C wiring (`SCL`, `SDA`) and pull-up resistors.
4. Start simulation.
5. Trigger floor/cabin requests and observe:
   - Elevator movement states
   - Door open/close timing
   - LCD status updates

## Communication Protocol (I2C)

Defined command bytes:
- `CMD_UPDATE_LCD = 0x01` → master sends elevator status to slaves
- `CMD_GET_BUTTONS = 0x02` → master requests floor button state from slaves

## Notes

- This is a low-level assembly implementation optimized for deterministic control flow.
- Timing behavior depends on oscillator settings and simulation timing.
- Logic can be extended for more floors/elevators by expanding request maps, addressing, and state handling.

