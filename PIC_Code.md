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


#include "mcc_generated_files/mcc.h"
#include "mcc_generated_files/i2c2_master.h" // This should be the header file where I2C2_Read1ByteRegister is declared.
//#include "mcc_generated_files/eusart2.h"
#include "mcc_generated_files/examples/i2c2_master_example.h"
// TC74 I2C address (write operation assumed, adjust if necessary)
#define TC74_SENSOR_ADDRESS 0x4C

void flashLED() {
    IO_RE0_SetHigh(); // Turn on the LED
    __delay_ms(100); // Leave it on for 50 milliseconds
    IO_RE0_SetLow(); // Turn off the LED
}

int8_t ConvertTC74Temp(uint8_t rawData) {
    // Convert 8-bit 2's complement to integer
    return (int8_t)rawData;
}

void main(void) {
    // Initialize the system
    SYSTEM_Initialize();
    INTERRUPT_GlobalInterruptEnable();
    INTERRUPT_PeripheralInterruptEnable();

    // Make sure RC5 is set as a digital output
    IO_RE0_SetDigitalOutput();
   
    uint8_t tempData = 0;
    // Main loop
    while (1) {
        // Read temperature data from TC74 sensor
        tempData = I2C2_Read1ByteRegister(TC74_SENSOR_ADDRESS, 0x00);
        int8_t temperature = ConvertTC74Temp(tempData);

        // Print temperature to console
        printf("Temperature: %dC\n", temperature);
        
flashLED();
        // Control LED based on temperature
        if (temperature > 27) {
           // IO_RE0_SetLow();
            flashLED(); // Turn LED on and off quickly
            
        } else {
            IO_RE0_SetHigh();
           IO_RE0_SetLow(); // Ensure LED is off
        }

        // Delay to prevent constant reading/writing
        __delay_ms(1000);
    }
}
 ## Humidity Sensor Code
