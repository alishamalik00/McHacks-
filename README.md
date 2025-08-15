# ECSE 324 — Lab 4: Timers and Interrupts

This lab builds upon previous ARMv7 assembly work on the Altera DE1-SoC board by introducing hardware timers, interrupts, and the ARM Generic Interrupt Controller (GIC).  
It combines pushbutton and HEX display control with timed, interrupt-driven operations.

---

## Pushbutton Interrupt Drivers

**Description:**  
Extends Lab 3’s pushbutton driver by adding:
- `enable_PB_INT_ASM` → Enables interrupt functionality for specific pushbuttons.
- `disable_PB_INT_ASM` → Disables interrupt functionality for specific pushbuttons.

Both routines accept pushbutton indices via register **R0** and set/clear the interrupt mask bits accordingly.

**Example:**  
Calling `enable_PB_INT_ASM` with `0x5` enables interrupts for PB0 and PB2.

---

## ARM A9 Private Timer Drivers

**Description:**  
Implements drivers for the DE1-SoC’s 200 MHz ARM A9 private timer:
- `ARM_TIM_config_ASM` → Configures timer load value and control bits.
- `ARM_TIM_read_INT_ASM` → Reads the timer’s interrupt status (F flag).
- `ARM_TIM_clear_INT_ASM` → Clears the F flag to acknowledge an interrupt.

**Example:**  
Using a load value corresponding to 0.25 s and enabling auto-reload creates a periodic interrupt every 0.25 seconds.

---

## Interrupt Configuration (GIC)

**Description:**  
Configures the ARM Generic Interrupt Controller to handle:
- Pushbutton interrupts (ID 73)
- ARM A9 private timer interrupts (ID 29)

The main program sets stack pointers for IRQ and SVC modes, enables IRQ handling, and registers ISRs for both devices.

**Example:**  
A pushbutton press triggers `KEY_ISR`, updating a flag in memory; the timer triggers `ARM_TIM_ISR`, incrementing a counter for HEX display updates.

---

## Rotating HEX Display with Timers & Interrupts

**Description:**  
The final deliverable combines pushbutton, slider switch, timer, and HEX display drivers into a fully interrupt-driven system.  
The HEX displays show a 6-character rotating message that:
- Moves at one of three speeds (0.3 s, 0.5 s, 1.0 s per shift)
- Can reverse direction
- Can pause/resume
- Changes based on slider switch settings (predefined messages, concatenated if multiple switches are on)
- Updates speed/direction via PB0–PB2
- Pauses/resumes via PB3

LEDs indicate the current speed or pause state.

**Example:**  
Default state: `oFF1CE` scrolls right-to-left at 0.3 s per shift.  
Pressing PB1 speeds it up to 0.1 s; toggling SW0 changes the message to `ECSE 324`.  
Pressing PB2 reverses the scroll direction.

---

## Testing & Grading

The implementation is validated against 10 automated test cases covering:
1. Basic rotation  
2. Speed changes  
3. Direction changes  
4. Single-pattern display  
5. Multi-pattern concatenation  
6. Pause/resume  
7-8. Mixed operations without pause  
9-10. Mixed operations with pause  

Passing all tests requires correct use of interrupts (no direct polling), proper flag handling, and accurate speed/timing control.
