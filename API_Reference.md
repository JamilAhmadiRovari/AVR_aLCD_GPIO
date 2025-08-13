## Hardware Pin & Register Definitions
In this library, it is very easy to connect the LCD pins to any desired microcontroller pins. The default pin assignments provided in the library are configured to work with the D1 LCD Shield.   

Pin Assignments by Defaults:
```c
RS -> PORTB.0
EN -> PORTB.1
DB4 -> PORTB.4
DB5 -> PORTB.5
DB6 -> PORTB.6
DB7 -> PORTB.7
BL -> PORTB.2 (Optional)
```

However, if you wish to use different pins for your specific hardware, you can simply modify the corresponding pin definitions in the **alcd.h** file.

You can change the pin assignments to any other GPIO pins by simply modifying the values of `__alcd_RS_Pin`, `__alcd_EN_Pin`, `__alcd_DB4_Pin`, `__alcd_DB5_Pin`, `__alcd_DB6_Pin`, and `__alcd_DB7_Pin` to match your hardware configuration.  


```c
/* Control Pins */
#define __alcd_RS_Config   DDRB  /**< RS pin direction register */
#define __alcd_RS_Control  PORTB /**< RS pin port register */
#define __alcd_RS_Pin      0     /**< RS pin number */

#define __alcd_EN_Config   DDRB  /**< EN pin direction register */
#define __alcd_EN_Control  PORTB /**< EN pin port register */
#define __alcd_EN_Pin      1     /**< EN pin number */

/* Data Pins */
#define __alcd_DB4_Config  DDRD  /**< DB4 direction register */
#define __alcd_DB4_Control PORTD /**< DB4 port register */
#define __alcd_DB4_Pin     4     /**< DB4 pin number */

#define __alcd_DB5_Config  DDRD  /**< DB5 direction register */
#define __alcd_DB5_Control PORTD /**< DB5 port register */
#define __alcd_DB5_Pin     5     /**< DB5 pin number */

#define __alcd_DB6_Config  DDRD  /**< DB6 direction register */
#define __alcd_DB6_Control PORTD /**< DB6 port register */
#define __alcd_DB6_Pin     6     /**< DB6 pin number */

#define __alcd_DB7_Config  DDRD  /**< DB7 direction register */
#define __alcd_DB7_Control PORTD /**< DB7 port register */
#define __alcd_DB7_Pin     7     /**< DB7 pin number */

/* Backlight */
#ifdef __alcd_useBL
    #define __alcd_BL_Config   DDRB  /**< Backlight direction register */
    #define __alcd_BL_Control  PORTB /**< Backlight port register */
    #define __alcd_BL_Pin      2     /**< Backlight pin number */
#endif
```

> [!NOTE]  
> Using the backlight is optional. If you want to utilize this feature, you must first set the macro `#define __alcd_useBL true`. Once this is set to `true`, the block for the backlight will be enabled, allowing you to define the desired pin for the backlight control.

## API Reference
To use the LCD driver, include the `alcd.h` header file in your project.  Then, use the following functions to control the LCD.

> [!NOTE]  
> The library and all of its APIs provided below have been developed by myself.  
This library utilizes various macros defined in the `aKaReZa.h` header file, which are designed to simplify bitwise operations and register manipulations.    
Detailed descriptions of these macros can be found at the following link:  
> [https://github.com/aKaReZa75/AVR/blob/main/Macros.md](https://github.com/aKaReZa75/AVR/blob/main/Macros.md)  

### Initialization
```c
void alcd_init(void);
```
 * Initializes the LCD module.
 * This function must be called before any other LCD function.
 * It configures the necessary pins and sends initialization commands to the LCD.

> [!IMPORTANT]  
Currently, this library supports only the 16x2 LCD display. If you are using a display with a different size, you may need to modify the library to support it.

Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init(); /**< Initialize the LCD */
    while(1)
    {
        /* ... your code ... */        
    }
}
```

### Display Control
```c
void alcd_clear(void);
```
 * Clear the display and return the cursor to the home position (0, 0).

```c
void alcd_display(_Bool _alcd_displayStatus);
```
 * Turn the display on or off.
 * @param `_alcd_displayStatus` TRUE to turn on, FALSE to turn off.
 
> [!NOTE]  
By default, the display is turned on. Therefore, there is no need to explicitly issue a command to enable the LCD display.

Example:
```c

#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init();
    alcd_display(true); /**< Turn on the display */
    alcd_clear(); /**< Clear the display */
    while(1)
    {
        /* ... your code ... */
    }
}
```

    
### Cursor Control
```c
void alcd_cursor(_Bool _alcd_Cursor, _Bool _alcd_Blink);
```
 * Control the cursor appearance.
 * @param `_alcd_Cursor` TRUE to show the cursor, FALSE to hide it.
 * @param `_alcd_Blink` TRUE to enable blinking, FALSE to disable it.


```c
void alcd_gotoxy(uint8_t _alcd_x_position, uint8_t _alcd_y_position);
```
 * Set the cursor position.
 * @param `_alcd_x_position` Column position (0-15).
 * @param `_alcd_y_position` Row position (0-1).
 
> [!NOTE]
By default, the cursor is off and does not blink. If you want to enable the cursor or blinking, you can use the `alcd_cursor()` function to configure the desired behavior.

Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init();
    alcd_cursor(true, false); /**< Show the cursor without blinking */
    alcd_gotoxy(5, 1); // Move cursor to column 5, row 1
    while(1)
    {
        /* ... your code ... */
    }
}

```

### Data Writing
```c
void alcd_write(uint8_t _alcd_write_value, bool _alcd_cmdData);
```
 * Sends a command or data byte to the LCD (low-level function).
 * @param `_alcd_write_value` The byte value (command or data) to send to the LCD.
 * @param `_alcd_cmdData` A boolean indicating whether the byte is a command (`false`) or data (`true`).
 * Use the pre-defined constants `__alcd_writeData` and `__alcd_writeCmd` for clarity.
 
Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init();
    alcd_write(0x41, __alcd_writeData); // Write 'A' (ASCII 0x41) to the LCD
    alcd_write(__alcd_DISPLAY_CLEAR, __alcd_writeCmd); // Clear the display using a command
    while(1)
    {
        /* ... your code ... */
    }
}
```
 
```c
void alcd_putc(char c);
```
 * Display single character at current cursor position.
 * @param c The character to display on the LCD.
 
```c
void alcd_puts(char *_alcd_str);
```
 * Write a string to the display.
 * @param `_alcd_str` Null-terminated string to display.
 
Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init();
    alcd_gotoxy(0, 0); /**< Move cursor to the first row, first column */
    alcd_putc('X'); /**< Print 'X' to the LCD */
    alcd_gotoxy(0, 1); /**< Move cursor to the first row, second column */    
    alcd_puts("Hello, World!"); /**< Print "Hello, World!" to the LCD */
    while(1)
    {
        /* ... your code ... */
    }
}
```

### Backlight Control

> [!IMPORTANT]
If the **__alcd_useBL** macro is true, the backlight can be controlled.   
In this case, the backlight pin (often pin 16 on the display, also known as LED-) should be driven through a NPN transistor to ensure proper current handling.    

```c
void alcd_backLight(bool _alcd_backLightStatus);
```
 * Control the backlight (if enabled).
 * @param `_alcd_backLightStatus` Set to true to turn the backlight on, false to turn it off.
 
Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

int main(void) 
{
    alcd_init();
    while(1)
    {
        alcd_backLight(true); /**< Turn the backlight on */
        delay_ms(500);
        alcd_backLight(false); /**< Turn the backlight off */
        delay_ms(500);        
    }
}
```

### Custom Characters
```c
 void alcd_customChar(uint8_t _alcd_CGRAMadd, const uint8_t *_alcd_CGRAMdata);
```
 * Defines a custom character and stores it in the LCD's CGRAM.
 * `_alcd_CGRAMadd`: The CGRAM address (0-7) where the character will be stored.
 * `_alcd_CGRAMdata`: A pointer to an array of 8 bytes representing the character data. Each byte represents a row of the character.
 
> [!TIP]  
> To easily and quickly create custom characters for your LCD display, you can use the following online tools:
> - [LCD Character Creator](https://maxpromer.github.io/LCD-Character-Creator/)
> - [HD44780 Custom Character Generator](https://www.quinapalus.com/hd44780udg.html)   
>
> These tools allow you to design custom characters visually and generate the corresponding byte data that can be used with the `alcd_customChar` function.

Example:
```c
#include "aKaReZa.h"
#include "alcd.h"

uint8_t heart[8] = 
{
    0b00000,
    0b01010,
    0b11111,
    0b11111,
    0b01110,
    0b00100,
    0b00000,
    0b00000
};

uint8_t smiley[8] = 
{
    0b00000,
    0b01010,
    0b01010,
    0b00000,
    0b10001,
    0b01110,
    0b00000,
    0b00000
};

int main(void) 
{
    alcd_init();
    alcd_customChar(0, heart); /**< Store the heart character at CGRAM address 0 */
    alcd_customChar(1, smiley); /**< Store the smiley character at CGRAM address 1 */    
    alcd_putc(0); /**<  Print the heart character (ASCII code 0 for CGRAM address 0) */
    alcd_gotoxy(0, 1); /**< Move cursor to the first row, second column */  
    alcd_putc(1); /**<  Print the smiley character (ASCII code 1 for CGRAM address 1) */    
    while(1)
    {
        /* ... your code ... */
    }
}
```

### **Summary**

| **Function** | **Purpose** |
|-------------|------------|
| `alcd_init()` | Initializes the LCD |
| `alcd_clear()` | Clears the LCD screen |
| `alcd_gotoxy(x, y)` | Moves cursor to (x, y) position |
| `alcd_puts("text")` | Displays a string on the LCD |
| `alcd_putc('c')` | Displays a single character |
| `alcd_cursor(true, false)` | Enables cursor without blinking |
| `alcd_display(true)` | Turns the LCD display ON |
| `alcd_customChar(pos, data)` | Stores a custom character |
| `alcd_backLight(true)` | Turns backlight ON |

## Complete Example
```c
#include "aKaReZa.h"
#include "alcd.h"

/* Main function demonstrating LCD usage */
int main(void) 
{
    alcd_init(); /**< Initialize LCD */

    alcd_clear(); /**< Clear the display */

    alcd_backLight(true); /**< Turn on the backlight (if available) */

    alcd_gotoxy(0, 0); /* Set cursor position to column 0, row 0 */

    alcd_puts("Hello, World!"); /**< Write text to the LCD */

    while (1) 
    {
        /* ... your code ... */
    }
}
```
## Important Notes
- Always call alcd_init() before using any other LCD functions.
- Verify that the pin definitions in alcd.h match your actual hardware connections.
-  Most LCD modules have a potentiometer for adjusting the contrast.  Adjust this potentiometer to achieve optimal visibility.
- Custom characters are stored in CGRAM, which has a **limit of 8 characters**, remember that CGRAM addresses are 0-7. The ASCII codes corresponding to these addresses are also 0-7.
- The aKaReZa.h library is essential to provide `delay_ms()` and bit manipulation macros (bitSet, bitClear, etc.).  Ensure that it is correctly included in your project and that the `delay_ms()` function is properly implemented for your clock frequency.  Without a correct delay function, the LCD will not initialize or operate correctly.
- The first row index is `0`, and the second row index is `1`, not `1` and `2`.

### Performance Considerations
1. Each command requires minimum 37μs execution time
2. Clear display command requires 1.52ms
3. CGRAM writes should be done during initialization
4. Backlight control may affect power consumption significantly

# 🌟 Support Me
If you found this repository useful:
- Subscribe to my [YouTube Channel](https://www.youtube.com/@aKaReZa75).
- Share this repository with others.
- Give this repository and my other repositories a star.
- Follow my [GitHub account](https://github.com/aKaReZa75).

# ✉️ Contact Me
Feel free to reach out to me through any of the following platforms:
- 📧 [Email: aKaReZa75@gmail.com](mailto:aKaReZa75@gmail.com)
- 🎥 [YouTube: @aKaReZa75](https://www.youtube.com/@aKaReZa75)
- 💼 [LinkedIn: @akareza75](https://www.linkedin.com/in/akareza75)
