# STM32 as SPI-UART converter
Code used in a STM32F0308 Discovery development board to continuously receive data via SPI and forward it via UART. In my case it was used to receive NMEA messages from a GNSS receiver, specifically the NEO-M9 from U-Blox and forward them via UART.

First of all, the SPI1 interface is configured as Full Duplex Master. The parameters used for its configuration are the following:
  - Data size: 8 bits
  - First Bit: MSB
  - Clock:
        -- Prescaler: 256
        -- CPOL: Low
        -- CPHA: 1 Edge
  - GPIO Settings:
        -- SPI1_SCK: PA5
        -- SPI1_MISO: PA6
        -- SPI1_MOSI: PA7
        (in our case CS is created manually)
Then we configure the USART 2 interface as asynchronous with the following parameters:
  - Baud rate: 9600 bits/s
  - Word length: 8 bits
  - GPIO Settings:
        -- USART2_TX: PA2
        -- USART2_RX: PA3
        
It has been decided to use the HAL functions making use of interrupts, however it could also have been done via polling or DMA (more info at https://deepbluembedded.com/how-to-receive-spi-with-stm32-dma-interrupt/). The HAL_SPI_Receive_IT() function is called passing as parameters the pointer of SPI1 and the pointer of the buffer where the data will be stored. In the callback function we will send the bytes stored in the buffer via UART and we will call the HAL_SPI_Receive_IT() function again. This process is repeated cyclically.

If we wanted to send a message by SPI in addition to the above, we could save the message as a constant uint8_t and send it using the HAL_SPI_Transmit() function.
