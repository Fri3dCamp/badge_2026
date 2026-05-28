# Communicator add-on

To get your communicator working, you need to assemble it before connecting it to the badge.

## HARDWARE

### Communicator features

The communicator consists of:

- QWERTY keyboard with backlight designed by [Solder Party](https://www.solder.party/)
- RISC-V based microcontroller - [WCH CH32X035](https://www.wch-ic.com/products/CH32X035.html)
- [TDK ICS43434](https://invensense.tdk.com/products/ics-43434/) microphone
- [Analog Devices MAX98357A](https://www.analog.com/en/products/max98357a.html) DAC with amplifier
- small speaker

You can also use the keyboard as a USB keyboard.

The design and source files can be found in the [GitHub repository](https://github.com/Fri3dCamp/communicator_2026)

![blockdiagram](blockdiagram.png)

### Step by step assembly guide

#### All components neatly packed

The package you received contains everything you need to build the communicator add-on

- Communicator PCB
- Coloured cover plate
- 4 x long plastic pin
- 4 x 2mm short plastic pin
- speaker
- silicone keyboard
- 1 x 2x6 pin header with extra long pins

![Contents of the package](contents.jpg)

#### Mount the speaker

Remove the plastic backing to stick the speaker to the PCB. Solder the 2 wires to the PCB as shown in the photo below. The red wire must go to the solder pad marked with a `+`

![speaker soldered](speaker.jpg)
![speaker rear view](speaker2.jpg)

#### Solder the long pins

Place the long pins on the side with all the components. You can use another female connector (or even the badge) to keep the 2 loose pin strips neatly aligned while soldering.

![pins soldered](headers.jpg)

#### Mount the keyboard

Push the 2mm plastic pins into the pink cover as shown in the photos below. Place the silicone keyboard in it and click the whole assembly onto the communicator PCB.

![2mm pins placed, close-up](pink_spacer.jpg)
![2mm pins placed](pink_spacer_overview.jpg)
![keyboard in cover plate](pink_keyboard.jpg)
![keyboard mounted](pink_mounted.jpg)

#### Connect the communicator to the badge

Push the long plastic pins into the 4 holes that correspond to the badge. Remove the protective back plate and push the communicator into place.

![long pins placed](long_spacer.jpg)
![Communicator connected](communicator_mounted.jpg)

### Usage

The keyboard presents itself as a HID input device over USB.
With the `Fn` key you can activate special functions:

- `Fn+Backspace`: Delete
- `Fn+Up`: Page Up
- `Fn+Down`: Page Down
- `Fn+Left`: Home
- `Fn+Right`: End
- `Fn+Space`: toggle the backlight on/off
- `Fn+Right Shift`: Toggle Caps Lock

### Firmware features

The firmware sends [HID packets](https://files.microscan.com/helpfiles/ms4_help_file/ms-4_help-02-46.html) (8 bytes) over USB, I2C (address `0x40`) and UART.

The first byte indicates which modifier keys are pressed:

| Bit | Modifier Key |
| --- | ------------ |
| 0   | LEFT CTRL    |
| 1   | LEFT SHIFT   |
| 2   | LEFT ALT     |
| 3   | LEFT GUI     |
| 4   | RIGHT CTRL   |
| 5   | RIGHT SHIFT  |
| 6   | RIGHT ALT    |
| 7   | RIGHT GUI    |

The second byte is reserved; the remaining 6 bytes can each hold a [HID key code](https://gist.github.com/MightyPork/6da26e382a7ad91b5496ee55fdc73db2).

&nbsp;<br>
## SOFTWARE (FIRMWARE)

### Programming
The firmware will be flashed onto your microcontroller. However, if it does not work, you can re-flash the firmware at the `badge flash station` in the soldering area.

If you wish, you can also flash the firmware yourself using your own laptop — for example if you want to update the firmware or make your own modifications. The source files can be found in the [GitHub repository](https://github.com/Fri3dCamp/communicator_2026)

### Compiling

The firmware uses [platformio](https://platformio.org) to compile the code. Make sure to also install the [ch32v platform package](https://github.com/Community-PIO-CH32V/platform-ch32v). To compile the debug version of the firmware via the command line, type:

```
pio run -e debug
```

The firmware can then be found here: `.pio/build/debug/firmware.bin`.

To flash the firmware to your communicator, hold the button down while plugging in the USB cable to your computer. Then run:

```
pio run -e debug -t upload
```

If everything goes well, your communicator is now re-flashed with your own version of the firmware.

### I2C

As mentioned earlier, the badge can also communicate with the communicator via I2C (address ```0x40```). The following registers can be accessed to read or write data:

| Register | Name | Permissions | Bytes | Description |
|-|-|-|-|-|
| 0x00 | Version number | R | 3 | The firmware version |
| 0x03 | Current HID report packet | R | 8 | 8-byte HID report packet (see above) |
| 0x0b | Configuration | R/W | 1 | A 1-byte configuration register (see below) |
| 0x0c | Backlight | R/W | 2 | Keyboard backlight brightness (0-100) |

The configuration is a 1-byte value with the following meaning for each bit:
| Bit | Name |
|-|-|
| \[7:2\] | reserved |
| 1 | reboot into bootloader |
| 0 | enable interrupt mode (in development) |

### UART

You can also communicate with the communicator from the badge via UART. This uses UART settings 115200 8N1.

The advantage of this is that the badge simply has to listen for incoming HID packets via UART. These packets arrive automatically without the badge needing to poll.

You can also send 2 bytes from the badge to the UART of the communicator to set the backlight. The first byte must be a value between 0 and 100 (the backlight intensity), the second byte must be the binary inverse of the first byte (XOR with 0xFF).
