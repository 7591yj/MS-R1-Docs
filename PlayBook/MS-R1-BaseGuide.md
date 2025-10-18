# MS-R1

## Intro
MS-R1 is a ARM based MINIPC which is same architecture with your Android Phone.
Because of same architecture with Android Phone, compared to traditional x86 Android emulators, MS-R1 Docker runs Android with unparalleled compatibility.
This Guide to guide to how to run Android in Docker on MS-R1

## How To Play MS-R1



## Basic Information
### Default Username Password
default username: `mini`
default password: `mini`

### I/O Port
#### Front I/O

| Ports/Buttons | Note |
|----------|----------|
| 1xPower Button  | To Turn on/off MS-R1. Press 5sec to force shutdown  |
| 1x3.5mm Combo Jack | Standard 4 Pin Combo Jack. Ctia standard| 
| 1xUSB3.2 Gen 2| 10Gbps Type-A USB3 Port|
| 2xUSB2 | 480Mbps USB2 Type-A Port|


#### Back I/O
| Ports/Buttons | Note |
|----------|----------|
| 2xUSB2 | 480Mbps USB2 Type-A Port|
| 2xRJ45 | 10Gbps RJ45 Ethernet Port. Base On RTL8127|
| 2xType-C | Type-C full function port. USB can run 10Gbps. DP supports DP1.4. Not support USB4|
| 1xHDMI | HDMI2.0 Ports|
| 2xUSB3.2 Gen 2| 10Gbps Type-A USB3 Port|
| 1xPower IN| 5.5x2.5mm 19V Power IN|


#### Internel I/O
##### BACK SIDE
| Ports/Buttons | Note |
|----------|----------|
| 1x40PIN GPIO | Define docs TBD|
| 1xAuto Power-On Switch | ON: Auto Power on when connect power input. |
| BIOS FLASH PIN & UART1 PIN |  FLASH PIN Connect to BIOS Flash. UART1 PIN def prints on montherboard|
| 1xU.2 POWER | 12V Power For U.2 |
| 1xM.2 E-KEY | PCIE4.0x2 with USB|
| 1xM.2 M KEY | PCIE4.0x4|
| 1xTPM PIN | Reserved TPM CONN |
| 1x5V FAN CONN | 5V, For SSD FAN  |
| 1xRTC CONN |  |

##### CPU SIDE
| Ports/Buttons | Note |
|----------|----------|
| 1xUART2 | UART2 PIN def prints on montherboard  |
| 1xCPU FAN |  |
| 1xPCIE Port| PCIE4.0x8, Not support bifurication  |



