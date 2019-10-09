The I-NUCLEO-LRWAN1 features the USI LoraWAN module WM-SG-SM-42 with preloaded AT-Command software, and can be controlled from an external host such as NUCLEO-L053 boards. You can also develop your own application based on the I-CUBE-LRWAN to control it without external host. You just need to correct the ping definition and antenna switch logic to run the EndNode Application of I-CUBE-LRWAN on the I-NUCLEO-LRWAN1.
You can follow the following steps to patch the ping definition and antenna control logic in the EndNode Application of I-CUBE-LRWAN:

[1] please download the I-CUBE-LRWAN 1.2.2 from ST web site below:

http://www.st.com/en/embedded-software/i-cube-lrwan.html


[2] unzip the downloaded "en.i-cube_lrwan_2.2.0.zip" and patch the two files below:

	a. stm32l0xx_hw_conf.h
	
	  1. download the corrected "stm32l0xx_hw_conf.h" for I-NUCLEO-LRWAN1 from the link below:
	  
	  	 https://github.com/USIWP1Module/USI_I-NUCLEO-LRWAN1/blob/master/I-CUBE-LRWAN_patchs/I-CUBE-LRWAN%201.1.5/sm42-lrwan_v1.1.5.patch
      
	  2. overwrite the file in the path below
	  
	     STM32CubeExpansion_LRWAN_V1.2.2\Projects\STM32L053R8-Nucleo\Applications\LoRa\End_Node\Core\inc\stm32l0xx_hw_conf.h

	b. sx1272mb2das.c
	
	  1. download the corrected "sx1272mb2das.c" for I-NUCLEO-LRWAN1 from the link below:
	  
	  	 https://github.com/USIWP1Module/USI_I-NUCLEO-LRWAN1/blob/master/I-CUBE-LRWAN_patchs/I-CUBE-LRWAN%201.1.5/sm42-lrwan_v1.1.5.patch
      
	  2. overwrite the file in the path below
	  
	     STM32CubeExpansion_LRWAN_V1.2.2\Drivers\BSP\SX1272MB2DAS\sx1272mb2das.c


[3] Build & Test EndNode Demo Application

    a. open EndNode Project file from the path below based on your IDE tool, and then build and flash into the I-NUCLEO_LRWAN1.

	   STM32CubeExpansion_LRWAN_V1.1.5\Projects\Multi\Applications\LoRa\End_Node\{EWARM|MDK-ARM|SW4STM32}\STM32L053R8-Nucleo\Lora. uvprojx

    b. when the EndNode application is running, debug log will print on the pin 2 of JP6 of the I-NUCLEO_LRWAN1, this pin is an UART-TX, you can monitor the log by using a UART tool with configuration 115200,N,8,1,None in default

    c. the default activation is ABP, you can change the related commissioning parameters like Activation,NSK,ASK,APPEUI,DEV-EUI... in this file below:

	   STM32CubeExpansion_LRWAN_V1.2.2\Projects\STM32L053R8-Nucleo\Applications\LoRa\End_Node\LoRaWAN\App\inc\Commissioning.h

	d. the default LoRaWAN region is EU868, you can change it by refer to the description in the file below:

	   STM32CubeExpansion_LRWAN_V1.2.2\Middlewares\Third_Party\LoRaWAN\Mac\region\Region.h

    e. the EndNode application will transmit a tx packet every 10 seconds, and the TX payload can be changed in the function send() in the file below:
	
	   STM32CubeExpansion_LRWAN_V1.2.2\Projects\STM32L053R8-Nucleo\Applications\LoRa\End_Node\LoRaWAN\App\src\main.c
	   






