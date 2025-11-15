# Liquid Handling Robot - FluidNC Implementation

This project migrates the liquid handling robot from the failed GRBL_ESP32 implementation to FluidNC firmware.

## Hardware Configuration
- **Board:** ESP32-S3-DevKitC-1
- **Motors:** 4-axis stepper motors (MKS Servo42C, 200 steps/rev, 200 steps/mm)
- **Control:** I2S shift registers (74HC595 cascade) with optoisolators
- **Safety:** Emergency stop system with GPIO19/21

## Setup Instructions

1. Flash FluidNC firmware to ESP32-S3 using the Web Installer
2. Upload config.yaml via the FluidNC web interface
3. Connect to WiFi network NETGEAR13
4. Access web interface at http://fluidnc.local or IP address

## Configuration
The config.yaml file contains all validated pin assignments and settings from the working verification test.

## Previous Project
See [LiquidHandlingRobot01](https://github.com/keithcausey/LiquidHandlingRobot01) for the GRBL_ESP32 failure analysis and hardware validation details.

## Original FluidNC README
This directory contains the FluidNC source code for reference. For the official FluidNC documentation, see [https://github.com/bdring/FluidNC](https://github.com/bdring/FluidNC).
