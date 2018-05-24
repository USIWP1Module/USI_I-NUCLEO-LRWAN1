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


(5) when the EndNode application is running, debug log will print on the pin 2 of JP6 of the I-NUCLEO_LRWAN1, this pin is an UART-TX, you can monitor the log by using a UART tool with configuration 115200,N,8,1,None   



a. For AT_Master project:
en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.0\Projects\Multi\Applications\LoRa\AT_Master\inc\stm32l0xx_hw_conf.h
b. For End_Node project:
en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Projects\Multi\Applications\LoRa\End_Node\inc\stm32l0xx_hw_conf.h
c. For PingPong project:
en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Projects\Multi\Applications\LoRa\PingPong\inc\stm32l0xx_hw_conf.h
(4) Correct the antenna switch logic for I-NUCLEO-LRWAN1
// 1. Replace SX1272IoInit() in sx1272mb2das.c with the code below.
void SX1272IoInit( void ) {
GPIO_InitTypeDef initStruct={0};

SX1272BoardInit( &BoardCallbacks );


initStruct.Mode = GPIO_MODE_IT_RISING;

initStruct.Pull = GPIO_PULLDOWN;

initStruct.Speed = GPIO_SPEED_HIGH;

HW_GPIO_Init( RADIO_DIO_0_PORT, RADIO_DIO_0_PIN, &initStruct );

HW_GPIO_Init( RADIO_DIO_1_PORT, RADIO_DIO_1_PIN, &initStruct );

HW_GPIO_Init( RADIO_DIO_2_PORT, RADIO_DIO_2_PIN, &initStruct );

HW_GPIO_Init( RADIO_DIO_3_PORT, RADIO_DIO_3_PIN, &initStruct );

/* Initialize I-NUCLEO-LRWAN1 Antenna IO */

initStruct.Mode =GPIO_MODE_OUTPUT_PP;

initStruct.Pull = GPIO_NOPULL;

initStruct.Speed = GPIO_SPEED_HIGH;

HW_GPIO_Init( RADIO_ANT_SWITCH_PORT, RADIO_ANT_SWITCH_PIN, &initStruct );

HW_GPIO_Init( RADIO_ANT2_SWITCH_PORT, RADIO_ANT2_SWITCH_PIN, &initStruct );
}
// 2. Replace SX1272SetAntSw() in sx1272mb2das.c with the code below.
/*
•	Andy: SM42 uses PA_BOOST for RF TX and RFI for RF RX, TX RX is controlled by a SPDT switch by the following control logic:
•	
•	  ANT | ANT2
•	
•	---+-----+------
•	TX | H | L
•	RX | L | H
•	
*/ void SX1272SetAntSw( uint8_t opMode ) {
switch( opMode ) {

case RFLR_OPMODE_TRANSMITTER:

	SX1272.RxTx = 1;
	/* Switch the antenna of I-NUCLEO-LRWAN1 in TX mode */
	HW_GPIO_Write( RADIO_ANT_SWITCH_PORT, RADIO_ANT_SWITCH_PIN, 1);
	HW_GPIO_Write( RADIO_ANT2_SWITCH_PORT, RADIO_ANT2_SWITCH_PIN, 0);
	break;

case RFLR_OPMODE_RECEIVER:
case RFLR_OPMODE_RECEIVER_SINGLE:
case RFLR_OPMODE_CAD:
default:
	
	SX1272.RxTx = 0;
	/* Switch the antenna of I-NUCLEO-LRWAN1 in RX mode */
	HW_GPIO_Write( RADIO_ANT_SWITCH_PORT, RADIO_ANT_SWITCH_PIN, 0);
	HW_GPIO_Write( RADIO_ANT2_SWITCH_PORT, RADIO_ANT2_SWITCH_PIN, 1);
	break;
}
}
(5) since SM42 uses PA_BOOST for the RF TX, please change the RFLR_PACONFIG_PASELECT_RFO value from 0x00 to 0x80 in the header files below: 'en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Drivers\BSP\Components\sx1272\sx1272Regs-Fsk.h' 'en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Drivers\BSP\Components\sx1272\sx1272Regs-Lora.h'
#define RFLR_PACONFIG_PASELECT_RFO 0x80 // to enable PA_BOOTST for TX
(6) Patch is done at this step, you can make AT_Master, End_Node or PingPong project code for I-NUCLEO-LRWAN.
(7) You can test the LoraWAN connectivity with Semtech sx1301 gateway starter-kit using End_Node project code, and just need to correct the configurations below:
a. in 'en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Projects\Multi\Applications\LoRa{AT_Master|AT_Slave|End_Node|PingPong}\inc\Comissioning.h':
// correct the definition below depended on your sx1301 gateway:
OVER_THE_AIR_ACTIVATION LORAWAN_APPLICATION_EUI LORAWAN_APPLICATION_KEY LORAWAN_DEVICE_ADDRESS LORAWAN_NWKSKEY LORAWAN_APPSKEY
b. in 'en.i-cube_lrwan\STM32CubeExpansion_LRWAN_V1.1.4\Middlewares\Third_Party\Lora\Core\lora.c':
#define LORAWAN_DUTYCYCLE_ON false // to enabling to update node data every 10 seconds to gateway #define USE_SEMTECH_DEFAULT_CHANNEL_LINEUP 1 // let the node uses DR3 on RX2

