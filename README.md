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

[gcode_macro KNOMI_OFF]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE=True

[gcode_macro KNOMI_ON]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE=False
```

---

You can use this macro to automatically turn off the display when idle
```ini
[idle_timeout]
timeout: 900 # 15min
gcode:
  M84
  TURN_OFF_HEATERS
  STATUS_OFF
  UPDATE_DELAYED_GCODE ID=__KNOMI_CHECK_IDLE DURATION=1

[delayed_gcode __KNOMI_CHECK_IDLE]
initial_duration: 1
gcode:
  {% set state = printer.idle_timeout.state %}
  {% set vars = printer["gcode_macro _KNOMI_STATUS"] %}
  {% set is_any_true = vars.homing or vars.probing or vars.qgling or vars.heating_nozzle or vars.heating_bed %}
  # i know there is a better way to do this but i dont want to work with jinja any more then i have to
  # python is a terrible language and jinja is somehow even worse
  {% if state == "Idle" and is_any_true == False %}
    SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE=True
    UPDATE_DELAYED_GCODE ID=__KNOMI_CHECK_IDLE DURATION=1
  {% else %}
    SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=off VALUE=False
  {% endif %}
```
Then set the knomi homing variable in ```[homing_override]```, probing in ```[BED_MESH_CALIBRATE]``` and qgling in ```[QUAD_GANTRY_LEVEL]```.
Dont forget to set them back to False at the end of each macro ;)

And make sure to do ```VALUE=True``` and not ```VALUE="True"```, spent an hour finding that mistake.
