# Badge 2026 documentation

## Connectors

When you hold the badge you will notice it already has quite a few connection options. Below is a brief summary of the available and optional connectors on the badge.

### Standard

- USB-C (max power draw?)
- Headset (TRRS: stereo audio + mic input)
- Badge expansion connector (12 pins available, IO and various power rails)
- MicroSD card

### Optional

- **[SAO](https://hackaday.io/project/175182-simple-add-ons-sao) connector**, ideal for add-ons such as the [ToF addon](../tof)
- A **"multimeter" connector** that exposes +3.3V, GND and an analogue input pin of the [WCH CH32X035](https://www.wch-ic.com/products/CH32X035.html) microcontroller. With some clever maths and a small piece of code you can use this to measure voltages up to 15V or measure resistance values. Note that there is no protection whatsoever on this pin, which a real multimeter obviously does have. So be careful when using it.
- For those who bought the LoRa kit, a **LoRa antenna connector** has been provided. You can solder the included spiral antenna here or fit the optionally available SMA connector. The advantage of the SMA connector is that you can easily swap antennas — for example a directional antenna when doing [foxhunting](https://en.wikipedia.org/wiki/Transmitter_hunting) and an omnidirectional antenna for normal use.
- The **Badge Link / Blaster connector** is once again present on the badge. It is compatible with the <a href="/badge_2024/blaster-2022/">Time Blaster from 2022</a> and <a href="/badge_2024/flamingo">the BFG 9000 from 2024</a>.

## Hardware

This edition the badge is made up of not 1, but **2** microcontrollers! Alongside the familiar [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) Wi-Fi microcontroller from Espressif, you will also find a small [CH32X035](https://www.wch-ic.com/products/CH32X035.html) chip from WCH. This compact, and above all cheap, microcontroller is used to compensate for the limited number of IO pins on the [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3). We do this by offloading a number of functions that have limited speed requirements, or are very specific to the board, to this microcontroller and forwarding them to the [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) via an [I²C connection](https://en.wikipedia.org/wiki/I2C). In the block diagram you can quickly see which functions are connected to the [CH32X035](https://www.wch-ic.com/products/CH32X035.html) chip — these pin numbers start with **PA**, **PB** or **PC** followed by a number. The other connections to the [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) are also visible in this block diagram, starting with **IO** followed by a number.

![Block diagram](badge_2026_blockdiagram.png)

## Software

The [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) runs [MicroPythonOS](https://micropythonos.com/) by default.

The additional [CH32X035](https://www.wch-ic.com/products/CH32X035.html) microcontroller found on the badge runs [default firmware](https://github.com/Fri3dCamp/badge_2026_fw) that makes this microcontroller act as an IO expander chip via an I²C interface.
