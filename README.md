# LORALINK

First LoRa link test using Arduino. Uses arduino-LoRa library. Goal is to send stuff over a distance. Future uses, various sensor networks.

## Adafruit RFM95W LoRa Radio Transceiver Breakout
Adafruit breakout board contains RFM95C which contains SX1276.

The Adafruit breakout board has pinout:
VIN - Regulated down to 3.3v, you can use 3.3v to 6v 
GND - Ground for logic and power
EN - Pulled HIGH if left unconnected. Enable/disable module. Pull LOW to cut power to radio.
SCK - SPI clock
MISO - Data from radio to MCU
MOSI - Data from MCU to radio
CS - Chip Select, also called SS somewhere. Pull low to start SPI transaction.
RST - Pulled HIGH by default, pull LOW to turn on the radio.
G0 - Radio's GPIO 0 pin, also known as IRQ pin, used for interrupt request notification, from radio to microcontroller.
G1 to G5 - Optional IO for radio.

Voltage: 3.3v
Current during +20 dBm transmit: about 100 mA
Current during active listening: about 30 mA
Crystal oscillator frequency: 32 MHz

Aproximate range, 2 km line of sight, tuned directional antennas.



HopeRF RFM95C - Low Power Long Range Transceiver Module

All HopeRF modules:

| Part number 	| Frequency band 	| Spreading factor 	| Bandwidth     	| Effective bitrate 	| Est. sensitivity 	|
|-------------	|----------------	|------------------	|---------------	|-------------------	|------------------	|
| RFM95W      	| 868/915 MHz    	| 6-12             	| 125-500 kHz   	| .293 - 37.5 kbps  	| -111 to -136 dBm 	|
| RFM96W      	| 433/470 MHz    	| 6-12             	| 62.5-500 kHz  	| .1465 - 37.5 kbps 	| -112 to -140 dBm 	|
| RFM98W      	| 169 MHz        	| 6-12             	| 31.25-125 kHz 	| 73.24 - 9375 bps  	| -118 to -143 dBm 	|

[RFM95C Datasheet, including SX127x info](https://cdn-learn.adafruit.com/assets/assets/000/031/659/original/RFM95_96_97_98W.pdf?1460518717)


Semtech SX1276/77/78/79 - Long Range Low Power Transceiver

LoRa Core, 137 to 1020 MHz transceiver.

[SX127x Datasheet](https://cdn-shop.adafruit.com/product-files/3179/sx1276_77_78_79.pdf)

## Antenna
Quarter-wave monopole antenna. We should consider velocity factor, wave propagation speed when deciding on antenna dimensions. The ground plane is the bit of copper near the base of the monopole antenna.

Copper: vf = 0.95
Vacuum: vf = 1.00

Wave velocity: v = c = 299792458 m/s

Wave frequency: f = 868 MHz

Wavelength: lambda = v/f = 299792458 / (868 * 10^6) [m]= 0.34538301612 [m] = 34.538301612 [cm]

Quarter wavelength in vacuum: 1/4 lambda * 1 = 8.634575403 [cm]

Quarter wavelength in copper: 1/4 lambda * 0.95 = 8.20284663285 [cm]

## FSK/OOK Mode transmitter power
RF_OP: Programmable steps
max +11 to +14 dBm
min -1 dBm

RF_OPH: Programmable steps
max +17 dBm
min +2 dBm

RF_OPH_MAX: High power mode, +20 dBm

Received power (dB) = transmitted power (dB) + gains (dB) − losses (dB)

## Should we use FSK or OOK?
FSK: Frequency Shift Keying
OOK: On Off Keying

FSK needs about 1.5x OOK bandwidth. OOK is very simple, while FSK requires a stable clock source.

Probability of data bit errors are different between modulation methods. 

Non-coherent FSK is 1 dB poorer than coherent OOK and FSK. Non-coherent is common in the industry. Thus, OOK is likely better on sensitivity. (Check radio transciever)

FSK is more spectrally efficient. Less sensitive to fading.
OOK is less dependent on accurate clock.

[OOK, ASK, FSK comparison](http://www.digikey.com.au/Web%20Export/Supplier%20Content/RFM_583/PDF/rfm-an-ook-ask-fsk-comparison.pdf)
[Modulation choice](https://www.silabs.com/community/wireless/proprietary/knowledge-base.entry.html/2015/06/09/modulation_choice-oUw0)

## Coding
LoRa uses cyclic error coding, but adds overhead.
Coding rates and associated overhead:
1, 2, 3, 4

1.25, 1.5, 1.75, 2

## Signal bandwidth
See table over signal bandwidth, higher bandwidth means we can use a higher data rate, reducing transmission time.

Nominal Rb (bps): from 18 to 1172 bps, for 7.8 kHz to 500 kHz

## LoRa packet structure
See 4.1.1.6 in HopeRF Datasheet, page 26

Preamble
Header
CRC
Payload
Payload CRC

Explicit vs implicit header mode: Explicit requires header to be added. Implicit can send data without a header.

