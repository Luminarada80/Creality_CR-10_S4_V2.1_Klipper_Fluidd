# Creality CR-10 S4 V2.1 Klipper Fluidd
# Raspberry Pi and KIAUH Setup Guide

## Raspberry Pi Imager v1.8.5
- **Advanced Options**: `ctrl + shift + x`
  - Set a username and password.
    - Default username: pi
    - Default passwork: raspberry
  - Configure wireless LAN:
    - Set SSID (WiFi name) and password.
    - Wireless LAN country: US.
- **Device**: Raspberry Pi 5.
- **OS**:
  - Raspberry Pi OS (Other).
    - Raspberry Pi OS Lite (32-bit).
- **Write**: Once finished writing, plug the SD card into the Raspberry Pi and power on.

---

## Set Up Raspberry Pi with KIAUH

### Resources
- [KIAUH GitHub](https://github.com/dw-0/kiauh)

### Find Desktop IP Address
1. Use `ipconfig` to find the Default Gateway IP address for your computer
2. Set Reserved IP for the device:
   - Go to **Connected Devices > Devices**.
   - Click **EDIT** next to the Raspberry Pi's Host Name.
   - Change to Reserved IP.

### Set Up Raspberry Pi SSH
1. Open Raspberry Pi configuration:
   - Run `sudo raspi-config`.
   - Navigate to `3 Interface Options > I1 SSH` -> enable.
   - Finish.
2. From the desktop:
   - Open Command Prompt: `ssh <username>@<ip_address>`.
     - Type `yes`.
     - Enter your passwod
3. Update Raspberry Pi:
   - Run: `sudo apt-get update && sudo apt-get upgrade && sudo apt-get clean`.
4. Install Git:
   - Run: `sudo apt-get install git -y`.
5. Clone KIAUH repository:
   - Run: `git clone https://github.com/th33xitus/kiauh.git`.
6. Launch KIAUH shell script:
   - Run: `./kiauh/kiauh.sh`.

---

## KIAUH Install Menu

### Install Steps
1. Type `1` to go to the install menu.
2. **Klipper**:
   - If prompted to try out KIAUH V6, select `2` (no).
   - Type `1` to install Klipper.
   - Type `1` to install the recommended Python version.
   - Type `1` to set up the number of Klipper instances.
   - Confirm with `y`.
3. **Moonraker**:
   - Type `2` to install Moonraker.
   - Confirm with `Y`.
4. **Fluidd** (Web Interface):
   - Type `4` to install Fluidd.
   - Skip recommended macros with `n` (these can be installed later).

### Access Fluidd
- In your web browser, enter the Raspberry Pi IP address.
- Connect to the Fluidd interface.

---

## Printer Setup

### Get Current Printer Settings or Firmware
- **Get Current Printer Settings**:
  - Use [Repetier](https://www.repetier.com/documentation/repetier-host/rh-installation-and-configuration/) or [Printrun](https://github.com/kliment/Printrun.git) to connect a computer to the printer.
  - Send `M503` in the terminal.
- **Firmware on the Printer**:
  - Refer to the [Marlin GitHub Build](https://github.com/MarlinFirmware/MarlinBuilds/tree/release-2.1.3-b2/builds/Creality/CR-10%20S4/CrealityV1).

---

## Setting Up Klipper

### Flashing Klipper to the Printer
1. Connect the Raspberry Pi via USB to the printer.
2. Run:
   - `cd ~/klipper/`
   - `make menuconfig`.
   - Select `Micro-controller Architecture (Atmega AVR)` and `Processor Model (atmega2560)`.
3. Determine the serial port:
   - Run: `ls /dev/serial/by-id/*`.
   - Example output: `/dev/serial/by-id/usb-FTDI_FT232R_USB_UART_A506VKUB-if00-port0`.
4. Flash Klipper:
   - Stop the Klipper service: `sudo service klipper stop`.
   - Run: `make flash FLASH_DEVICE=<serial_port>`.
   - Restart Klipper: `sudo service klipper start`.

### Configure `printer.cfg`
1. Download and edit configuration files as necessary:
   - Base config: [printer-creality-ender3max-2021.cfg](https://github.com/Klipper3d/klipper/blob/master/config/printer-creality-ender3max-2021.cfg).
   - Pins: [pins_RAMPS_CREALITY.h](https://github.com/InsanityAutomation/BLTouchDev/blob/master/Marlin/src/pins/pins_RAMPS_CREALITY.h).
2. Key sections to modify:
   - `[mcu]`: Replace `serial` with the value from `ls /dev/serial/by-id/*`.
   - `[stepper_x]`, `[stepper_y]`, `[stepper_z]`: Adjust `position_max` to match the print volume.
   - Add Fluidd configurations. Refer to [Fluidd Documentation](https://docs.fluidd.xyz/configuration/initial_setup).

### Testing and Calibration
- Follow the [Klipper Configuration Checks](https://www.klipper3d.org/Config_checks.html).
- Verify temperature, endstops, stepper motors, and extruder motor.
- Calibrate PID settings for the extruder and heated bed.

---

## Additional Configurations
- Set up filament switch sensor:
  - Add `[filament_switch_sensor]` settings.
- Configure BL-Touch:
  - Enable `[safe_z_home]`, `[bltouch]`, and `[bed_mesh]`.
  - Update offsets and mesh settings.

### Final Printer Configuration
- Creality CR-10 S4 V2.1 printer.cfg
