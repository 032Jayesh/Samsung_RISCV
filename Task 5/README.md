<details>
<summary><b>Task 5:</b> Implementing Simple ALU using VSDSquadron Mini</summary>  
  
## **Overview**  

This project focuses on implementing a Basic Arithmetic Logic Unit (ALU) capable of performing addition, subtraction, multiplication, and division operations. Designed using a RISC-V-based SoC development kit, the ALU takes two user-provided inputs and sequentially computes the corresponding sum, difference, product, and quotient of the numbers, without requiring additional control signals.
  
## **Components Required**  
* VSDSquadron Mini  
* I2C LCD DISPLAY(16*2)
* 4*4 MATRIX KEYPAD
* Jumper Wires   
* Breadboard
* VS Code for Software Development  
* PlatformIO multi framework professional IDE  
  
## **Board being used:-**
![board](https://github.com/user-attachments/assets/0f354f5a-03d5-417c-9df4-4c5dc3ff019a)


## **Circuit Diagram**
![connections](https://github.com/user-attachments/assets/3992e617-bc43-4d72-a6ab-b7d0c9e06c90)

## Hardware connections:

### I2C LCD :

| **LCD Pin** | **Description**       | **VSD Squadron Mini Pin** | **Board Connection** |
|-------------|-----------------------|----------------------------|-----------------------|
| **GND**     | Ground                | GND                        | Common Ground         |
| **VCC**     | Power Supply (+5V)    | 5V                         | 5V Power Rail         |
| **SDA**     | Serial Data Line (I2C)| PC6                        | I2C Data (SDA)        |
| **SCL**     | Serial Clock Line (I2C)| PC5                       | I2C Clock (SCL)       |

### 4x4 Keypad :

| **Wire** | **Keypad Function** | **VSD Squadron Mini Pin** | **Description** |
|----------|----------------------|---------------------------|------------------|
| Wire 2   | Column 3            | PD1                       | Connected to Column 3 |
| Wire 3   | Column 2            | PD2                       | Connected to Column 2 |
| Wire 4   | Column 1            | PD3                       | Connected to Column 1 |
| Wire 5   | Row 4               | PD4                       | Connected to Row 4    |
| Wire 6   | Row 3               | PD6                       | Connected to Row 3    |
| Wire 7   | Row 2               | PD7                       | Connected to Row 2    |
| Wire 8   | Row 1               | PA1                       | Connected to Row 1    |

# DEMO
-
The setup demonstrates the working of the application- "Simple Aritmetic Unit".
Below are the code and video to view the working of the application.
## CODE
```
//Simple arithmetic unit

#include <ch32v00x.h>
#include <ch32v00x_gpio.h>
#include <stdio.h>
#include <stdlib.h>

// Define Keypad Pins
#define R1 GPIO_Pin_5 // PD5
#define R2 GPIO_Pin_2 // PD2
#define R3 GPIO_Pin_3 // PD3
#define R4 GPIO_Pin_4 // PD4
#define C1 GPIO_Pin_4 // PC4
#define C2 GPIO_Pin_5 // PC5
#define C3 GPIO_Pin_6 // PC6
#define C4 GPIO_Pin_7 // PC7

// LCD I2C Address
#define LCD_Address 0x27

// I2C Pins
#define SDA_PIN GPIO_Pin_1
#define SCL_PIN GPIO_Pin_2

// Function Prototypes
void GPIO_INIT(void);
char keypad_get_key(void);
void delay_ms(uint32_t ms);
void lcd_init(void);
void lcd_send_cmd(unsigned char cmd);
void lcd_send_data(unsigned char data);
void lcd_send_string(const char *str);
void i2c_start(void);
void i2c_stop(void);
void i2c_write(unsigned char data);
int get_number_from_keypad(void);

// GPIO Initialization
void GPIO_INIT(void) {
    GPIO_InitTypeDef GPIO_InitStructure;

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC | RCC_APB2Periph_GPIOD, ENABLE);

    // Initialize rows (R1-R4) as output
    GPIO_InitStructure.GPIO_Pin = R1 | R2 | R3 | R4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    // Initialize columns (C1-C4) as input pull-up
    GPIO_InitStructure.GPIO_Pin = C1 | C2 | C3 | C4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    // Initialize I2C Pins
    GPIO_InitStructure.GPIO_Pin = SDA_PIN | SCL_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    lcd_init(); // Initialize LCD
}

// Delay Function
void delay_ms(uint32_t ms) {
    while (ms--) {
        for (uint32_t i = 0; i < 4000; i++) {
            __NOP();
        }
    }
}

// Keypad Scan
char keypad_get_key(void) {
    const char keys[4][4] = {
        {'1', '2', '3', 'A'},
        {'4', '5', '6', 'B'},
        {'7', '8', '9', 'C'},
        {'*', '0', '#', 'D'}
    };

    uint16_t rows[] = {R1, R2, R3, R4};
    uint16_t cols[] = {C1, C2, C3, C4};

    for (int row = 0; row < 4; row++) {
        for (int r = 0; r < 4; r++) {
            GPIO_WriteBit(GPIOD, rows[r], Bit_SET);
        }
        GPIO_WriteBit(GPIOD, rows[row], Bit_RESET);

        for (int col = 0; col < 4; col++) {
            if (GPIO_ReadInputDataBit(GPIOC, cols[col]) == RESET) {
                delay_ms(200); // Debounce delay
                return keys[row][col];
            }
        }
    }
    return '\0';
}

// I2C Functions
void i2c_start(void) {
    GPIO_SetBits(GPIOC, SDA_PIN);
    GPIO_SetBits(GPIOC, SCL_PIN);
    delay_ms(1);
    GPIO_ResetBits(GPIOC, SDA_PIN);
    delay_ms(1);
    GPIO_ResetBits(GPIOC, SCL_PIN);
}

void i2c_stop(void) {
    GPIO_ResetBits(GPIOC, SDA_PIN);
    delay_ms(1);
    GPIO_SetBits(GPIOC, SCL_PIN);
    delay_ms(1);
    GPIO_SetBits(GPIOC, SDA_PIN);
}

void i2c_write(unsigned char data) {
    for (int i = 0; i < 8; i++) {
        if (data & 0x80) {
            GPIO_SetBits(GPIOC, SDA_PIN); // Send 1
        } else {
            GPIO_ResetBits(GPIOC, SDA_PIN); // Send 0
        }
        delay_ms(1);
        GPIO_SetBits(GPIOC, SCL_PIN); // Clock high
        delay_ms(1);
        GPIO_ResetBits(GPIOC, SCL_PIN); // Clock low
        data <<= 1;
    }
    // Acknowledge
    GPIO_SetBits(GPIOC, SDA_PIN); 
    delay_ms(1);
    GPIO_SetBits(GPIOC, SCL_PIN);
    delay_ms(1);
    GPIO_ResetBits(GPIOC, SCL_PIN);
}


// LCD Functions
void lcd_init(void) {
    delay_ms(50); // Wait for LCD power-on

    lcd_send_cmd(0x03);
    delay_ms(5);
    lcd_send_cmd(0x03);
    delay_ms(5);
    lcd_send_cmd(0x03);
    delay_ms(5);
    lcd_send_cmd(0x02); // 4-bit mode

    lcd_send_cmd(0x28); // 2 lines, 5x8 matrix
    lcd_send_cmd(0x0C); // Display ON, Cursor OFF
    lcd_send_cmd(0x06); // Entry mode: auto-increment, no shift
    lcd_send_cmd(0x01); // Clear display
    delay_ms(5);
}


void lcd_send_cmd(unsigned char cmd) {
    unsigned char cmd_u = (cmd & 0xF0);
    unsigned char cmd_l = ((cmd << 4) & 0xF0);
    i2c_start();
    i2c_write(LCD_Address << 1);
    i2c_write(cmd_u | 0x0C); // Enable=1, RS=0
    i2c_write(cmd_u | 0x08); // Enable=0
    delay_ms(2);
    i2c_write(cmd_l | 0x0C);
    i2c_write(cmd_l | 0x08);
    delay_ms(2);
    i2c_stop();
}

void lcd_send_data(unsigned char data) {
    unsigned char data_u = (data & 0xF0);
    unsigned char data_l = ((data << 4) & 0xF0);
    i2c_start();
    i2c_write(LCD_Address << 1);
    i2c_write(data_u | 0x0D); // Enable=1, RS=1
    i2c_write(data_u | 0x09); // Enable=0
    delay_ms(2);
    i2c_write(data_l | 0x0D);
    i2c_write(data_l | 0x09);
    delay_ms(2);
    i2c_stop();
}


void lcd_send_string(const char *str) {
    while (*str) {
        lcd_send_data(*str++);
    }
}

// Get Number from Keypad
int get_number_from_keypad(void) {
    char num_str[5] = {0};
    int index = 0;
    char key;

    while (1) {
        key = keypad_get_key();
        if (key >= '0' && key <= '9') {
            num_str[index++] = key;
            lcd_send_data(key);
        } else if (key == '#') {
            return atoi(num_str);
        }
    }
}

int main(void) {
    GPIO_INIT();

    int num1, num2;

    // Prompt for num1
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("Enter Num1: ");
    num1 = get_number_from_keypad();

    // Prompt for num2
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("Enter Num2: ");
    num2 = get_number_from_keypad();

    // Perform Operations
    char result[16];

    // Display Sum
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("ADDITION");            // First line: Operation
    lcd_send_cmd(0xC0);                // Move to second line
    sprintf(result, "=%d", num1 + num2);
    lcd_send_string(result);
    delay_ms(2000);

    // Display Subtraction
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("SUBTRACTION");     // First line: Operation
    lcd_send_cmd(0xC0);                // Move to second line
    sprintf(result, "=%d", num1 - num2);
    lcd_send_string(result);
    delay_ms(2000);

    // Display Multiplication
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("MULTIPLICATION");        // First line: Operation
    lcd_send_cmd(0xC0);                // Move to second line
    sprintf(result, "=%d", num1 * num2);
    lcd_send_string(result);
    delay_ms(2000);

    // Display Division
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("Division");       // First line: Operation
    lcd_send_cmd(0xC0);                // Move to second line
    if (num2 != 0)
        sprintf(result, "=%d", num1 / num2);
    else
        sprintf(result, "Div=Error");
    lcd_send_string(result);
    delay_ms(2000);

    // End of Program
    lcd_send_cmd(0x01);  // Clear screen
    lcd_send_string("Operations Done");

    while (1);
}
```



</details>
