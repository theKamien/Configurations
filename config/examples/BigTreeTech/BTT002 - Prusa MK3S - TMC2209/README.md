# BigTreeTech BTT002/TMC2209 Config for Prusa MK3S

## Requirements
- [Prusa MK3S](https://www.prusa3d.com/original-prusa-i3-mk3/) or MK3 with MK3S upgrade (required for filament runout to work properly)
- BigTreeTech BTT002 motherboard
- 4 x BigTreeTech TMC2209s (DIAG pin location differs from Watterott's & similarl designs)

## Upgrade Notes
* ⚠️ Cut or desolder the Z & E driver DIAG pins or they will interfere with PINDA & filament runout detection. ⚠️
* Set the jumpers under your drivers to "TMC2208-UART MODE":
![image](https://user-images.githubusercontent.com/13375512/74117621-24415000-4b6d-11ea-8811-f867e187ea0c.png)

## Changes to Start G-code
The `W` in Prusa's `G28 W ; home all without mesh bed level` G-code does not exist in Marlin and [`G80 ; mesh bed leveling`](https://marlinfw.org/docs/gcode/G080.html) cancels the current motion mode, so no bed leveling will take place.

### PrusaSlicer
Paste the start g-code block below in the Printer Settings tab's Custom G-code section. Use the Filament Settings tab's Custom G-code section to add the [Linear Advance](https://marlinfw.org/docs/features/lin_advance.html) (`M900 K0.0`) value since it can be saved on a per-filament basis.
```gcode
G90 ; use absolute coordinates
M83 ; extruder relative mode
M104 S170 ; preheat extruder to 170
M140 S[first_layer_bed_temperature] ; set bed temp
M190 S[first_layer_bed_temperature] ; wait for bed temp
G28 ; home all
G29 ; mesh bed leveling
G1 X0 Z0.6 Y-3.0 F1000.0 ; go outside print area
M109 S[first_layer_temperature] ; wait for extruder temp
G92 E0.0
G1 X60.0 E9.0 F1000.0 ; intro line
G1 X100.0 E12.5 F1000.0 ; intro line
G92 E0.0
```

### Cura
Paste the start g-code block below in the Start G-code section. Don't forget to add your [Linear Advance](https://marlinfw.org/docs/features/lin_advance.html) (`M900 K0.0`) value in the start G-code block below:
```gcode
G90 ; use absolute coordinates
M83 ; extruder relative mode
M900 K0.0 ; place your linear advance value here
M104 S170 ; preheat extruder to 170
M140 S{material_bed_temperature_layer_0} ; set bed temp
M190 S{material_bed_temperature_layer_0} ; wait for bed temp
G28 ; home all
G29 ; mesh bed leveling
G1 X0 Z0.6 Y-3.0 F1000.0 ; go outside print area
M109 S{material_print_temperature_layer_0} ; wait for extruder temp
G92 E0.0
G1 X60.0 E9.0 F1000.0 ; intro line
G1 X100.0 E12.5 F1000.0 ; intro line
G92 E0.0
```

### Simplify3D
Paste the start g-code block below in the Starting Script section. Don't forget to add your [Linear Advance](https://marlinfw.org/docs/features/lin_advance.html) (`M900 K0.0`) value in the start G-code block below:
```gcode
G90 ; use absolute coordinates
M83 ; extruder relative mode
M900 K0.0 ; place your linear advance value here
M104 S170 ; preheat extruder to 170
M140 S[bed0_temperature] ; set bed temp
M190 S[bed0_temperature] ; wait for bed temp
G28 ; home all
G29 ; mesh bed leveling
G1 X0 Z0.6 Y-3.0 F1000.0 ; go outside print area
M109 S[extruder0_temperature] T0 ; wait for extruder temp
G92 E0.0
G1 X60.0 E9.0 F1000.0 ; intro line
G1 X100.0 E12.5 F1000.0 ; intro line
G92 E0.0
```