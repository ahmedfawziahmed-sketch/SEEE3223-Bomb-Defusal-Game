# 💣 Bomb Defusal Game (STM32 Blue Pill)

**Course:** SEEE3223 (Embedded Systems)  
**Platform:** STM32F103C8T6 (Blue Pill)  
**Language:** C (Bare-Metal & Hardware Registers)

## 📌 Project Overview
The "Bomb Defusal Game" is an interactive, high-tension embedded systems project designed to test players under pressure. Operating on an STM32 Blue Pill using bare-metal register programming, players must decipher clues and enter the correct passcode via a matrix keypad before the hardware countdown timer hits zero. 

If the players succeed, the system disarms. If they fail—or if they hit the panic button too many times—a stepper motor activates a mechanical "detonation" prop.

## ✨ Key Features
* **Real-Time Hardware Timer:** Utilizes `TIM2` hardware interrupts for a non-blocking, perfectly accurate 1Hz countdown calibrated to the 8MHz internal clock.
* **Dual Display Output:** Real-time updates pushed to an I2C LCD (Top-Right MM:SS timer & game status) and a TM1637 4-digit 7-segment display.
* **Asynchronous Panic Button:** An external interrupt (`EXTI`) on PA8 allows players to request a hint, which dynamically deducts 10 seconds from the active timer as a penalty.
* **Audio-Visual Feedback:** Includes 4 Red LEDs flashing on the second, 4 Green LEDs for the win state, a primary buzzer for keypad input, and a secondary PB5 buzzer for a 10-second low-time warning.
* **Physical Prop Actuation:** A 28BYJ-48 Stepper Motor spins continuously upon game-over to trigger physical escape-room-style props.

---

## 🛠️ Hardware Components
* **Microcontroller:** STM32F103C8T6 (Blue Pill)
* **Input:** 4x4 Matrix Keypad (8 active pins)
* **Displays:** 16x2 I2C LCD Display, TM1637 4-Digit 7-Segment Display
* **Actuator:** 28BYJ-48 Stepper Motor + ULN2003 Driver Board
* **Audio/Visual:** 2x Buzzers (Active/Passive), 4x Red LEDs, 4x Green LEDs
* **Power:** 5V/9V External Power Supply for Stepper Motor (Common Ground with STM32)

---

## 🔌 Pin Mapping & Wiring

| Component | STM32 Pin | Notes |
| :--- | :--- | :--- |
| **I2C LCD** | `PB6` (SCL), `PB7` (SDA) | Configured as Open-Drain |
| **Panic Button** | `PA8` | Configured as EXTI (Interrupt) |
| **Red LEDs (Timer)** | `PB8`, `PB9`, `PB10`, `PB11` | Flashes every second |
| **Green LEDs (Win)** | `PA9`, `PA10`, `PA11`, `PA12` | Indicates defused state |
| **10-Sec Buzzer** | `PB5` | Pulses when time <= 10s |
| **Keypad Buzzer** | `PC13` | Beeps on key press / Wrong code |
| **Stepper Motor** | `PB12`, `PB13`, `PB14`, `PB15` | Connects to ULN2003 IN1-IN4 |

> **Note on Keypad Wiring:** If using a 10-pin keypad connector, the two outermost pins (Pin 1 and Pin 10) are typically dummy/backlight pins. Connect STM32 row/column pins strictly to Keypad Pins 2 through 9.

> **Note on Stepper Motor Power:** Do **not** power the stepper motor directly from the STM32 5V pin. Use an external supply and ensure the external `GND` is tied to the STM32 `GND`.

---

## 🎮 How to Play
1. **Power On:** The system boots, initializes GPIOs and Clocks, and forces all LEDs off. The LCD displays `DEFUSE THE BOMB!`.
2. **The Countdown:** You have 60 seconds. The time ticks down on both displays while the Red LEDs flash. 
3. **The Panic Button:** Stuck? Press the Panic Button to receive a clue on the LCD. *Warning: This immediately deducts 10 seconds from your remaining time!*
4. **Entering Codes:** * Press `0-9` to enter your defusal code.
   * Press `*` to clear your current input.
   * Press `#` to submit the code.
5. **Win/Loss Conditions:**
   * **Win:** Submitting the correct code halts the timer, turns on all Green LEDs, and displays `SAFE!`.
   * **Loss:** Submitting a wrong code deducts 5 seconds. If the timer reaches `00:00`, the LCD displays `BOOM!`, and the stepper motor permanently engages.

---

## 💻 Software Architecture Highlights
* **Super Loop + Interrupts:** The main `while(1)` loop handles non-critical display updates and keypad scanning, while `TIM2_IRQHandler` strictly manages the countdown logic in the background.
* **Bare-Metal Register Configuration:** Skipping heavy HAL libraries, this project relies on direct memory access to `RCC`, `GPIOA`, `GPIOB`, and `TIM2` registers for highly efficient code execution.
* **Clock Calibration:** Delay functions (`Delay_ms`, `Delay_us`) and Timer prescalers (`PSC = 7999`, `ARR = 999`) are precisely calibrated for the STM32's default **8 MHz** internal oscillator.

## 👥 Contributors
* **[Your Name]** * **[Group Member 2]**
* **[Group Member 3]**
*(Group assignment for SEEE3223)*
