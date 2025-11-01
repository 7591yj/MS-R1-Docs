# MS-R1 GPIO Docs

## MS-R1 40Pin GPIO MAP

|Voltage|Function|Define|PIN|PIN|Define|Function|Voltage|
|---|---|---|---:|:---:|---|---|---|
|3.3V|VCC|3.3V|1|2|5V|VCC|5V|
|3.3V|I2C|SDA0|3|4|5V|VCC|5V|
|3.3V|I2C|SCL0|5|6|GND|GND|GND|
|3.3V|GPIO|GPIO71|7|8|UART3_TXD|UART|3.3V|
|GND|GND|GND|9|10|UART3_RXD|UART|3.3V|
|3.3V|PWM|PWM0|11|12|I2S4_SCK|I2S|3.3V|
|3.3V|PWM|PWM1|13|14|GND|GND|GND|
|3.3V|UART|UART5_TXD|15|16|UART5_RXD|UART|3.3V|
|3.3V|VCC|3.3V|17|18|GPIO44|GPIO|3.3V|
|3.3V|SPI|SPI2_MOSI|19|20|GND|GND|GND|
|3.3V|SPI|SPI2_MISO|21|22|GPIO45|GPIO|3.3V|
|3.3V|SPI|SPI2_CLK|23|24|SPI2_CS0|SPI|3.3V|
|GND|GND|GND|25|26|SPI2_CS1|SPI|3.3V|
|3.3V|I2C|SDA2|27|28|SCL2|I2C|3.3V|
|3.3V|GPIO|GPIO76|29|30|GND|GND|GND|
|3.3V|GPIO|GPIO78|31|32|GPIO46|GPIO|3.3V|
|3.3V|GPIO|GPIO79|33|34|GND|GND|GND|
|3.3V|I2S|I2S4_TWS|35|36|I2S4_MCLK|I2S|3.3V|
|3.3V|GPIO|GPIO80|37|38|I2S4_DATA_IN|I2S|3.3V|
|GND|GND|GND|39|40|I2S4_DATA_OUT|I2S|3.3V|

------------------------------------------------------------------------

## 40Pin GPIO Pin Detail

|Pin|Signal|Dir|Volt|Description|
|---|---|---|---|---|
|1|3.3V||3.3V|Power|
|2|5V||5V|Power|
|3|I2C0_SDA|IO|3.3V|I2C0 serial data|
|4|5V||5V|Power|
|5|I2C0_SCL|Out|3.3V|I2C0 serial clock|
|6|GND||||
|7|GPIO71|IO|3.3V|Configurable I/O|
|8|UART3_TXD|Out|3.3V|UART3 TX|
|9|GND||||
|10|UART3_RXD|In|3.3V|UART3 RX|
|11|PWM0|Out|3.3V|PWM0 output|
|12|I2S4_SCK|Out||I2S4 clock|
|13|PWM1|Out|3.3V|PWM1 output|
|14|GND||||
|15|UART5_TXD|Out|3.3V|UART5 TX|
|16|UART5_RXD|In|3.3V|UART5 RX|
|17|3.3V||3.3V|Power|
|18|GPIO44|IO|3.3V|Configurable I/O|
|19|SPI2_MOSI|Out|3.3V|SPI2 MOSI|
|20|GND||||
|21|SPI2_MISO|In|3.3V|SPI2 MISO|
|22|GPIO45|IO|3.3V|Configurable I/O|
|23|SPI2_CLK|Out|3.3V|SPI2 clock|
|24|SPI2_CS0|Out|3.3V|SPI2 CS0|
|25|GND||||
|26|SPI2_CS1|Out|3.3V|SPI2 CS1|
|27|I2C2_SDA|IO|3.3V|I2C2 data|
|28|I2C2_SCL|Out|3.3V|I2C2 clock|
|29|GPIO76|IO|3.3V|Configurable I/O|
|30|GND||||
|31|GPIO78|IO|3.3V|Configurable I/O|
|32|GPIO46|IO|3.3V|Configurable I/O|
|33|GPIO79|IO|3.3V|Configurable I/O|
|34|GND||||
|35|I2S4_TWS|Out|3.3V|I2S4 word select|
|36|I2S4_MCLK|Out|3.3V|I2S4 master clock|
|37|GPIO80|IO|3.3V|Configurable I/O|
|38|I2S4_DATA_IN|In|3.3V|I2S4 data in|
|39|GND||||
|40|I2S4_DATA_OUT|Out|3.3V|I2S4 data out|

------------------------------------------------------------------------
