# Principle of operation
A quick description of operation principle of the code
## Cycle number detection
- Current system cycle number (0-8) is determined by the number of motor start/stops within some reasonable interval (2hours). Each time the binary_sensor reads "HIGH" (motor running) it increases cycle_number.
- After 2 hours of no activity of the motor (binary_sensor) the cycle number is reset to 0. 0 = normal operation

## Backwash speed up
- If you want to speed up the initial backwash cycle, set the backwash_speed_up switch to "ON".
- If this switch is ON AND cycle number is "1", the 3minute timer starts and after this time the button combination to advance to next cycle is pressed

## Salt draw + rinse speed up
- similar functionality as backwash speed up
- If the switch salt_rinse_speed_up is "ON" AND cycle number is "2", the 30minute timer starts and after this time the next cycle is advanced.
- After this step the controller is running autonomously as there is no need to interfere with following cycles. Additional backwash rinse + downflow rinse + brine draw look reasonably timed.

## Future improvements: TDS meter
- As a future improvement I want to install TDS (Total Dissolved Solids) meter on the waste water outlet. By measuring TDS, the salt rinse can be stopped at the exact moment the salt concentration drops to 0 (to be specific to the same TDS as it was during rinse = tap water) and only clean water is flowing to the drain. This eliminates water waste to absolute minimum, while still maintaining 100% proper rinse of the salt out of the resin.
- I am bit disappointed that this approach is not used by the manufacturers by default and they waste huge amounts of water instead using some slack timing control with 100%-200% margins ...

### Future improvements: water consumption/ water remaining...
- Add counters like "total water usage since installation, water used since regeneration, water remaining until next regeneration....
