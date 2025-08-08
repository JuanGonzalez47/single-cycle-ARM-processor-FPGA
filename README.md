# Single-Cycle ARMv7 Processor for Real-Time Temperature Control

## 1. Project Overview

This project is a comprehensive implementation of a 32-bit, single-cycle embedded system, featuring a custom processor inspired by the ARMv7 architecture. The entire system was designed and developed from scratch in SystemVerilog and successfully deployed on an Altera DE10-Lite FPGA.

The core of the project is a fully functional CPU that executes a custom assembly language program to perform a real-world task: reading temperature from an analog sensor, processing the data, displaying it on 7-segment displays, and controlling a fan's speed via Pulse-Width Modulation (PWM).

This project demonstrates a deep understanding of computer architecture, digital logic design, hardware-software co-design, and low-level embedded systems programming.

## 2. Key Features

- **Custom Single-Cycle ARMv7 Processor:** Designed and implemented in SystemVerilog, including datapath, control unit, and register file.
- **Extended Instruction Set:** The base instruction set was expanded to include the `BL` (Branch with Link) instruction for subroutine calls.
- **Memory-Mapped Peripherals:** The processor interfaces with external hardware by mapping their controls to specific memory addresses.
- **Real-Time Temperature Sensing:** Reads data from an external Analog-to-Digital Converter (ADC) to measure ambient temperature.
- **Proportional Fan Control:** Generates a PWM signal whose duty cycle is proportional to the measured temperature, with override control from a physical switch.
- **Data Display:** Processes and displays the temperature in Celsius, with one-decimal-point precision, on three 7-segment displays.
- **FPGA Implementation:** The complete system is synthesized and operational on a DE10-Lite board.

## 3. System Architecture and Implementation

The system is built around the custom processor, which orchestrates all operations.

### Processor Core (CPU)
The processor is a single-cycle design, meaning each instruction completes in one clock cycle. It was inspired by the ARMv7 architecture as described in "Digital Design and Computer Architecture – ARM Edition". Key components include:
- **Datapath:** Includes the ALU, register file, and multiplexers required to execute data-processing and memory-access instructions. It was modified to support the `BL` instruction by managing the Link Register (R14).
- **Control Unit:** A combinational logic block that decodes instructions and generates the necessary control signals for the datapath, memory, and peripherals.

### Peripherals and Memory-Mapped I/O
The CPU communicates with the outside world through memory-mapped I/O. Instead of dedicated I/O instructions, peripherals are treated as specific memory locations:
- **Analog-to-Digital Converter (ADC):** The temperature sensor's analog value is converted to digital and read by the CPU from address `dmem[0]`.
- **PWM Generator:** The calculated duty cycle for the fan is written by the CPU to address `dmem[5]`.
- **7-Segment Displays:** The final digits to be displayed are written to dedicated memory addresses controlling HEX0, HEX1, and HEX2.
- **Switches:** The state of physical switches (e.g., SW[0] for fan enable/disable) is read from a memory-mapped address.

## 4. Functionality and Program Flow

The system's logic is driven by an assembly language program running on the processor. The program executes a continuous loop to ensure real-time monitoring and control.

**The program flow is as follows:**

1.  **Read ADC:** The processor loads the digital value from the memory address corresponding to the ADC.
2.  **Convert to Temperature:** The raw ADC value is converted into a temperature in degrees Celsius with one-decimal precision using optimized arithmetic operations (avoiding hardware-intensive multiplication/division).
3.  **Calculate PWM Duty Cycle:**
    - If Temperature ≤ 20.0°C, PWM is set to 0% (fan off).
    - If Temperature ≥ 40.0°C, PWM is set to 100% (fan at max speed).
    - If 20.0°C < Temperature < 40.0°C, the PWM duty cycle is calculated proportionally.
4.  **Check Fan Switch:** The state of the physical switch `SW[0]` is read. If it's off, the PWM output is forced to 0, overriding the temperature logic.
5.  **Display Temperature:** The temperature value is mathematically separated into its integer and decimal parts, which are then encoded for display on the three 7-segment modules.
6.  **Repeat:** The cycle repeats indefinitely.

## 5. Technical Specifications

- **Hardware Platform:** Terasic DE10-Lite (Altera MAX 10 FPGA)
- **Development Language:** SystemVerilog
- **Processor Architecture:** 32-bit Single-Cycle (ARMv7-inspired)
- **Simulation & Verification:** Functionality was verified using CPUlator and hardware-level simulations before physical implementation.

## 6. Repository Content

This repository contains:
- `/src`: All SystemVerilog source files (`.sv`) for the processor, peripherals, and top-level module.
- `/asm`: The assembly language source code (`.s`) for the temperature control program.
- `/docs`: The final project report (PDF) with detailed explanations and diagrams.

## 7. Authors

- Juan Pablo González Blandón
- Santiago Castaño Loaiza
