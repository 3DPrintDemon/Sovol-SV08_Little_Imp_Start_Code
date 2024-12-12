# SV08 Litte Imp Stand Alone ORCA SLICER Machine Start Gcode

If you feel this start Gcode is valuable enough to use please consider hitting that "sponsor this project" button or the Ko-Fi image below or at https://ko-fi.com/3dprintdemon & buying me a beer/coffee. It's always very much appreciated & anything you do send goes towards helping me continue putting my ideas out there for the whole 3D printing community. Sending even a little makes a difference! Thank you & happy printing!!

[<img width="171" alt="kofi_s_tag_dark" src="https://github.com/3DPrintDemon/Demon_Klipper_Essentials_Unified/assets/122202359/08473899-563b-4b4d-9409-5e6602d6ec44">](https://ko-fi.com/3dprintdemon)

****************************************************************************************************************************
## NOTE: THIS IS NOT COMPATIBLE WITH DEMON KLIPPER ESSENTIALS! IT WILL NOT WORK IF YOU TRY TO MIX THE TWO!
****************************************************************************************************************************

This is a simple back to basics stand-alone slicer start gcode mod, there are no frills, no bells or whistles, it is only a functional reworking of the original Sovol SV08 Orca Slicer Machine Start Gcode that does not lock you into single temperature pre-print setup. With just a few simple edits you can easily modify your system.

It will do all the pre-print setup at print file temperatures with the nozzle at 160ºc & then raise to printing temperature before printing the stock purge lines. No more stock heating & cooling to this or that temperature. You can also now opt to do an auto Z offset (WITHOUT THE TEST PRINT!!) each time the print starts, edits required! I personally DO NOT recommend this, as at the time of writing the Sovol auto Z offset process is NOT accurate or reliable enough for repeated per-print use!
Using this feature could result in a high chance of PRINTER DAMAGE! I recommend you DO NOT use it. However the option is there & you can enable it if you wish. 

## ANY PRINTER DAMAGE THAT RESULTS FROM ENABLING THIS PER-PRINT AUTO Z FEATURE IS TOTALLY ON YOU, YOU HAVE BEEN WARNED> USE AT YOUR OWN RISK!



## Edit your Printer.cfg File

### Step 1:

Open your stock Sovol `Macro.cfg` file & scroll down to lines 131 & line 132


## DAMAGE WARNING!!!
### If you enable the per-print auto Z feature in the Machine Start G-code BUT do NOT comment out the two lines shown below you WILL do the test print BEFORE your main print starts! THIS IS VERY BAD!! DO NOT FORGET TO DO THIS!!

Comment out these two lines by putting a hashtag at the very start of both of them:

EXAMPLE

```
#   M23 /.zoffset_test.gcode
#   M24
```

### Step 2:

Now select the entire `[gcode_macro CLEAN_NOZZLE]` macro.

This starts at line 48 & ends at line 114

Now with all those lines selected press ctrl+/ on PC or CMD+/ on MacOS

This will now place a hashtag at the beginning of each line of the selection, commenting the whole thing out of the system.


### Step 3:

Deselect the now commented out CLEAN_NOZZLE macro from the previous step 

Put the curser on very end of line 114, NOT the beginning, then press return two times to drop the remaining text down two lines & to create some clear space between macros.

Now paste in this edited macro underneath the old one, it uses exaclty the same movements & positioning as your old stock one:
```
[gcode_macro CLEAN_NOZZLE] 
gcode:
    {% if printer.toolhead.homed_axes != "xyz" %}
       G28
    {% endif %}
    G90 
    G1 X348 Y0 Z0.3 F9000
    M117 Nozzle heating...
    M109 S160
    G91
    G1 Z10 F300
    G90
    M106 S255
    M117 Clean nozzle
    G1 X315 Y360 F9000
    G1 Z0.2 F300
    G1 X352 F4500
    G1 Y360 X324
    G1 Y360 X345
    G1 Y360 X324
    G1 Y360 X345
    G1 Y360 X324
    G1 Y360 X345
    G1 Y360 X324
    G1 Y360 X345
    G1 Y360 X324
    G1 Y360 X325
    G1 Y356 X324 Z5
    G1 Z0.2
    G1 Y360 X324
    G1 Y357 X326
    G1 Y360 X326
    G1 Y357 X328
    G1 Y360 X330
    G1 Y357 X332
    G1 Y360 X334
    G1 Y357 X336
    G1 Y360 X338
    G1 Y357 X340
    G1 Y360 X324
    G1 Y357 X326
    G1 Y360 X326
    G1 Y357 X328
    G1 Y360 X330
    G1 Y357 X332
    G1 Y360 X334
    G1 Y357 X336
    G1 Y360 X338
    G1 Y357 X340
    G1 Y360 X324
    G1 Y357 X326
    G1 Y360 X326
    G1 Y357 X328
    G1 Y360 X330
    G1 Y357 X332
    G1 Y360 X334
    G1 Y357 X336
    G1 Y360 X338
    M400
    M117 Clean Finish
    M107 
    G91
    G1 Z10 F300
    G90
    G28 Z0
  {% if printer.print_stats.state not in ["printing", "paused"] %}
      M104 S0
  {% endif %}

```

### Step 4: 

Press the Save & Restart button at the top of the screen.

*********************

## Edit Your ORCA Slicer Machine Start Code

### STEP 1:

Open Orca Slicer & click on the printer name in the top left to open the `Printer Settings` menu, then click the `Machine G-ocde` tab. 

Copy your current Orca Slicer `Machine Start G-code` into Notepad or similar - save as a backup!


### Step 2:

Replace the entire Machine Start G-code text with this:


```
; 3DPrintDemon Little Imp Orca Slicer Machine Start Gcode
G90
M83
G28
M118 Heating bed, please wait…
M190 S[bed_temperature_initial_layer_single] ; Setting bed temperature
M118 Nozzle preheating, please wait…
M109 S160
M118 Running QGL…
QUAD_GANTRY_LEVEL_BASE
M117
G0 Z20
G28 Z
M118 Cleaning nozzle
CLEAN_NOZZLE
M400

; WARNING - USE AT OWN RISK - INCREASED CHANCE OF PRINTER DAMAGE!
; To perform auto Z calibrations per print delete the semi colons (";") at the beginning of the 3 lines below.

; M118 Auto Z offset 
; Z_OFFSET_CALIBRATION
; M400

; Auto Z actions above this line ^^^^^^^

M117
G0 Z20
BED_MESH_CALIBRATE_BASE ADAPTIVE=1
G0 Z20
M400

; Extract from Stock Sovol purge line draw…
G1 X88.000 Y0 F9000
M109 S[nozzle_temperature_initial_layer] ; Setting hot-end temperature
G1 E-0.200 F1800
G1 Z0.800 F600
G91
M83
G1 X87.000 E20.88 F1800
G1 X87.000 E13.92 F1800
G1 Y1 E0.16 F1800
G1 X-87.000 E13.92 F1800
G1 X-87.000 E20.88 F1800
G1 Y1 E0.24 F1800
G1 X87.000 E20.88 F1800
G1 X87.000 E13.92 F1800
G1 E-0.200 Z5 F600
G90
G92 E0
M400
M117 Printing…

```

### Step 3:

Save your new start g-code in your Orca profile


FIRST USE WARNING!
Be very careful using any new macros or settings for the first time. Cover the emergency stop button incase something should go wrong, you never know. Every effort has been made to make sure these changes are good & that they work, but please do be careful. By using any information contained here on this page you accept full responiblity & liability for any & all bad outcomes &/or damage or loss incurred. Use at your own risk. 

[<img width="171" alt="kofi_s_tag_dark" src="https://github.com/3DPrintDemon/Demon_Klipper_Essentials_Unified/assets/122202359/08473899-563b-4b4d-9409-5e6602d6ec44">](https://ko-fi.com/3dprintdemon)
