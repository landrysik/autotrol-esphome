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
- If the switch salt_rinse_speed_up is "ON" AND cycle number is "2", the 40 minute timer starts and after this time the next cycle is advanced.
- After this step the controller is running autonomously as there is no need to interfere with following cycles. Additional backwash rinse + downflow rinse + brine draw look reasonably timed.
> [!NOTE]
> Those 40 minutes should be adjusted accordingly to the amount of resin, amount of salt used and water pressure with some reasonable extra time to be sure all of the salt is flushed.
> In "extreme cases" additional water savings can be achieved by speeding up the next cycle (C5: Downflow Fast Rinse) which takes 4 minutes by default OR lowering the salt rinse timing. This fast rinse will flush any remaining salt the same way (downflow) so there is maybe no need to flush 100% of the salt in slow salt rinse phase.
> Just in case you are interested about the rest of the regeneration cycle, it follows with C6 (Upflow Backwash) at 1minute, C7 (Downflow Fast Rinse) at 1minute and finally it refills the salt tank at C8 which takes 9minutes in my setup (30 liters of resin)

## Future improvements: TDS meter
- As a future improvement I am thinking about installing TDS (Total Dissolved Solids) meter on the waste water outlet. By measuring TDS, the salt rinse can be stopped at the exact moment the salt concentration drops to 0 (to be specific to the same TDS as it was during rinse = tap water) and only clean water is flowing to the drain. This eliminates water waste to absolute minimum, while still maintaining 100% proper rinse of the salt out of the resin.
- I am bit disappointed that this approach is not used by the manufacturers by default and they waste huge amounts of water instead using some slack timing control with 100%-200% margins ...
- This option would make the water softener more versatile in different configurations (resin volume/salt use setting, different pressures (= flowrates)). For one fixed scenario the timing control looks quite sufficient TBH.

### Future improvements: water consumption/ water remaining...
- Add counters like "total water usage since installation, water used since regeneration, water remaining until next regeneration....

## Regeneration controlled by HA
By default the regeneration is controlled autonomously by the Autotrol valve logic once the softening capacity is exhausted (at 2:00 at night or any other time you choose). It is OK for most people but If you want to control the regeneration from HA set the softening capacity of the Autotrol to some higher value (see manual) so it won't regenerate automatically. Then set your water counter and automation as you wish to long press the regen. button... It is the same logic as setting mechanical thermostat on your boiler at 80°C but cutting power using smart switch at 60°C from HA.
