# Software Debouncing
Note:  The following explantion will include the SoftwareDebouncing functionality for all processors since the logic does not vary between the MSP430s.

## Explanation
Instead of using a for loop to handle button bouncing, this section implemented  method to handle bouncing using the built in MSP430 timers. The code that was written started a timer 
and turned the LEDS on/off every time that a button was pressed. The main purpose of the timer was to hold the processor for 32ms. This delay, allowed the buttons to debounce correctly without
effecting the LEDs in a way that was not intended. In the PortX interrupt, logic was written to actually disable the interrupt until the the delay was over. This allowed the processor to disregard 
the PortX interrupt entirely and allow the delay to run uninterrupt. The PortX interrupt was disabled by using the PXIE register which essentially is what lets the PortX interrupt trigger.
Once this was turned off the PortX interrupt would no longer trigger until the PxIE was reenabled in the Timer interrupt. Within the Timer interrupt the timer was halted and cleared so 
every time a button was pressed a new delay would start. It is also important to note that within the PortX interrupt the PXIES register was also flipped to account for button bouncing 
when the button is pressed and depressed.

##Extra Work
Using the MSP430fr5994 an extra button was debounced. On this processor the two buttons are on the same port so the Port5 interrupt had to handle both of these flags. This was done by
using the P5IFG register to determine which button was actually pressed. Conditonal statements were used to determine if P5IFG raised a flag because of P5.6 or P5.5. Once this logic had been handled.
the functoinality basically mirrored the code that was written for just one button. The main difference was that another timer (TimerB) was added for P5.6. The reason for this was because
if only one timer was used each button could affect the delay of the other by triggering before the delay finished. By adding another timer that allowed each button to have independent delays that
could not be affected by the other.