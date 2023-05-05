Something3D

___________
Parts:

Ramps V1.4 (can be read on board)
screen + sd card reader mj204b (RAMPS GADGETS3D LCD and SHIELD v1.0) http://gadgets3d.com/beta/index.php?route=product/product&product_id=65
gadgets3d fan splitter rev1
Arduino mega 2560
installed stepper motor drivers, on Ramps board: G3D A498 driver rev1
hotbed thermocoupler resistence: 142.7 kOhm
Nozzle on both = 0.35 (according to an old slic3r file from 2013, probably legit)

X max travel 280
Y max travel 270 (but can actually reach 280, if I really need it one day...)
Z max travel 270

3/4 extruder motors: sy42sth47 1206A (except Z)
(060047083 & 1806307 is still also readable on them)

____________

Original Firmware settings:
Control->Temperature
Autotemp off
	Min:210
	Max:250
	Fact:000.10
PID-P: +058.91
PID-I: +004.96
PID-D: +174.70
PID-C: 1

Control->Temperature->Preheat PLA conf
Fan speed:255	(i'm assuming this isn't wired)
Nozzle:200
Bed:60

Control->Temperature->Preheat ABS conf
Nozzle:235 (240 default for marlin firmware)
Bed:105 (110 default for marlin firmware)

Control->Motion
Accel:500
Vxy-jerk: 5
Vz-jerk: +000.10
Ve-jerk: 5
Vmax x: 500
Vmax y: 500
Vmax z: 5
Vmax e: 25
Vmin: 0
VTray min: 0
Amax x: 900
Amax y: 900
Amax z: 5
Amax e: 10000
A-retract: 900
Xsteps/mm: +053.20  ** new: 53.5
Ysteps/mm: +053.20  ** new: 53.5
Zsteps/mm: +1632.0  ** new: 1600
Esteps/mm: +0095.6  ** new: 92.73 for 1st (new) extruder, unknown for 2nd
updated Esteps to 94.18 on 4.5.23.

_____________
A few links that helped me:

https://3dprinting.stackexchange.com/questions/6535/controlling-more-fans-with-ramps-board?newreg=1a15be9784524da9a4cec570d9a283f3

UBL:
https://marlinfw.org/docs/features/unified_bed_leveling.html
https://3dprinting.stackexchange.com/questions/8153/how-to-set-z-probe-boundary-limits-in-firmware-when-using-automatic-bed-leveling
https://github.com/MarlinFirmware/Marlin/issues/9418 



_____________
Notes for self. Testing and calibrating my printer 29.1.2021
100mm at 50mm/min 2nd extruder (left) T1, extruded 105.3mm.
100mm at 50mm/min 1st extruder, extruded 98mm.
why?
anyhow current value is 95.6.
so let's print with 1st extruder, new value 97.551
https://www.e-ci.com/3d-scoop/2020/1/16/calculating-e-steps-extruder-accuracy-in-octoprint

Watching how the cel automaker prints: EVERY layer has a full grid infill. as oppose to: one layer criss, other layer cross. so adhesion must be better!!
Kisslicer is not willing to generate that!
(fill pattern is called 'lines' in automaker, but Cura calls it 'grid'. and it is using Cura for the gcode.

I'm overextruding while the printer is moving - then there's low pressure when it's starting to extrude again.
I can retract at every Z level change and then add 0.64 mm^3 (retraction extra prime amount - cura). looks better.
https://community.ultimaker.com/topic/15459-retraction-extra-prime-amount-suggested-values/

Z max feedrate at 10 was too much! went back to default 5!
50mm movement at Z gives me 51mm!
current steps:1632 per mm.
//fyi:
//10 threads are ~19.5mm
//1mm = 0.5128205128205128 = 1632of Z axis turn (~half a thread)
1632 * 50 / 51 = 1600. seems great!

fml. x axis 130mm was 129.3
053.20 * 130/129.3
new steps: 53.48. 53.5? works great. (assumed x/y are the same. for good reasons though)

new extruder assembly. 
now 100mm extrusion = 105.2, new 92.73, good.

20x20x5 cube *on CEL automaker*:
11.67 seconds for single parameter (hollow)
80/11.67 = 6.85 mm/s
30 seconds for the top layer. 
which is 50 stripes of 0.4mm extrusion. 50x20 = 1000mm total layer length.
1000/30 = 33mm/s for the top layer.


_____________
Some Gcode commands
http://marlinfw.org/meta/gcode/
g92 z0 set home
g90 absolute
g91 relative

g28 - auto home - watch out!
M114 - Get Current Position
M119 - Get endstop state
M211 - software endstops off/on
M226 - wait for pin State.


ניסיון שימוש בפקודה שמחכה ללחיצה על המיקרוסויץ' של הזד שלא עובדת כרגע:
G0 X0
M117 wait for z limit switch to get pressed  (only 20 letters on screen)
M226 P18 S1
G0 X5
M25 ; pause sd print
G0 x0




5.5.23, current m503:
SENDING:M503
echo:  G21    ; Units in mm (mm)
echo:  M149 C ; Units in Celsius
echo:Filament settings: Disabled
echo:  M200 D1.75
echo:  M200 T1 D1.75
echo:  M200 D0
echo:Steps per unit:
echo: M92 X53.50 Y53.50 Z1600.00 E94.18
echo:Maximum feedrates (units/s):
echo:  M203 X500.00 Y500.00 Z5.00 E500.00
echo:Maximum Acceleration (units/s2):
echo:  M201 X500.00 Y500.00 Z100.00 E10000.00
echo:Acceleration (units/s2): P<print_accel> R<retract_accel> T<travel_accel>
echo:  M204 P500.00 R2000.00 T3000.00
echo:Advanced: B<min_segment_time_us> S<min_feedrate> T<min_travel_feedrate> J<junc_dev>
echo:  M205 B20000.00 S0.00 T0.00 J0.01
echo:Home offset:
echo:  M206 X0.00 Y0.00 Z0.00
echo:Hotend offsets:
echo:  M218 T1 X33.30 Y0.00 Z0.000
echo:Unified Bed Leveling:
echo:  M420 S1 Z10.00
Unified Bed Leveling System v1.01 active
Active Mesh Slot: 1
EEPROM can hold 7 meshes.
echo:Material heatup parameters:
echo:  M145 S0 H195 B60 F0
echo:  M145 S1 H235 B105 F0
echo:PID settings:
echo:  M301 P161.35 I24.63 D264.29 F0.00
echo:  M301 P107.71 I13.55 D213.98 F0.00
echo:  M304 P73.68 I7.56 D478.84
echo:Z-Probe Offset (mm):
echo:  M851 X-44.00 Y-4.00 Z7.50
echo:Linear Advance:
echo:  M900 T0 K0.12
  M900 T1 K0.00
echo:Tool-changing:
echo: Z2.00
