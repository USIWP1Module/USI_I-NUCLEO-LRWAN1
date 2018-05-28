The I-NUCLEO-LRWAN1 features the USI LoraWAN module WM-SG-SM-42 with preloaded AT-Command software, and can be controlled from an external host such as NUCLEO-L053 boards. You can also develop your own application based on the I-CUBE-LRWAN to control it without external host. You just need to correct the ping definition and antenna switch logic to run the I-CUBE-LRWAN on the I-NUCLEO-LRWAN1.
You can follow the following steps to patch the ping definition and antenna control logic in the EndNode Application of I-CUBE-LRWAN:

[1] Download the I-CUBE-LRWAN 1.1.5 from the link below:

http://www.st.com/en/embedded-software/i-cube-lrwan.html


[2] Download the patch file from the Github link below:

https://github.com/USIWP1Module/USI_I-NUCLEO-LRWAN1/blob/master/I-CUBE-LRWAN_patchs/I-CUBE-LRWAN%201.1.5/sm42-lrwan_v1.1.5.patch


[3] run the patch under the root folder of I-CUBE-LRWAN 1.1.5, the following is the example in cygwin environment:

$ cd STM32CubeExpansion_LRWAN_V1.1.5         <--- to go the root folder of I-CUBE-LRWAN 1.1.5

$ patch -p1 < ../sm42-lrwan_v1.1.5.patch     <--- assume 'sm42-lrwan_v1.1.5.patch' is located at upper directory.

[4] Build & Test EndNode Demo Application

(4-1) open EndNode Project file from the path below based on your IDE tool, and then build and flash into the I-NUCLEO_LRWAN1.

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\{EWARM|MDK-ARM|SW4STM32}\STM32L053R8-Nucleo\Lora. uvprojx



(4-2) when the EndNode application is running, debug log will print on the pin 2 of JP6 of the I-NUCLEO_LRWAN1, this pin is an UART-TX, you can monitor the log by using a UART tool with configuration 115200,N,8,1,None in default


(4-3) you can change the End device commissioning parameters (Activation,NSK,ASK,APPEUI,DEV-EUI...) in this file below:

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\inc\Commissioning.h


(4-4) you can change the LoraWAN region by refer to the description in the file below:

STM32CubeExpansion_LRWAN_V1.1.5\Middlewares\Third_Party\Lora\Mac\region\Region.h


(4-5) the TX payload can be changed in the function send() in the file below:

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\src\main.c


[5] Build & Test AT-Slave Demo Application

(5-1) the original AT-Slave code is only for B-L072Z-LRWAN1 hardware, and this patch is for patch the code to run AT-Slave code on I-NUCLEO_LRWAN1 hardware based on the MDK-ARM IDE tool only, so please open the patched AT-Slave Project file from the path below using MDK-ARM IDE tool, and then build and flash into the I-NUCLEO_LRWAN1.

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\AT_Slave\MDK-ARM\B-L072Z-LRWAN1\Lora. uvprojx


(5-2) when the AT-Slave application is running, you can see debug log and AT command responses on the UART-TX (pin CN9-1), and also can input AT command on UART-RX (pin CN9-2), these UART configuration is 115200,N,8,1,None


(5-3) you can change the LoraWAN commissioning parameters (Activation,NSK,ASK,APPEUI,DEV-EUI...) in this file below:


STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\AT_Slave\inc\Commissioning.h


(5-4) you can change the LoraWAN region by refer to the description in the file below:

STM32CubeExpansion_LRWAN_V1.1.5\Middlewares\Third_Party\Lora\Mac\region\Region.h


(5-5) you can change the LoraWAN region by refer to the description in the file below:

AT+DCS=0    <--- disable duty cycle

AT+JOIN  <--- join gateway depended on the LoraWAN settings in 'Commissioning.h'

AT+SEND=2:00000000000000FA000000000000000A  <--- send data

AT+RECVB=?    <--- query received data if got 'rxDone' event


[6] for patch PingPong Application, just replace the file:

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\PingPong\inc\stm32l0xx_hw_conf.h

with the file from End_Node project:

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\inc\stm32l0xx_hw_conf.h



