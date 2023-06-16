# stm32-wwdg
It's a complete tutorial for using window watchdog in stm32 using LL library

## Introduction: 
The Window Watchdog (WWDG) is a hardware peripheral in STM32 microcontrollers that helps maintain system integrity by monitoring firmware execution.
It uses a down-counter that must be refreshed within a specified time window to prevent a system reset.
If the counter is not refreshed in time, the WWDG triggers a reset, providing an additional layer of fault detection and recovery in embedded systems.

## WWDG vs IWDG:
### Purpose and Behavior:
#### WWDG: 
The WWDG is designed to monitor the execution of firmware within a specific time window. It has a configurable down-counter that must be refreshed periodically to prevent a system reset. If the counter is not refreshed within the window, a reset is triggered.
#### IWDG: 
The IWDG is intended for system-level fault detection and recovery. It operates independently of the firmware and has a separate dedicated clock source. It uses a down-counter that must be regularly reloaded to avoid a system reset. If the counter reaches 0, a reset is triggered regardless of the firmware execution.

### Watchdog Configuration:
#### WWDG: 
The WWDG offers a window concept, allowing a reset to be triggered either when the counter reaches 0 or when it exits a predefined window range. It provides more flexibility in defining the watchdog behavior.
#### IWDG: 
The IWDG typically offers a simple timeout-based configuration, where a reset is triggered when the counter reaches 0. It focuses on basic system monitoring without the windowing feature.

### Clock Sources:
#### WWDG: 
The WWDG derives its clock source from the APB1 peripheral clock, allowing synchronization with other peripherals.
#### IWDG: 
The IWDG has a dedicated low-frequency clock source, typically an internal oscillator, providing independent operation from other peripherals.

### Configuration Flexibility:
#### WWDG: 
The WWDG often provides more configuration options, such as adjustable prescaler values and window ranges, allowing fine-tuning of the watchdog behavior.
#### IWDG: 
The IWDG usually has simpler configuration options, primarily involving setting the reload value to define the timeout period.

## the steps to use the WWDG with the LL library:

### Step 1: Initialization
Start by initializing the WWDG peripheral with the desired parameters. Here's an example:
```c
// Enable the WWDG clock
LL_APB1_GRP1_EnableClock(LL_APB1_GRP1_PERIPH_WWDG);

// Set the prescaler value
LL_WWDG_SetPrescaler(LL_WWDG_PRESCALER_8);

// Set the window value (maximum allowed value before a reset)
LL_WWDG_SetWindowValue(127);

// Set the counter value (initial value)
LL_WWDG_SetCounter(127);

// Enable the WWDG Early Wakeup interrupt
LL_WWDG_EnableIT_EWKUP();

// Enable the WWDG
LL_WWDG_Enable(WWDG);
```

### Step 2: Refreshing the WWDG
To prevent the WWDG from resetting the microcontroller, you need to refresh it periodically. This is done by writing a specific value to the WWDG_CR register. It should be done before the watchdog timer expires. Here's an example:
```c
// Refresh the WWDG
LL_WWDG_SetCounter(127); // Write any value within the window range (0-127)
```
### Step 3: Handling the Early Wakeup Interrupt
If the WWDG counter reaches 0, it triggers an Early Wakeup interrupt. You can handle this interrupt in your code to take appropriate actions. Here's an example of an interrupt handler:
```c
void WWDG_IRQHandler(void)
{
    if (LL_WWDG_IsActiveFlag_EWKUP())
    {
        // Early Wakeup interrupt occurred
        // Handle the situation (e.g., perform system reset)
        // ...

        // Clear the Early Wakeup interrupt flag
        LL_WWDG_ClearFlag_EWKUP();
    }
}
```
### Step 4: System Reset on WWDG Timeout (Optional)
If the WWDG counter reaches 0 and you want the system to perform a reset, you can enable the option in the WWDG control register (WWDG_CFR). Here's an example:
```c
// Enable the system reset when WWDG counter reaches 0
LL_WWDG_EnableSystemReset();
```
