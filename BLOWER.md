# Blower Ladder Logic Documentation

## Overview
This documentation explains the ladder logic for the blower control system as provided. The program includes three main parts: MAIN, DIGITAL IO, and CONTROL, which are detailed below along with a flowchart for better understanding.

---

## 1. MAIN Program (LAD 2)

**Function**: The "MAIN" program serves as the central hub for the execution flow. It calls two subroutines:
- **DIGITAL IO (U:4)**: Handles the input/output logic for buttons and blower output.
- **CONTROL (U:5)**: Manages the blower's operational state based on button presses (manual or automatic control).

### Logic Flow:
- Two `JSR` (Jump to Subroutine) instructions:
  - The first jumps to **DIGITAL IO (U:4)**.
  - The second jumps to **CONTROL (U:5)**.
- After executing both subroutines, the program ends.

---

## 2. DIGITAL IO (LAD 3)

**Function**: Handles the interaction between physical input buttons (Hand, Off, Auto) and the blower output.

### Rungs:
1. **Hand Push Button Input**:
   - Monitors the "Hand PB Input" (Push Button).
   - The status of this button is tied to the memory bit `B3:0/0`.

2. **Off Push Button Input**:
   - Monitors the "Off PB Input".
   - Its state is associated with the memory bit `B3:0/1`.

3. **Auto Push Button Input**:
   - Monitors the "Auto PB Input".
   - Linked to `B3:0/2`.

4. **Blower Output**:
   - Controls the actual blower operation (Output `O:2/0`).
   - The output is activated based on conditions set in the **CONTROL** program (LAD 4).

---

## 3. CONTROL (LAD 4)

**Function**: Manages the operational state of the blower based on the inputs from the digital buttons.

### Key Points:
- **Blower State (N7:0)**:
  - `0 = OFF`
  - `1 = HAND`
  - `2 = AUTO`

### Rungs:
1. **Hand Button Logic**:
   - If the "Hand PB" (`B3:0/0`) is pressed and the current state is not HAND (`N7:0 != 1`), it:
     - Executes a **One-Shot (`ONS`)** instruction to prevent repeated actions.
     - Moves the value `1` (HAND) to the "Blower State" register (`N7:0`).

2. **Off Button Logic**:
   - If "Off PB" (`B3:0/1`) is pressed:
     - A One-Shot prevents repeated actions.
     - Moves `0` (OFF) to the "Blower State" register (`N7:0`).

3. **Auto Button Logic**:
   - If "Auto PB" (`B3:0/2`) is pressed:
     - A One-Shot triggers once to move `2` (AUTO) to the "Blower State" (`N7:0`).

4. **Blower State Control**:
   - Checks the current state of the blower (`N7:0`):
     - If `N7:0 == 1` (HAND mode), it activates the blower (`B3:0/3`).
     - If `N7:0 == 2` (AUTO mode), it sets a bit (`B3:0/7`) for AUTO operation (details of AUTO logic might depend on other conditions).

---

## Program Flow Explanation
1. The **MAIN** program starts and sequentially executes the DIGITAL IO and CONTROL subroutines.
2. In **DIGITAL IO**:
   - Inputs from buttons (Hand, Off, Auto) are read and stored in memory (`B3`).
   - The blower output (`O:2/0`) is linked to memory (`B3:0/3`).

3. In **CONTROL**:
   - Button presses update the blower state (`N7:0`) through one-shot logic.
   - The blower output is set based on the current state:
     - `N7:0 = 0`: Blower OFF.
     - `N7:0 = 1`: Blower runs in HAND mode.
     - `N7:0 = 2`: AUTO logic determines blower activation.

---

## Flowchart


![image](https://github.com/user-attachments/assets/c861c9b6-b9ba-4d06-83ba-3267ed00d8cc)


---

## Summary
- The ladder logic ensures the blower operates in three modes: OFF, HAND, or AUTO.
- Button presses trigger state changes through one-shot logic.
- The CONTROL subroutine manages the state and output based on button inputs.
