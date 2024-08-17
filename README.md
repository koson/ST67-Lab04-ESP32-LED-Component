# การสร้าง ESP32 LED Component

ESP-IDF รองรับการเขียนโปรแกรมในภาษา C++ ช่วยให้สามารถสร้าง component ในลักษณะ object ได้ ซึ่งจะทำให้การเขียนโปรแกรมทำได้อย่างเป็นธรรมชาติมากขึ้น

พิจารณา class LED เบื้องต้นที่มีองค์ประกอบอย่างง่าย 

```mermaid
classDiagram
class LED {
  Port_Number : int
  LED(Port_Number : int)  
  ON()
  OFF()
}
```


|element| Description|
|-------|------------|
|  Port_Number : int | กำหนดหมายเลขพอร์ตที่เชื่อมต่อกับ  LED|
|  LED(Port_Number : int) | Constructor ทำหน้าที่กำหนดค่าต่าง ๆ ในตอนสร้าง object เช่น การกำหนดทิศทางของพอร์ต มีพารามิเตอร์ 1 ตัวตือหมายเลขขาของพอร์ตที่เชื่อมต่อกับ LED|
|  ON() : void  | สั่งให้ LED ติด |
|  OFF() : void | สั่งให้ LED ดับ |


## สำดับการทดลอง
### 1. สร้าง  project ใหม่ ชื่อ LED_Test


![alt](./Slides/Picture-01.png)


![alt](./Slides/Picture-02.png)


#### เนื้อหาในไฟล์ main.c
```c 
#include <stdio.h>

void app_main(void)
{

}
```

### 2. สร้าง  Component ใหม่ ชื่อ LED

2.1 เปลี่ยนชื่อไฟล์ LED.c เป็น LED.cpp

2.2 เพิ่มการประกาศคลาสลงใน LED.h โดยให้ใส่ `#include "driver/gpio.h"` ด้วย เพื่อใช้งาน GPIO

#### LED.h

```c
#include "driver/gpio.h" 

class LED
{
    int PinNumber;
public:
    LED(int PinNumber);
    void ON();
    void OFF();
};
```
2.3 แก้ไขไฟล์ LED.cpp ให้เป็นดังนี้

#### LED.cpp
```cpp
#include <stdio.h>
#include "LED.h"

LED::LED(int Pin)
{
    PinNumber = Pin;
    gpio_set_direction((gpio_num_t)PinNumber, GPIO_MODE_OUTPUT);
}

void LED::ON()
{
    gpio_set_level((gpio_num_t)PinNumber,1);
}

void LED::OFF()
{
    gpio_set_level((gpio_num_t)PinNumber,0);
}
```

2.4 แก้ไขไฟล์ CMakeLists.txt ใน component/LED ให้เป็นดังต่อไปนี้

#### CMAkeLists.txt (ใน component/LED)

```Cmake
idf_component_register(SRCS "LED.cpp"
                    INCLUDE_DIRS "include"
                    REQUIRES driver)
```


### 3. แก้ไขไฟล์ต่าง ๆ ใน Main
3.1 เปลี่ยนชื่อไฟล์ main.c เป็น main.cpp
3.2 แก้ไขไฟล์ CMakeLists.txt ใน Main เพื่อเปลี่ยนชื่อไฟล์ main.c เป็น main.cpp

#### CMAkeLists.txt (ใน Main)
```Cmake
idf_component_register(SRCS "main.cpp"
                    INCLUDE_DIRS "."
                    REQUIRES LED)
```
3.3 แก้ไขไฟล์ main.cpp

``` cpp
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "LED.h"

extern "C" void app_main(void);

void app_main(void)
{
    LED led1(5); 
    LED led2(17); 
    while(1)
    {        
        led1.ON();
        led2.OFF();
        vTaskDelay(500/portTICK_PERIOD_MS);
        led1.OFF();
        led2.ON();
        vTaskDelay(500/portTICK_PERIOD_MS);
    }
}
```

Build และทดสอบบนบอร์ด ESP32

## บันทึกผล

1.เว็บไซร์ที่ ทำการ Commit จาก โปรแกรมบน VS code ไปบน Git Hub  https://github.com/AnchisaPhetnoi/ESP32-LED-Component.git

![image](https://github.com/user-attachments/assets/0c72ce90-250a-4d6c-a327-52c9a921ddb0)

2.รูปภาพ 
  ผลลัพธ์: จากการทดลอง คือหลอดไฟดวงที่ 1 และ ดวงที่ 2 นั้นติดและดับสลับกัน 

  รูปภาพ
  
  ![453654008_335158616223189_6564999699179168474_n](https://github.com/user-attachments/assets/cfbc3160-df68-4f72-b1d8-bd88b34f19e5)
  ![452673405_456774763843924_4732536554693144361_n](https://github.com/user-attachments/assets/34db67b6-6199-46c9-964b-a09c9949d7b3)

  วิดิโอ (ที่ลงใน tiktok ส่งเป็นลิงค์)
  https://vt.tiktok.com/ZSYoXuJWr/
  
![ภาพ](https://github.com/user-attachments/assets/8770c865-fd6d-4d91-8472-a506592d6dcf)

![ภาพ](https://github.com/user-attachments/assets/f68a2b86-6508-4f49-8f20-89ec40f34334)


















