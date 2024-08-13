# Flashing ESPHome onto Shelly Plus 1PM Mini

This document outlines the process of flashing ESPHome firmware onto the Shelly Plus 1PM Mini. It highlights common issues encountered during the flashing process and suggests potential solutions, serving as a resource for troubleshooting rather than a comprehensive guide.

This documentation is specific to the older [Shelly Plus 1PM Mini](https://devices.esphome.io/devices/Shelly-Plus-1PM-Mini), which slightly differs from the [Shelly 1PM Mini Gen3](https://devices.esphome.io/devices/Shelly-1PM-Mini-Gen3) in terms of connection terminals and flash memory capacity, with the Gen3 model having improved terminals and 8MB of flash compared to the older model's 4MB.

## Getting the Firmware
To begin, I added a new ESPHome device in Home Assistant, selected ESP32-C3 as the chip type, and compiled and downloaded the `factory` firmware. This initial setup was performed using ESPHome version `2024.7.3`. Once the factory firmware is successfully flashed, the device supports over-the-air (OTA) updates to the final firmware version.

## Process of Flashing
Removing the cover of the device is straightforward; it is secured only by clips. It is important to keep track of the small separate part that covers the reset button.

I soldered thin wires to the points on the PCB as indicated in the [serial pinout diagram](https://devices.esphome.io/devices/Shelly-Plus-1PM-Mini#serial-pinout) on the ESPHome website. The board version used was `1PM Mini v0.1.3`, and its layout was identical to the one shown on the ESPHome Devices page. For a reliable connection at boot, I soldered GPIO0 and GND together. It is worth noting that connecting the 3.3V to the device does not activate any LEDs, unlike some Sonoff devices.

After connecting to a 3.3V UART adapter, I attempted to flash the device using `esphomeflasher` (version `1.3.0`), but encountered issues:
```
esptool.FatalError: Unexpected UART datecode value 0x03700000. Failed to autodetect chip type.
```

After adjusting the command line parameters, communication with the chip was established, yet another error occurred during the flashing attempt:

```
Using '/dev/ttyUSB0' as serial port.  
Connecting....  
  
Chip Info:  
- Chip Family: ESP32  
- Chip Model: ESP32D0WDQ6 (revision 0)  
- Number of Cores: 2  
- Max CPU Frequency: 80MHz  
- Has Bluetooth: YES  
- Has Embedded Flash: NO  
- Has Factory-Calibrated ADC: NO  
- MAC Address: 00:00:00:00:00:00  
Uploading stub...  
Running stub...  
Error putting ESP in stub flash mode: Invalid head of packet (0x47)
```

After ensuring the cabling was correct, I considered that `esphomeflasher` might not support the ESP32-C3 chip due to it being deprecated for some time. I then switched to the ESPHome Web method, which successfully connected to the ESP32 and completed the initial setup and firmware flashing. Although viewing logs from the ESPHome Web tool was unsuccessful, the device appeared in Home Assistant after flashing. I confirmed that OTA updates were functional, then desoldered the wires, reassembled the device, and powered it externally to update the firmware via Home Assistant.

Post-flashing, the voltage readings were incorrect, prompting me to apply a `multiply` filter to the sensor component within ESPHome to calibrate the readings accurately. The current and other sensor functionalities were verified to be precise.

