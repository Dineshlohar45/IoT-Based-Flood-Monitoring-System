# IoT-Based-Flood-Monitoring-System
#include "stm32f4xx.h"
#include "stm32f4xx_hal.h"
#include "ssd1306.h" // OLED library
#include "hcsr04.h" // Ultrasonic sensor library
#include "float_sensor.h" // Float sensor library

// Function prototypes
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_USART2_UART_Init(void);
static void MX_I2C1_Init(void);

// UART handle
UART_HandleTypeDef huart2;

// I2C handle
I2C_HandleTypeDef hi2c1;

// Flood status
char flood_status[20];

int main(void) {
  // HAL initialization
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  MX_USART2_UART_Init();
  MX_I2C1_Init();

  // Initialize OLED
  ssd1306_Init();

  // Initialize sensors
  HCSR04_Init();
  FloatSensor_Init();

  while (1) {
    // Read distance from HC-SR04
    float distance = HCSR04_ReadDistance();
    
    // Read float sensor status
    int float_status = FloatSensor_Read();

    // Determine flood status
    if (float_status == 1) {
      strcpy(flood_status, "Flood Detected");
    } else if (distance < 10.0) {
      strcpy(flood_status, "High Water Level");
    } else {
      strcpy(flood_status, "Safe");
    }

    // Display flood status on OLED
    ssd1306_Fill(SSD1306_COLOR_BLACK);
    ssd1306_SetCursor(0, 0);
    ssd1306_WriteString("Flood Status:", SSD1306_COLOR_WHITE);
    ssd1306_SetCursor(0, 20);
    ssd1306_WriteString(flood_status, SSD1306_COLOR_WHITE);
    ssd1306_UpdateScreen();

    // Transmit flood status via UART to ESP
    HAL_UART_Transmit(&huart2, (uint8_t*)flood_status, strlen(flood_status), HAL_MAX_DELAY);

    // Delay
    HAL_Delay(1000);
  }
}

void SystemClock_Config(void) {
  // Implementation of system clock configuration
}

static void MX_GPIO_Init(void) {
  // Implementation of GPIO initialization
}

static void
MX_USART2_UART_Init(void) {
  // Implementation of UART initialization
}

static void MX_I2C1_Init(void) {
  // Implementation of I2C initialization
}
