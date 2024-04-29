 ## Microcontroller Code

#include "mcc_generated_files/mcc.h"

#include "mcc_generated_files/pin_manager.h"
#include "mcc_generated_files/i2c2_master.h"
#include "mcc_generated_files/interrupt_manager.h"
#include "mcc_generated_files/pin_manager.h"
#include "mcc_generated_files/examples/i2c2_master_example.h"
#include "mcc_generated_files/device_config.h"
#include "stdio.h"
#include "string.h"
#include <stdint.h>
#include <xc.h>
#include <pic18.h>

#define Temperature_Address 0x4C
#define Humidity_Address 0x27

int i=0;

void print_data(void)
{
        i++;
}

void main(void)
{
    // Initialize the device
    SYSTEM_Initialize();
    
    TMR0_SetInterruptHandler(print_data);
    
    // Enable the Global Interrupts
    INTERRUPT_GlobalInterruptEnable();

    TMR0_StartTimer(); // start timer
    
    SPI1_Open(SPI1_DEFAULT);
    
    LED3_SetHigh(); 
    
    uint8_t byteArray[] = {0b11101111, 0b11101000, 0b11101101};
    uint8_t temp = 0;
    uint8_t humidity = 0;

    while (1)
    {        
        
        humidity = HDC1000_GetHumidity();
        __delay_ms(100);
        temp = I2C2_Read1ByteRegister(Temperature_Address,0x00);
        __delay_ms(100);
        
        if (temp >= 29)
        {
            __delay_ms(50); //CW
            CSN_SetLow();
            SPI1_ExchangeByte(byteArray[2]);            
            CSN_SetHigh();
            __delay_ms(50);
            //printf("Motor Condition: Clockwise \r\n");
        }
        else if (temp <=27)
        {
            __delay_ms(50); //CCW
            CSN_SetLow();
            SPI1_ExchangeByte(byteArray[0]);            
            CSN_SetHigh();
            __delay_ms(50);
            //printf("Motor Condition: Counterclockwise \r\n");
        }
        else
        {
            __delay_ms(50); //OFF
            CSN_SetLow();
            SPI1_ExchangeByte(byteArray[1]);            
            CSN_SetHigh();
            __delay_ms(50);
            //printf("Motor Condition: Off \r\n");
        }
        
        if(humidity <= 40)
        {
            DRYLED_Toggle();
            __delay_ms(50);
        }
        else if(humidity >= 60)
        {
            WETLED_Toggle();
            __delay_ms(50);
        }
        else
        {
            WETLED_SetLow();
            DRYLED_SetLow();
            __delay_ms(50);
        }
        
        if (i>5)
        {
            printf("Temperature Sensor: %d degrees Celsius \n Humidity Sensor: %d%%\n\r", temp, humidity);
            __delay_ms(100);
            
            i=0;
        }
    }
}
