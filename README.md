# KNOMI1

Added the ability to control the display brightness from klipper.

Add ```variable_brightness``` to ```_KNOMI_STATUS``` macro. 0 = off, 16 = max. I think Knomi1 desn't accualy have brightness control, only on or off.

```ini
[gcode_macro _KNOMI_STATUS]
variable_homing: False
variable_probing: False
variable_qgling: False
variable_heating_nozzle: False
variable_heating_bed: False
variable_brightness: 16
gcode:

[gcode_macro KNOMI_OFF]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=brightness VALUE=0

[gcode_macro KNOMI_ON]
gcode:
  SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=brightness VALUE=16
```

---

You can use this macro to automatically turn off the display when idle

```ini
[idle_timeout]
timeout: 900 # 15min
gcode:
  M84
  TURN_OFF_HEATERS
  STATUS_OFF # SB leds
  LIGHT_OFF # Frame leds
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
    SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=brightness VALUE=0
    UPDATE_DELAYED_GCODE ID=__KNOMI_CHECK_IDLE DURATION=1
  {% else %}
    SET_GCODE_VARIABLE MACRO=_KNOMI_STATUS VARIABLE=brightness VALUE=16
  {% endif %}
```

## Knomi2

Should work, don't know ¯\\\_(ツ)\_/¯
