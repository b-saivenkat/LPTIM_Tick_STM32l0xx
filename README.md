# STM32L0 LPTIM-Based FreeRTOS Tick (CubeMX FreeRTOS 10.2 Fix)

This repository is derived from Jeff Tenney’s LPTIM-Tick library and demonstrates its use on STM32L0 (STM32L083) with STM32CubeMX–generated FreeRTOS v10.2.x projects.  

Original source and discussion:  
https://github.com/jefftenney/LPTIM-Tick  
https://github.com/jefftenney/LPTIM-Tick/discussions/9  

## Background

The original LPTIM-Tick implementation assumes a FreeRTOS port where the RTOS tick setup function is named `vPortSetupTimerInterrupt()`. In STM32CubeMX-generated FreeRTOS v10.2 for STM32L0 (ARM_CM0), the corresponding function in `port.c` is instead named `prvSetupTimerInterrupt()`. This mismatch prevents STOP mode from working correctly when LPTIM is used as the RTOS tick source.

## Fix Implemented in This Repository

This repository resolves the issue by:

- Updating the FreeRTOS portable layer located at:  
  `Middlewares/Third_Party/FreeRTOS/Source/portable/GCC/ARM_CM0`  
- Aligning `lptimTick.c` with the STM32L0 FreeRTOS port to use  
  `prvSetupTimerInterrupt()` instead of `vPortSetupTimerInterrupt()`  
- Ensuring `port.c`, `portmacro.h`, and `lptimTick.c` all come from the same FreeRTOS version  

After these changes:

- STOP mode works correctly  
- LPTIM fully replaces SysTick as the FreeRTOS time base  
- The solution functions as expected with CubeMX FreeRTOS v10.2.x  

## Power Observation

- STOP mode current with FreeRTOS + LPTIM: ~0.3 mA  
- Bare-metal STOP mode on the same hardware: ~0.08 mA  

The additional current observed in the RTOS configuration is currently under investigation.

## Clock Configuration Note

- MSI + LSE is used and works reliably with LPTIM-based tickless idle
  
## Acknowledgements

All credit for the original LPTIM-based tickless implementation goes to Jeff Tenney. This repository documents the specific fixes required to use the library with STM32CubeMX-generated FreeRTOS 10.2 on STM32L0.
