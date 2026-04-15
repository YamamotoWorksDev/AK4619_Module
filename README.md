# AK4619 Module
This is a module board for prototyping digital synthesizers or mixers, featuring a 4-channel audio codec (AK4619VN) that supports up to 192 kHz.

## How to buy
### 2x2
- [Tindie(International)](https://www.tindie.com/products/yamamotoworks/ak4619vn-codec-module-2x2/)  
- [BASE(Japan)](https://miyworks.base.shop/items/131805475)

### 1x4
- [Tindie(International)](https://www.tindie.com/products/yamamotoworks/ak4619vn-codec-module-1x4/)  
- [BASE(Japan)](https://miyworks.base.shop/items/139583333)

## Specifications
- Power: 5V input, onboard 3.3V regulator (AP7361)
- CODEC IC: [AK4619VN](https://www.akm.com/global/en/products/audio/audio-codec/ak4619vn/)
  - Channels: 4in/4out
  - Sample Rate: 192kHz(max.)
  - TDM Slave (expects external BCLK/LRCLK and MCLK)
- Audio Jack: [Green Thonkiconn Stereo 3.5mm Jack Socket](https://www.thonk.co.uk/shop/thonkiconn/)
- RESET IC: [ADM811](https://www.analog.com/en/products/adm811.html)
- Power IC: [AP7361EA-33E-13](https://www.diodes.com/assets/Datasheets/AP7361EA.pdf)

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

    // System Clock Setting (example: 96kHz)
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

## Size
### 2x2
<img width="1590" height="1426" alt="ak4619_2x2_brd" src="https://github.com/user-attachments/assets/eb640620-8cd3-447e-87ba-b081af19437f" />

### 1x4
<img width="1514" height="734" alt="ak4619_1x4_brd" src="https://github.com/user-attachments/assets/de26ea99-787c-4a11-84c0-8e2f10d26057" />

## Photos
### 2x2
![2x2 top](https://github.com/user-attachments/assets/9b0fe9bc-cb60-43e0-b9de-c6aa34c6c8b7)
![2x2 bottom](https://github.com/user-attachments/assets/ee3ac4e1-c161-4f76-94a3-f9506609e3fa)

### 1x4
![1x4 top](https://github.com/user-attachments/assets/367ceadc-91bd-4c56-83e2-8c4c404966b8)
![1x4 bottom](https://github.com/user-attachments/assets/e5c5755c-530a-49de-98e3-d5158322d2c5)

## License
The hardware design files in this repository are licensed under the
**CERN Open Hardware Licence Version 2 - Permissive (CERN-OHL-P-2.0)**.

Unless otherwise stated, the following are treated as hardware design files covered by CERN-OHL-P-2.0:

- KiCad schematic files
- PCB layout files

A copy of the license text is provided in the `LICENSE` file.

