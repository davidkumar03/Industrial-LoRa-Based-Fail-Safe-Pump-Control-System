# Industrial LoRa-Based Fail-Safe Pump Control System

## 🚀 Project Overview
This project implements a professional, long-range wireless control system for industrial fluid management. It bridges the gap between low-level firmware and high-power electrical engineering by using **STM32 microcontrollers** and **SX1276 LoRa transceivers** to manage a 12V Diaphragm Pump.

The core focus of this project is **Reliability** and **Safety**, moving beyond basic connectivity to handle real-world industrial challenges like EMI interference and communication loss.

## 🛠 Tech Stack & Hardware
* **MCU:** STM32 (ARM Cortex-M4 @ 84MHz)
* **Radio:** LoRa SX1276 (SPI Interface)
* **Power Stage:** BTS7960 (IBT-2) High-Power H-Bridge
* **Actuator:** R385 12V DC Diaphragm Pump
* **Power Supply:** 12V 20A Industrial SMPS
* **Firmware:** C (STM32 HAL)

## 🏗 System Architecture
The system operates on a Master-Slave topology with a custom-engineered 3-byte telemetry frame.

1.  **Master (Gateway):** Broadcasts encrypted/validated duty cycle commands.
2.  **Slave (Controller):** Validates commands via CRC-8 and drives the pump via TIM1 Complementary PWM.

## 🔒 High-Impact Engineering Features

### 1. Dual-Layer Data Integrity
To prevent accidental triggers in noisy environments, the system utilizes:
* **Hardware-Level CRC:** Native LoRa payload verification.
* **Software-Level CRC-8:** Custom implementation of the Dallas/Maxim polynomial ($x^8 + x^5 + x^4 + 1$) to validate the packet before execution.

### 2. Industrial Fail-Safe (Watchdog)
Designed for mechanical safety, the Slave node includes a **Communication Heartbeat Watchdog**. If a valid signal is not received within **5000ms**, the MCU automatically forces the PWM to **0%**, preventing pump dry-running or tank overflow during link failure.

### 3. Precision Motor Control
Using STM32's advanced-control timer (**TIM1**):
* **Frequency:** Optimized for 12V DC motor response using 84MHz clock.
* **Complementary PWM (CH1/CH1N):** Enables efficient H-bridge driving.
* **Dead-Time Insertion:** Hardware-level dead-time ensures MOSFET protection against shoot-through.

### 4. Link Quality Diagnostics
Real-time telemetry reported via UART for site surveys:
* **RSSI (Received Signal Strength Indicator):** Monitors raw signal power.
* **SNR (Signal-to-Noise Ratio):** Ensures the link budget is sufficient even below the noise floor.

## 📋 Custom Protocol Structure
| Byte | Field      | Description |
| :--- | :---       | :--- |
| 0    | Slave ID   | Unique node address (e.g., 0x01). |
| 1    | Duty Cycle | PWM value (0-100%). |
| 2    | CRC-8      | Integrity check byte. |

## 🔌 Hardware Setup (Simplified)
* **Control:** STM32 PA8 (CH1) -> IBT-2 RPWM | PA7 (CH1N) -> IBT-2 LPWM.
* **Power:** 12V SMPS -> IBT-2 B+/B- | IBT-2 M+/M- -> Pump.
* **Ground:** All GND pins (SMPS, IBT-2, STM32) must be tied to a common Star Ground.

## 📂 Project Structure
* `/Lora_Transmit/` : Source code for the transmitter gateway.
* `/Lora_Receive/` : Source code for the pump controller.
* `/Docs/` : Schematic diagrams and UART diagnostic logs.

---
**Developed for Industrial IoT Portfolio Applications.**
