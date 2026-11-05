# FRA271_LAB2_ACTUATOR_FRAB12

```
├── DC Motor and Stepper/   # Lab 2.1 + 2.2 (MotorXploer.slx + motorxplorer.bin)
├── BLDC/                   # Lab 2.3 (BLDC_Explorer.bin + MC Workbench + โปรเจกต์)
├── docs/images/
└── waijung_18.11a.7z
```

บอร์ดที่ใช้: **NUCLEO-G474RE** (STM32G474RETx)

![NUCLEO-G474RE Pinout](docs/images/nucleo_g474_pinout.png)

> ขั้นตอนการประกอบ/ถอดเปลี่ยนมอเตอร์ และการใช้ Oscilloscope ดูใน **Manual** ของ Lab 2

## 1. การต่อสายไฟ (DC Motor / Stepper)

**DC Motor**

![เดินสาย DC Motor](docs/images/ph_dc_driver_wiring.png)

**Stepper Motor**

![ต่อสาย Stepper คู่ A/B](docs/images/ph_stepper_wiring_ab.png)

| หน้าที่ | Pin |
|---|---|
| DC motor PWM / DIR | PC7 (TIM8_CH2) / PC6 |
| Stepper STEP | PC8 (TIM20_CH3) |
| Stepper M0 / M1 / M2 | PB15 / PB14 / PB13 |
| Stepper DIR / EN | PB2 / PB1 |
| Encoder A / B | PA6 / PA7 (TIM3) |
| ADC Load cell / Current | PA0 / PA1 |
| UART (ST-Link VCP) | PA2 / PA3, 2,000,000 baud |

## 2. อัปโหลด .bin เข้า STM32

1. เสียบบอร์ด STM32 เข้าคอมพิวเตอร์ผ่านสาย USB จะมี Drive (Mass Storage) ขึ้นมาเหมือน Flash Drive

   ![STM32 Mass Storage Drive](docs/images/stm32_mass_storage_drive.png)

2. ลากไฟล์ `.bin` ไปวางใน Drive นั้น
   - DC Motor และ Stepper: `DC Motor and Stepper/motorxplorer.bin`
   - BLDC: `BLDC/BLDC_Explorer.bin`
3. บอร์ดจะกระพริบไฟแล้วรีเซ็ตตัวเองเพื่อรันโปรแกรมใหม่โดยอัตโนมัติ

> ถ้าขึ้นว่าพื้นที่ไม่พอ ให้ Upload Empty Project เข้าไปก่อน แล้ววางไฟล์ใหม่อีกรอบ
>
> ถ้าลองแล้วมีปัญหา ติดต่อพี่ TA ได้ที่ IG: **sxrx1st**

## 3. การติดตั้ง Waijung (ขั้นตอนคร่าวๆ)

1. แตกไฟล์ `waijung_18.11a.7z` ออกมาเป็นโฟลเดอร์ `waijung_18.11a`
2. เปิด MATLAB แล้ว `cd` เข้าไปที่โฟลเดอร์ที่แตกไว้
3. รันสคริปต์ติดตั้ง `install_waijung.m` (ดับเบิลคลิกหรือพิมพ์ `install_waijung` ใน Command Window)
4. รอจน MATLAB ติดตั้ง Waijung block library เสร็จ แล้ว restart MATLAB
5. ตรวจสอบว่ามี Waijung library ขึ้นใน Simulink Library Browser

## 4. การตั้งค่า Simulink (DC Motor / Stepper)

เปิด `DC Motor and Stepper/MotorXploer.slx` แล้วตั้งค่าดังนี้

1. **Host Serial Setup** — ตั้ง COM Port ให้ตรงกับ ST-Link Virtual COM Port (ดูใน Device Manager), Baud rate `2000000`, Data bits `8`, Parity `None`, Stop bit `1`
2. **Fixed-step Solver** — Type: Fixed-step, Solver: `discrete (no continuous states)`, Fixed-step size: `0.001` วินาที
3. กด **Run** เพื่อเริ่มรับข้อมูล real-time จากบอร์ด

**Data Flow**

| บอร์ด → Simulink (Host Serial Rx) | หมายเหตุ |
|---|---|
| Motor Angular Velocity | rad/s จาก Encoder |
| Load Cell (PA0) | ค่า ADC ดิบ |
| Current (PA1) | ค่า ADC ดิบ (ต้องแปลงเป็นแอมป์เอง) |
| PWM Range (echo), Stepper Frequency (echo) | ค่าที่บอร์ดรับไปจริง |

| Simulink → บอร์ด (Host Serial Tx) | ชนิด |
|---|---|
| PWM Range, Motor frequency, Stepper frequency | single |
| M0, M1, M2, DIR, EN | int16 |

## 5. BLDC

1. อัปโหลด `BLDC/BLDC_Explorer.bin` ตามข้อ 2
2. ติดตั้ง **MC Workbench** (<https://www.st.com/en/embedded-software/x-cube-mcsdk.html>) แล้วเปิด `BLDC/BLDC_Explorer.stwb6`
3. กดปุ่ม **Motor Pilot** เลือก COM Port ของ STM32G474RE แล้วกด **Connect**
4. เลื่อน Slider ปรับความเร็ว แล้วกด **Start** มอเตอร์จะค่อย ๆ ramp ขึ้นถึงค่าที่กำหนด

   ![Motor Pilot](docs/images/bldc_motor_pilot_control.png)

---

หากมีปัญหาในการทำ Lab ติดต่อพี่ TA ที่ IG: **sxrx1st**
