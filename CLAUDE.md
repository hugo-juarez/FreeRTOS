# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

This project uses CMake with Ninja. Build from the project subdirectory (e.g., `001_Tasks`):

```bash
# Configure (from project directory, e.g., 001_Tasks/)
cmake --preset Debug    # or Release

# Build
cmake --build build/Debug    # or build/Release

# Clean and rebuild
cmake --build build/Debug --target clean && cmake --build build/Debug
```

The build produces an ELF file at `build/Debug/001_Tasks.elf`.

## Project Architecture

```
RTOS/
├── 001_Tasks/              # STM32 application project
│   ├── Core/Inc/           # Application headers, FreeRTOSConfig.h
│   ├── Core/Src/           # main.c, system init, interrupt handlers
│   ├── Drivers/            # CMSIS and STM32F4 HAL
│   ├── cmake/              # Toolchain files and STM32CubeMX CMake
│   ├── CMakeLists.txt      # Project build configuration
│   └── STM32F407XX_FLASH.ld  # Linker script
└── ThirdParty/
    └── FreeRTOS/           # FreeRTOS kernel (v202212.00)
```

## Target Hardware

- **MCU:** STM32F407VGTx (ARM Cortex-M4F with FPU)
- **Clock:** 168 MHz (8 MHz HSE with PLL)
- **Memory:** 1024 KB Flash, 128 KB RAM + 64 KB CCM
- **Toolchain:** gcc-arm-none-eabi

## FreeRTOS Integration

- **Port:** GCC_ARM_CM4F
- **Heap:** Implementation 4 (heap_4.c - dynamic with coalescing)
- **Heap Size:** 75 KB
- **Tick Rate:** 1000 Hz
- **Priority Levels:** 5 (0-4)
- **SysTick:** Reserved for FreeRTOS; HAL uses TIM6 for its timebase

FreeRTOS configuration is in `001_Tasks/Core/Inc/FreeRTOSConfig.h`. The ThirdParty CMake sets up the port and heap variant.

## Key Files

- `001_Tasks/Core/Src/main.c` - Application entry point, GPIO init for 4 LEDs (GPIOD 12-15) and button (GPIOA 0)
- `001_Tasks/Core/Inc/FreeRTOSConfig.h` - FreeRTOS kernel configuration
- `001_Tasks/001_Tasks.ioc` - STM32CubeMX project file (regenerate HAL code from here)
- `ThirdParty/CMakeLists.txt` - FreeRTOS port and heap configuration

## CMake Structure

The build system links three main components:
1. `stm32cubemx` - HAL configuration and include paths
2. `STM32_Drivers` - STM32F4 HAL driver library
3. `freertos_kernel` - FreeRTOS with freertos_config interface library

When adding new source files, update `001_Tasks/cmake/stm32cubemx/CMakeLists.txt`.
