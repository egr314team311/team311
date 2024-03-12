 ## Microcontroller Code

 ## Motor Code


#include "mcc_generated_files/mcc.h"

uint8_t byteArray[] = {0b11101110, 0b11101001, 0b11101100};
uint8_t byteValue;
uint8_t byteValue2;
uint8_t byteValue3;

void main(void)
{
    // Initialize the device
    SYSTEM_Initialize();
    SPI1_Initialize();
    EUSART2_Initialize();
    
    SPI1_Open(SPI1_DEFAULT);

    while (1)
    {
        if(EUSART2_is_tx_ready())
        {
            printf("\n");
            
            __delay_ms(50);
            CSN_SetLow();
            byteValue = SPI1_ExchangeByte(byteArray[0]);            
            CSN_SetHigh();
            __delay_ms(50);
            printf("1 Byte Value = %u Direction = %u \r \n", byteValue, CSN_GetValue());
            __delay_ms(1000);
           
            
            
            __delay_ms(50);
            CSN_SetLow();
            byteValue2 = SPI1_ExchangeByte(byteArray[1]);            
            CSN_SetHigh();
            __delay_ms(50);
            printf("2 Byte Value = %u Direction = %u \r \n", byteValue2, CSN_GetValue());
            __delay_ms(1000);

            __delay_ms(50);
            CSN_SetLow();
            byteValue3 = SPI1_ExchangeByte(byteArray[2]);            
            CSN_SetHigh();
            __delay_ms(50);
            printf("3 Byte Value = %u Direction = %u \r \n", byteValue3, CSN_GetValue());
            __delay_ms(1000);
        
         }
    }
}


 ## Temperature Sensor Code

 ## Humidity Sensor Code
