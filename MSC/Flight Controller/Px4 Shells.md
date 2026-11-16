# Accessing the PX4 / MAVLink Console on Holybro Kakute H7

This guide outlines how to establish a console session (NuttX Shell / `nsh`) on a Holybro Kakute H7 flight controller running PX4 Autopilot.

---

## Overview of Console Access Methods

The Kakute H7 supports two main approaches for console interaction:

1. **Tunneled MAVLink Console (via USB-C):** Uses MAVLink protocol (`SERIAL_CONTROL` messages) over the native USB CDC-ACM virtual COM port. Recommended for general inspection, diagnostics, running module commands, and calibration.
2. **Direct Hardware Serial Console (via UART3 / Pin Pads):** Directly attaches to the STM32H7 hardware serial console (`/dev/ttyS2`). Recommended for debugging early bootloader crashes, kernel panics, or situations where the MAVLink daemon fails to initialize.

---

## Method 1: Command-Line MAVLink Shell (`mavlink_shell.py`)

This is the standard command-line utility bundled inside the PX4 repository.

### Prerequisites

1. Ensure your user has permissions to access serial devices on Linux:
   ```bash
   sudo usermod -aG dialout $USER
   ```
   *(Log out and back in for group changes to take effect.)*

2. Install the necessary Python packages:
   ```bash
   pip3 install --user pymavlink pyserial
   ```

3. Ensure **QGroundControl or any other serial ground station is closed**, as simultaneous port access will cause resource contention.

### Connecting

1. Connect the Kakute H7 to your computer via USB-C.
2. Locate the device port (commonly `/dev/ttyACM0` on Linux, `/dev/cu.usbmodem*` on macOS, or `COMx` on Windows):
   ```bash
   ls /dev/ttyACM*
   ```
3. Navigate to your `PX4-Autopilot` workspace and run the shell script:
   ```bash
   cd /path/to/PX4-Autopilot
   ./Tools/mavlink_shell.py /dev/ttyACM0
   ```
4. Press `Enter` to display the prompt:
   ```text
   Connecting to MAVLink...
   nsh>
   ```

---

## Method 2: QGroundControl GUI (Analyze Tools)

If QGroundControl is already configured and communicating with the board:

1. Open **QGroundControl**.
2. Connect the Kakute H7 via USB-C or a wireless telemetry bridge.
3. Click the **Q** application icon in the top-left corner.
4. Open the **Analyze Tools** menu.
5. Select **Mavlink Console**.
6. Type commands into the bottom entry field and press `Enter`.

---

## Method 3: Direct Hardware UART Console (NuttX Raw `nsh`)

On the Kakute H7 board target in PX4, the raw serial debug console is configured on **USART3**.

### Hardware Wiring

Connect a **3.3V USB-to-UART / FTDI adapter** to the solder pads on the Kakute H7:

| FTDI Adapter Pin | Kakute H7 Solder Pad | Function / Description      |
|:-----------------|:---------------------|:----------------------------|
| **GND**          | **GND**              | Common ground               |
| **RX**           | **TX3**              | Flight controller TX $\rightarrow$ PC RX |
| **TX**           | **RX3**              | PC TX $\rightarrow$ Flight controller RX |

> ⚠️ **Warning:** Do **not** connect the 5V or 3.3V power pin of the FTDI adapter if the board is already receiving power via USB or battery. Always ensure signals are at **3.3V logic levels**.

### Terminal Setup

1. Connect the FTDI adapter to your computer (identified as `/dev/ttyUSB0` or similar).
2. Launch a serial terminal at **57600 baud** (8 data bits, 1 stop bit, no parity, no hardware flow control):

   * **Using `screen`:**
     ```bash
     screen /dev/ttyUSB0 57600
     ```
     *(To exit `screen`, press `Ctrl + A` then `k`, followed by `y`.)*

   * **Using `picocom`:**
     ```bash
     picocom -b 57600 /dev/ttyUSB0
     ```
     *(To exit `picocom`, press `Ctrl + A` then `Ctrl + X`.)*

3. Press `Enter` or power-cycle the board. You will receive raw boot output and land on the `nsh>` prompt directly.

---

## Essential Console Verification Commands

Once connected to `nsh>`, run these commands to inspect system status:

| Command | Purpose |
|:---|:---|
| `ver all` | Displays hardware platform, firmware version, and Git commit hash. |
| `top` | Displays active tasks, thread CPU utilization, and free memory stack. |
| `dmesg` | Displays kernel log messages and driver boot logs. |
| `uorb status` | Inspects currently published uORB topics and subscriber counts. |
| `sensors status` | Shows connection status and health of the onboard IMU, barometer, and external compass. |
| `listener <topic>` | Prints streaming data for a specific uORB topic (e.g., `listener sensor_combined`). |
| `param show <param>` | Queries runtime configuration parameters. |

---

## Troubleshooting

- **`Permission denied: '/dev/ttyACM0'`:** Make sure your user belongs to the `dialout` (or `uucp` on Arch Linux) group:
  ```bash
  sudo usermod -aG dialout $USER
  ```
- **`mavlink_shell.py` hangs on "Connecting to MAVLink...":**
  1. Verify QGroundControl or any background daemon (e.g., `mavproxy`, Micro-XRCE Agent) is not holding the port open.
  2. Unplug and replug the USB cable.
  3. Ensure the board has completed booting (solid/breathing status LEDs).
- **Garbled characters on Hardware UART:** Ensure the terminal baud rate is set strictly to `57600` (or `115200` if custom `syslink` parameters were configured).