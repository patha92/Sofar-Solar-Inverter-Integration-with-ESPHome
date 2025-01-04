# Sofar-Solar-Inverter-Integration-with-ESPHome
## Background
Sofar Solar Inverters are meant to integrated into your WIFI with the included Wifi-Stick which sends your inverter data to their server, where you can access it. 
Aim of this project is to integrate a Sofar Solar HYD KTL Inverter only locally into a home automation system (e.g. HomeAssistant) not with the included WIFI-Stick but with ESPHome.
The project [sofar-inverter-control](https://github.com/rnorth/sofar-inverter-control/blob/main/README.md) already achieved something similar, but not for the HYD KTL inverter series. 
This project shall document the hardware, the installation and the ESPHome-Config for a working integration into HomeAssistant.
## Hardware
- ESP32 Microcontroller
- RS485-to-TTL Converter with automated flow control [Example](https://de.aliexpress.com/item/1005007010574982.html?spm=a2g0o.productlist.main.1.75e41735uLalQr&algo_pvid=2d873073-70c7-4626-b2ce-429273d6a68e&algo_exp_id=2d873073-70c7-4626-b2ce-429273d6a68e-0&pdp_ext_f=%7B%22order%22%3A%2249%22%2C%22eval%22%3A%221%22%7D&pdp_npi=4%40dis%21EUR%214.93%214.73%21%21%214.94%214.74%21%40210388c917359849865815370e151d%2112000039053008183%21sea%21AT%213910860029%21X&curPageLogUid=PgyNlj7TBPNV&utparam-url=scene%3Asearch%7Cquery_from%3A)
- 5V DIN rail power supply [Example](https://de.aliexpress.com/item/1005008280222508.html?spm=a2g0o.productlist.main.5.2e6058d8jN4Ane&algo_pvid=537ebb88-41d3-4c92-b3b1-0fc1520acba4&algo_exp_id=537ebb88-41d3-4c92-b3b1-0fc1520acba4-2&pdp_npi=4%40dis%21EUR%217.98%213.91%21%21%2158.67%2128.75%21%40211b816617359850884265850e2274%2112000044465156417%21sea%21AT%213910860029%21X&curPageLogUid=3aD5QiX9IbtE&utparam-url=scene%3Asearch%7Cquery_from%3A)

## Installation
The Microcontroller and the RS485-Converter are soldered on a dummy-PCB with the follwing pin connections:

| ESP32  | RS485-Converter |
| ------------- | ------------- |
| 3,3V  | VCC  |
| GND  | GND  |
| Tx  | Rx  |
| Rx  | TX  |

![grafik](https://github.com/user-attachments/assets/d191c488-ed57-41e9-8cd8-d0ac03a024b6)
![grafik](https://github.com/user-attachments/assets/e7484d92-ac36-435c-8305-e3574c031422)

The ESP32-Microcontroller is powered with 5V from the DIN rail power supply. The converter shares the same ground potential but is supplied with 3.3V from the ESP.

The UART0 interface is used for communication with the RS485 to TTL converter. Normally, UART connections are cross-linked (Tx->Rx and Rx->Tx), so the ESP and converter pins are cross-connected accordingly. However, this converter does not seem to follow this convention, requiring the Tx and Rx pins to be swapped in the YAML file. Consequently, Tx is assigned to GPIO3 and Rx to GPIO1.

Logging is configured via the UART1 interface, as the UART0 interface is already in use.

The inverter is connected via the multi-plug. The connection between the RS485-Converter and the inverter is wired as follows:

| RS485-Converter  | Inverter |
| ------------- | ------------- |
| RS485 Converter A+  | Inverter Pin 1 (brown)  |
| RS485 Converter B- | Inverter Pin 3 (white)  |

![grafik](https://github.com/user-attachments/assets/f0116d7c-1a4d-46f1-b8af-2cb4a43883e4)
![grafik](https://github.com/user-attachments/assets/3e2ad736-a138-4651-aab7-47275db5bc9f)

## ESPHome
ESPhome accesses the defined Modbus-Register of the inverter (defined in the ESPHome.yaml-File) and provides the data to HomeAssistant. In my file, I´ve only read some of the register of high interest for me - the register-list can be found at Sofar Solar directly and therefore the file can be customized to the individual wishes.
