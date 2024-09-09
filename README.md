# KNOMI1

Added the ability to turn off the display.

Add ```variable_off``` to ```_KNOMI_STATUS``` macro.

```ini
[gcode_macro _KNOMI_STATUS]
variable_homing: False
variable_probing: False
variable_qgling: False
variable_heating_nozzle: False
variable_heating_bed: False
variable_off: False
gcode:

[gcode_macro knomi_off]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE="True"

[gcode_macro knomi_on]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE="False"
```
