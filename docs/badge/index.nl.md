# Badge 2026 documentatie

## Aansluitingen

Wanneer je de badge vast hebt zals je zien dat er al aardig wat aansluitmogelijkheiden zijn. Hieronder in het kort een somenvatting van de aanwezige en optionele aansluitingen op de badge.

### Standaard aanwezig
- USB-C (max power draw?)
- Headset (TRRS: stereo audio + mic input)
- badge expansion connector (12 pinnen beschikbaar, IO en verschillende voedingen)
- MicroSD card 

### Optionele connectiviteit
- **[SAO](https://hackaday.io/project/175182-simple-add-ons-sao) connector**, ideaal voor uitbreidingen zoals [ToF addon](../tof)
- Een **"multimeter" aansluiting** waarbij je +3.3V, GND en een analoge ingang pin van de [WCH CH32X035](https://www.wch-ic.com/products/CH32X035.html) microcontroller ter beschikking hebt. Met wat handig rekenwerk en een stukje code kan je hier dus spanningen tot 15V mee meten of weerstandswaardes nameten. Let wel op, er zit geen enkele vorm van protectie op deze pin, iets wat bij een gewone multimeter uiteraard wel aanwezig is. Wees dus voorzichtig bij het gebruik hiervan.
- Voor de mensen die de LoRa kit hebben gekocht is er nog een **LoRa antenna aansluiting** voorzien. Hier kan je de meegeleverde spiraal antenna op solderen of de optioneel beschikbare SMA connector plaatsen. Het voordeel van die SMA connector is dat je makkelijk van antenne kan wisselen. Denk dan bijvoorbeeld aan een directionele antenne wanneer je met [foxhunting](https://en.wikipedia.org/wiki/Transmitter_hunting) bezig bent en een omnidirectionele antenna voor normaal gebruik.
- Ook de **Badge Link / Blaster connector** is wederom voorzien op de badge. Deze is compatible met de <a href="/badge_2024/blaster-2022/">Time Blaster van 2022</a> en <a href="/badge_2024/flamingo">de BFG 9000 van 2024</a>.

## Hardware
De badge bestaat deze editie niet uit 1, maar **2** microcontrollers! Naast de vertrouwde [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) Wi-Fi microcontroller van Espressif, kan je ook een kleine [CH32X035](https://www.wch-ic.com/products/CH32X035.html) chip van WCH terugvinden. Deze compacte, en vooral goedkope, microcontroller gebruiken we om het gebrek aan IO pinnen van de [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) te verhelpen. We doen dit door enkele functies die een beperkte snelheid hebben, of zeer specifiek zijn voor het bord, te verzamelen op deze microcontroller en deze via een [I²C verbinding](https://en.wikipedia.org/wiki/I2C) door te sturen naar de [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) chip. In het blokschema kan je snel zien welke functies er verbonden zijn met de [CH32X035](https://www.wch-ic.com/products/CH32X035.html) chip, deze pin nummers beginnen met **PA**, **PB** of **PC** gevolgd door een nummer. Ook de overige connecties naar de [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) kan je zien op dit blokschema, deze beginnen met **IO** gevolgd door een nummer.

![Blockdiagram van de badge hardware met ESP32-S3, CH32X035 en randapparatuur](badge_2026_blockdiagram.png)

<details>
<summary>Tekstversie van het blokdiagram (voor LLM / screenreaders)</summary>
<pre>
                                         +--------------------------+
                                         | 3.3V Power rails         |
                                         | 2 x XC6210B33            |
                                         +--------------------------+
                                            ^
                                            | PB6
                                            |
+--------------+      +-----------------+   |
| Power Switch |----->| Battery Charger |---+
+--------------+      +-----------------+
                             ^
                             |
+--------------+             | USB C
| USB C        |-------------+
+--------------+             USB ON = PC3
                             |
+--------------+             |
| Reset Button |-------------+---------------------- Reset
+--------------+                                    |
                                                    |
+--------------+                                    |
| Start Button |------------------------------------+---- IO0
+--------------+                                    |
                                                    |
+--------------+  PC15     +------------------+     |       +--------------------------------+
| Menu Button  |---------->|                  |     |       |                                |
+--------------+           |                  |     |       | ESP32-S3 based                 |
+--------------+  PB8      |                  |     |       | ESP32-S3-WROOM-1               |
| A Button     |---------->|                  |     |       | 8MB PSRAM                      |
+--------------+           |                  |     |       | 16MB Flash                     |
+--------------+  PB9      |                  |     |       |                                |
| B Button     |---------->|                  |-----+------>| Reset                          |
+--------------+           |                  |             | IO0                            |
+--------------+  PB7      |                  |             |                                |
| X Button     |---------->|                  |             | IO12 --------------------------+----> +----------------------+
+--------------+           |                  |             |                                |      | 5 x WS2812 Neopixel |
+--------------+  PB10     |                  |             | IO21 --------------------------+----> +----------------------+
| Y Button     |---------->|                  |             |                                |      +----------------------+
+--------------+           |                  |             | SCL = IO18                     |----> | sAO v1.69 Bis        |
+--------------+  PA5/6    |       WCH        |             | SDA = IO9     I2C -------------+----> +----------------------+
| Joystick     |---------->|    CH32X035      |             |                                |      +----------------------+
+--------------+           |                  |             | INT = IO21 --------------------+----> | Stemma QT connector  |
+--------------+  PB3      |                  |             |                                |      +----------------------+
| Debug LED    |---------->|                  |             | SPI = IO6,7,8 -----------------+----> +----------------------+
+--------------+           |                  |             | CS  = IO14                     |      | Accelerometer        |
+--------------+  PA0      |                  |             |                                |      | LSM6DSO              |
| Multimeter   |---------->|                  |             | SPI = IO6,7,8 -----------------+----> | I2C addr 0x6A        |
+--------------+           |                  |             | CS  = IO5                      |      +----------------------+
                           |                  |             | DC  = IO4                      |      +----------------------+
STDBY   = PB0 ------------>|                  |             | RST = PB11                     |----> | MicroSD Card         |
CHRG    = PA7 ------------>|                  |             |                                |      +----------------------+
BAT MON = PC0 ------------>|                  |             | TPI INT = IO13 ----------------+----> +----------------------+
                           |                  |             |                                |      | ST7789V              |
DCK/CL  = IO42 <-----------|                  |             | PB12 --------------------------+----> | 320x240 IPS LCD      |
DIO/SDA = IO39 <-----------|                  |             |                                |      | 262k colors          |
BOOT/INT= IO3  <-----------|                  |             | SDIN = IO16 -------------------+      +----------------------+
                           +------------------+             | SCLK = IO2                     |      +----------------------+
                                  |                         | LRCK = IO47                    |----> | Touch                |
                                  |                         | MCLK = IO17                    |      | I2C addr 0x15        |
                                  |                         |                                |      +----------------------+
+--------------------+ RST = PB1  |                         | IO1 ---------------------------+      +----------------------+
| LoRa Module        |<-----------+                         |                                |----> | Backlight            |
| Wio SX1262         |                                      +--------------------------------+      +----------------------+
| SPI   = IO6,7,8    |                                                                              +----------------------+
| CS    = IO45       |                                                                              | I2S DAC              |
| RF SW = IO46       |                                                                              | CJC4334H             |
| BUSY  = IO41       |                                                                              +----------+-----------+
| DIO1  = IO40       |                                                                                         |
+--------------------+                                                                                         v
                                                                                                      +----------------------+
+--------------------+ IO10                                                                          | Headset TRRS         |
| Badge Link         |-----------------------------------------------------------------------------> | Connector            |
+--------------------+                                                                                +----------+-----------+
                                                                                                                 ^
+--------------------+ IO38                                                                                    |
| Buzzer             |------------------------------------------------------------------------------------------+
+--------------------+                                                                               +----------------------+
                                                                                                      | Microphone Amplifier |
+--------------------+ IO11                                                                          +----------------------+
| IR Receiver        |                                                                                         ^
+--------------------+                                                                                         |
                                                                                                                IO1
+----------------------------+
| Expansion connector        |
| I2S  = IO15/16/17/47       |
| UART0= IO43/44             |
| I2C  = IO18/19             |
+----------------------------+
</pre>
</details>

## Software
De [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) draait een [op maat gemaakte](https://github.com/Fri3dCamp/badge_firmware_MicroPythonOS) versie van [MicroPythonOS](https://micropythonos.com/)

Op de extra [CH32X035](https://www.wch-ic.com/products/CH32X035.html) microcontroller, die je kan terugvinden op de badge, draait [standaard firmware](https://github.com/Fri3dCamp/badge_2026_fw) die deze microcontroller laat werken als een IO expander chip via een I²C interface.
