The I-NUCLEO-LRWAN1 features the USI LoraWAN module WM-SG-SM-42 with preloaded AT-Command software, and can be controlled from an external host such as NUCLEO-L053 boards. You can also develop your own application based on the I-CUBE-LRWAN to control it without external host. You just need to correct the ping definition and antenna switch logic to run the I-CUBE-LRWAN on the I-NUCLEO-LRWAN1.
You can follow the following steps to patch the ping definition and antenna control logic in the EndNode Application of I-CUBE-LRWAN:

(1) Download the I-CUBE-LRWAN 1.1.5 from the link below:

http://www.st.com/en/embedded-software/i-cube-lrwan.html


(2) Download the patch file from the Github link below:

https://github.com/USIWP1Module/USI_I-NUCLEO-LRWAN1/blob/master/I-CUBE-LRWAN_patchs/I-CUBE-LRWAN%201.1.5/sm42-lrwan_v1.1.5.patch


(3) run the patch under the root folder of I-CUBE-LRWAN 1.1.5, the following is the example in cygwin environment:

$ cd STM32CubeExpansion_LRWAN_V1.1.5         <--- to go the root folder of I-CUBE-LRWAN 1.1.5

$ patch -p1 < ../sm42-lrwan_v1.1.5.patch     <--- assume 'sm42-lrwan_v1.1.5.patch' is located at upper directory.


(4) open EnNode Project from the path below based on your IDE tool, and then build and flash into the I-NUCLEO_LRWAN1.

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\


(5) when the EndNode application is running, debug log will print on the pin 2 of JP6 of the I-NUCLEO_LRWAN1, this pin is an UART-TX, you can monitor the log by using a UART tool with configuration 115200,N,8,1,None in default


(6) you can change the LoraWAN region by refer to the description in the file below:

STM32CubeExpansion_LRWAN_V1.1.5\Middlewares\Third_Party\Lora\Mac\region\Region.h


(7) you can change the content of TX payload in the function send() in the file below:

STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\src\main.c



