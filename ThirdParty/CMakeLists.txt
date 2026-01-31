# Using FreeRTOS Kernel
# Set the port BEFORE adding the FreeRTOS subdirectory
set(FREERTOS_PORT GCC_ARM_CM4F CACHE STRING "")  # Adjust for your MCU

# Set heap implementation (1-5, or path to custom)
set(FREERTOS_HEAP 4 CACHE STRING "")

# Create the freertos_config target (required by FreeRTOS CMake)
add_library(freertos_config INTERFACE)
target_include_directories(freertos_config
        INTERFACE
        ${PROJECT_SOURCE_DIR}/Core/Inc  # Where your FreeRTOSConfig.h lives
)

# Add FreeRTOS kernel as subdirectory
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/FreeRTOS FreeRTOS-Kernel)

# Include SYSVIEW include files needed for FreeRTOSConfig.h
target_include_directories(freertos_config INTERFACE
    ${CMAKE_CURRENT_SOURCE_DIR}/SEGGER/Config
    ${CMAKE_CURRENT_SOURCE_DIR}/SEGGER/OS
    ${CMAKE_CURRENT_SOURCE_DIR}/SEGGER/SEGGER
)

# Add SEGGER RTT and SystemView
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/SEGGER SEGGER)

target_compile_options(freertos_kernel PRIVATE
        ### Gnu/Clang C Options
        $<$<COMPILE_LANG_AND_ID:C,GNU>:-fdiagnostics-color=always>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-fcolor-diagnostics>

        $<$<COMPILE_LANG_AND_ID:C,Clang,GNU>:-Wall>
        $<$<COMPILE_LANG_AND_ID:C,Clang,GNU>:-Wextra>
        $<$<COMPILE_LANG_AND_ID:C,Clang,GNU>:-Wpedantic>
        $<$<COMPILE_LANG_AND_ID:C,Clang,GNU>:-Werror>
        $<$<COMPILE_LANG_AND_ID:C,Clang,GNU>:-Wconversion>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Weverything>

        # Suppressions required to build clean with clang.
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Wno-unused-macros>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Wno-padded>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Wno-missing-variable-declarations>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Wno-covered-switch-default>
        $<$<COMPILE_LANG_AND_ID:C,Clang>:-Wno-cast-align> )