Industrial-LoRa-Based-Fail-Safe-Pump-Control-System
🚀 Overview
           This project implements a robust, long-range wireless control system for industrial fluid management. Utilizing STM32 microcontrollers and SX1276 LoRa transceivers, the system provides real-time PWM control of a pump with a heavy focus on data integrity and mechanical safety.
🛠 Tech Stack
            MCU: STM32 (ARM Cortex-M4 @ 84MHz)
            Radio: LoRa SX1276 (SPI Interface)
            Protocol: Custom 3-Byte 
            Secured FrameDevelopment: C, STM32 HAL, UART Diagnostics
🏗 System Architecture
                        The system consists of a Master Gateway (Command) and a Slave Node (Actuator). The communication is designed to survive high-EMI (Electromagnetic Interference) environments typical of factories.
🔒 Key Engineering Features
              1. Dual-Layer Data Integrity
                 To prevent "bit-flips" from accidentally triggering a pump, the system uses:
                      1.1 : Hardware CRC: Built-in LoRa payload verification.
                      1.2 : Software CRC-8: A secondary check using the Dallas/Maxim polynomial ($x^8 + x^5 + x^4 + 1$) to validate the 3-byte payload before the MCU processes the command.
              2. Communication Watchdog (Fail-Safe)
               Industrial safety is paramount. The Slave node features a Heartbeat Monitor:
                     2.1 : If no valid packet is received within 5000ms, the system triggers an emergency shutdown.
                     2.2 : PWM is forced to 0%, preventing potential overflows or dry-running during signal loss.
              3. High-Resolution PWM Control
               Leveraging the 84MHz internal clock, the system achieves precise duty cycle modulation:
                   Prescaler: 1999
                   ARR: 41999
                   This provides a stable frequency for pump drivers with a 0-100% duty cycle resolution.
              4. Real-Time RF Diagnostics
               The system reports live link health via UART, including:
                    RSSI (Received Signal Strength Indicator): To determine link distance/quality.
                    SNR (Signal-to-Noise Ratio): To verify if the signal is operating below the noise floor.
📋 Packet Structure
                    Byte        Field                          Description
                    0           Slave ID                       Unique identifier for the target pump node.
                    1           Duty Cycle                     Value from 0-100 representing pump speed.
                    2           CRC-8                          Checksum for error detection.
🚀 How to Run
          Connect the SX1276 to the STM32 via SPI.
          Set your timer clocks to 84MHz.
          Flash LORA_Transmit.c to the controller and LORA_Receive.c to the actuator.
          Monitor link health via any Serial Terminal(UART) at 115200 baud.
