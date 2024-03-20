# Autotrol 760 water softener valve ESPhome integration
"Hardware assisted" integration of Pentair Autotrol 760 water softener valve into Esphome and Home Assistant

## Introduction
Pentair Autotrol 760 is a controller used in water softeners/filters to govern softener regeneration based on amount of water used, hardness of the water, time of day and more. It features multiple cycles that run in sequence to do proper resin regneration (backwash, brine draw, rinse, downflow rinse, brine refill..)

## Motivation
1) I wanted to see my water consumption in Home Assistant dashboard and generate statistics based on this data. The Autotrol controller monitors the water usage (hall-effect sensor + turbine with magnet) to count how many liters remainis before another regeneration is needed. Unfortunatelly there is no way how to extract the data. And I didn't want to use another water meter in-line.
2) In my opinion the regeneration process is wasting a lot of water in two steps: At the beginning there is a 10minute backwash at the rate of 8liters/minute. The backwash is needed to "fluff up" (expand) the softener resin beads and to wash any dirt to the drain. In my opinion the 10minutes is too long and causes around 80liters of water wasted. Unfortunatelly the time is fixed and can't be changed in this model.
3) Another wasted water is during salt draw and subsequent slow rinse of the resin bed. Once the brine tank is empty, the salt valve is closed and the resin is slowly rinsed to flush the salt into drain. Those two steps take around 70minutes in my configuration, while roughly first 15minutes is needed for the salt draw and the rest is for the rinse. As I observed the regeneration cycle, the rinse was sufficiently completed after 35 minutes (yes I tasted the expelled water to see if there is still salt being washed out). Even with all the salt flushed away the cycle took another 35minutes - wasting another 50+ liters of water. The timing set by manufacturer is in my opinion "too conservative" and causes a LOT of water wasted over the lifetime of the product. I wated to rectify this. I tried different "resin amount" and "salt amount" settings to lower this time but it didn't work as expected. By setting lower resin volume or lower salt amount, the cycle time got shorter but this also caused significant lowering of the brine refill time - resulting in insufficient amount of water(brine) for the next regeneration and improper resin regeneration.

## Results! 
### Water savings! 
By default in my configuration the softener used **250liters** of water for regeneration. After implementing this modification the amount of wated water was lowered to only **130liters** per regeneration and with more precise timing or TDS measurement it could be lowered even further!
### HA control and status
![obrazek](https://github.com/landrysik/autotrol-esphome/assets/124715451/921221c0-1e11-47a2-ade2-1ac45a1047d7)
### Water usage stats!
By feeding the water usage into HA energy dashboard, you can see your monthly/daily/hourly water consumption
![obrazek](https://github.com/landrysik/autotrol-esphome/assets/124715451/04147a56-2d27-4885-903a-5d40e062a00e)


## Modification principle
I decided to place ESP8266 microcontroller (Wemos D1 mini PRO) to emulate button presses of the Autotrol controller. If you press "square" + "button up" during regeneration, the controller advances to the next regeneration step. I exploited this and simulate button presses with the ESP8266 at the right times.
I also read out the state of the motor that rotates the valve shaft to get the current cycle number. 
As for water consumption I also tapped off the signal from hall-effect water turbine sensor and count pulses in ESPhome

## Teardown
> [!WARNING]
> the disassembly is bit destructive as the casing is glued/ultrasonically welded. At the end you will need to glue it back together

Use plastic prying tools + picks to slide between two parts of the plastic. Continue around the edge - you will need to use a bit of force to break the plastic welds

![Prying the case open](/images/opening.jpg)

After you are in, you see the bare board. 
![PCB side 1](/images/bare_board_1.jpg)

And the second side (after removing 4 screws):
![PCB side 2](/images/bare_board_2.jpg)

## Modification
1) Interfacing with the buttons is done by using small N-channel MOSFET transistor across the actual button. Buttons are NO with pullup to 3.6V. When pressed they short to GND. The MOSFET does the same when voltage from ESP8266 is applied to gate. I used breakout boards as the SO23 MOSFETs are too delicate for such purpose.
2) Reading the motor status is tapped off at the TP 19 or R7. I placed small signal diode and 1.8k resistor in series. Additional pull down resistor (1.8M) is connected to mitigate the issue with essentialy floating input on ESP side. When the motor is turning, there is 3.3V going to the triac controlling the actual motor current.
3) Water flow pulses are read from TP34. I again used small diode to mitigate any current flowing into the controller in case the ESP8266 misbehaving (during prototyping etc). Additional 1:1 resistor divider is implemented as the voltage signal is around 5.6V
![Modified board](/images/modified_board.jpg)

Here is a pin mapping into the ESP8266
| Wemos D1 pin  | Autotrol Controller |
| ------------- | ------------- |
| D1  | Square button  |
| D5  | UP button  |
| D2  | Regeneration button  |
| D6  | Water flow pulse input  |
| D7  | Motor input  |

At the end I routed all the wires out of the case using the hole under the sticker (can be easily removed with few drops of isopropyl alcohol). Presumably used for production programming.
The ESP8266 board is powered externally at the moment as I don'T have any suitable DC/DC converter at hand (all of this was a nice weekend project with components sourced from "the drawer"). Using existing power rail in the controller is not a great idea as the power path is not designed to handle any significant currents. The input 12V AC is half bridge rectified and simple wimpy LM317 in SO8 package linear stabilizer is used (maximum current 100mA). The ESP draws around 80mA so this will not be feasible.
I want to place the ESP inside the controller casing together with 12VAC ->3.3V DC regulator as a permanent setup. 



![Finished mod](/images/casing.jpg)

## ESPhome code + principle of operation
For the ESPhome code + explained principle of operation see /esphome

