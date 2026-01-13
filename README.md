# AK4619_Module
This board is an audio module board equipped with the AKM AK4619VN.

## Specifications
- CODEC IC: [AK4619VN](https://www.akm.com/global/en/products/audio/audio-codec/ak4619vn/)
  - Channels: 4in/4out
  - Sample Rate: 192kHz(max.)
  - TDM Slave
- Audio Jack: [Green Thonkiconn Stereo 3.5mm Jack Socket](https://www.thonk.co.uk/shop/thonkiconn/)
- RESET IC: [ADM811](https://www.analog.com/en/products/adm811.html)
- Power IC: [AP7361EA-33E-13](https://www.diodes.com/assets/Datasheets/AP7361EA.pdf)
- Power: 5Vin

## Codec Initialization(Example)
```c
void AUDIO_Init_AK4619(uint32_t hz)
{
    uint8_t sndData[1] = {0x00};

    // AK4619 HW Reset
    HAL_GPIO_WritePin(CODEC_RESET_GPIO_Port, CODEC_RESET_Pin, 0);
    HAL_Delay(10);
    HAL_GPIO_WritePin(CODEC_RESET_GPIO_Port, CODEC_RESET_Pin, 1);
    HAL_Delay(500);

    // Audio I/F format
    sndData[0] = 0xAC;  // 1 010 11 00 (TDM, 32bit, TDM128 I2S compatible, Falling, Slow)
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x01, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);

    // Reset Control
    sndData[0] = 0x10;  // 000 1 00 00
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x02, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);

    // System Clock Setting
    sndData[0] = 0x01;  // 00000 001 (96kHz)
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x03, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);

    // ADC Input Setting
    sndData[0] = 0x55;  // 01 01 01 01 (AIN1L, AIN1R, AIN4L, AIN4R)
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x0B, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);

    // DAC Input Select Setting
    // sndData[0] = 0x0E;  // 00 00 11 10 (ADC1 -> DAC1, ADC2 -> DAC2)
    sndData[0] = 0x04;  // 00 00 01 00 (SDIN2 -> DAC2, SDIN1 -> DAC1)
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x12, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);

    // Power Management
    sndData[0] = 0x37;  // 00 11 0 11 1
    HAL_I2C_Mem_Write(&hi2c3, (0b0010001 << 1), 0x00, I2C_MEMADD_SIZE_8BIT, sndData, sizeof(sndData), 10000);
}
```

## Application Example
- [JUMBLEQ](https://github.com/yamamo2shun1/JUMBLEQ)

## Photos
![DSCF6199](https://github.com/user-attachments/assets/9b0fe9bc-cb60-43e0-b9de-c6aa34c6c8b7)
![DSCF6204](https://github.com/user-attachments/assets/ee3ac4e1-c161-4f76-94a3-f9506609e3fa)

