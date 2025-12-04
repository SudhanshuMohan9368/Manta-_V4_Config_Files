# Manta-_V4_Config_Files
Heater Expander Board (8× Well Heaters via USB MCU)

This document describes the changes made to the machine to add an external 8-channel heater board controlled by Klipper via a second MCU connected over USB.

The main motion + base heaters remain on the Manta MP5 board.
All 8 well heaters (Lysis + Elution wells) are driven by a custom STM32G0B1-based expander board.

1. New MCU: expander (USB heater board)

A new MCU is added to Klipper and connected over USB.

[mcu expander]
serial: /dev/serial/by-id/usb-Klipper_stm32g0b1xx_12345-if00


This expander MCU exposes GPIO pins PB0–PB4, etc., which are used to drive MOSFETs for 8 heater channels and read 8 NTC thermistors.

2. Heater Configuration (8 well heaters)

All well heaters are defined as heater_generic devices on the expander MCU.
The NTCs are EPCOS 100K (B57560G104F) with 4.7k pullups.

2.1 Lysis / Elution Well Groups

By naming convention:

Lysis group (odd wells):

Lysis_Wells (well 1)

heater3 (well 3)

heater5 (well 5)

heater7 (well 7)

Elution group (even wells):

Elution_Wells (well 2)

heater4 (well 4)

heater6 (well 6)

heater8 (well 8)

These names are used consistently in macros and temperature wait logic.

2.2 heater_generic and verify_heater definitions
[heater_generic Lysis_Wells]
# Logical well 1 (Lysis)
heater_pin: !expander:PB12
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA7
pullup_resistor: 4700
control: pid
pid_Kp: 13.320
pid_Ki: 0.109
pid_Kd: 405.121
max_power: 1.0
min_temp: -273
max_temp: 300
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater Lysis_Wells]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic Elution_Wells]
# Logical well 2 (Elution)
heater_pin: !expander:PB11
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA6
pullup_resistor: 4700
control: pid
pid_Kp: 15.691
pid_Ki: 0.156
pid_Kd: 394.838
max_power: 1.0
min_temp: -273
max_temp: 300
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater Elution_Wells]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic heater3]
heater_pin: !expander:PB10
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA0
pullup_resistor: 4700
control: pid
pid_Kp: 13.320
pid_Ki: 0.109
pid_Kd: 405.121
max_power: 1.0
min_temp: -273
max_temp: 500
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater heater3]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic heater4]
heater_pin: !expander:PB2
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA1
pullup_resistor: 4700
control: pid
pid_Kp: 15.691
pid_Ki: 0.156
pid_Kd: 394.838
max_power: 1.0
min_temp: -273
max_temp: 200
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater heater4]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic heater5]
heater_pin: !expander:PB1
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA3
pullup_resistor: 4700
control: pid
# Original starting values:
# pid_Kp: 13.320
# pid_Ki: 0.109
# pid_Kd: 405.121
pid_Kp: 51.003
pid_Ki: 1.545
pid_Kd: 420.790
max_power: 1.0
min_temp: -273
max_temp: 200
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater heater5]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic heater6]
heater_pin: !expander:PB0
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA2
pullup_resistor: 4700
control: pid
# Original starting values:
# pid_Kp: 15.691
# pid_Ki: 0.156
# pid_Kd: 394.838
pid_Kp: 24.462
pid_Ki: 0.340
pid_Kd: 440.335
max_power: 1.0
min_temp: -273
max_temp: 200
pwm_cycle_time: 0.3
smooth_time: 3.0

[verify_heater heater6]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10
# Example: pid_Kp=28.024 pid_Ki=0.258 pid_Kd=761.934 from previous tuning, for reference.


[heater_generic heater7]
heater_pin: !expander:PB3
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA4
pullup_resistor: 4700
control: pid
pid_Kp: 51.003
pid_Ki: 1.545
pid_Kd: 420.790
max_power: 1.0
min_temp: -273
max_temp: 200
pwm_cycle_time: 0.3
smooth_time: 2.0

[verify_heater heater7]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10


[heater_generic heater8]
heater_pin: !expander:PB4
sensor_type: EPCOS 100K B57560G104F
sensor_pin: expander:PA5
pullup_resistor: 4700
control: pid
pid_Kp: 15.691
pid_Ki: 0.156
pid_Kd: 394.838
max_power: 1.0
min_temp: -273
max_temp: 700
pwm_cycle_time: 0.02
smooth_time: 3.0

[verify_heater heater8]
max_error: 240
check_gain_time: 120
heating_gain: 1
hysteresis: 10

3. Heater Group Control Macros

These macros control heaters in groups, based on the biological workflow:

Lysis heaters: 1, 3, 5, 7

Elution heaters: 2, 4, 6, 8

All macros assume the heater and sensor names defined above.

###############################################################################################
# HEATER GROUP CONTROL MACROS
###############################################################################################

# HEATER= names must match the [heater_generic] sections:
#   Lysis_Wells, Elution_Wells, heater3, heater4, heater5, heater6, heater7, heater8
# SENSOR="heater_generic <name>" must also match.

[gcode_macro SET_LYSIS_TEMP]
description: Set temperature for all 4 Lysis heaters (1,3,5,7)
gcode:
    {% set TEMP = params.TEMP|default(70)|float %}
    RESPOND TYPE=echo MSG="Setting LYSIS heaters (1,3,5,7) to {TEMP}°C"
    SET_HEATER_TEMPERATURE HEATER=Lysis_Wells TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater3      TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater5      TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater7      TARGET={TEMP}
    M117 Lysis heaters set to {TEMP}°C


[gcode_macro SET_ELUTION_TEMP]
description: Set temperature for all 4 Elution heaters (2,4,6,8)
gcode:
    {% set TEMP = params.TEMP|default(85)|float %}
    RESPOND TYPE=echo MSG="Setting ELUTION heaters (2,4,6,8) to {TEMP}°C"
    SET_HEATER_TEMPERATURE HEATER=Elution_Wells TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater4       TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater6       TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater8       TARGET={TEMP}
    M117 Elution heaters set to {TEMP}°C


[gcode_macro LYSIS_OFF]
description: Turn off all LYSIS heaters (1,3,5,7)
gcode:
    RESPOND TYPE=echo MSG="Turning off LYSIS heaters (1,3,5,7)"
    SET_HEATER_TEMPERATURE HEATER=Lysis_Wells TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater3      TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater5      TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater7      TARGET=0
    M117 Lysis heaters OFF


[gcode_macro ELUTION_OFF]
description: Turn off all ELUTION heaters (2,4,6,8)
gcode:
    RESPOND TYPE=echo MSG="Turning off ELUTION heaters (2,4,6,8)"
    SET_HEATER_TEMPERATURE HEATER=Elution_Wells TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater4       TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater6       TARGET=0
    SET_HEATER_TEMPERATURE HEATER=heater8       TARGET=0
    M117 Elution heaters OFF


[gcode_macro ALL_HEATERS_OFF]
description: Turn off ALL 8 well heaters
gcode:
    RESPOND TYPE=echo MSG="Turning off ALL well heaters"
    LYSIS_OFF
    ELUTION_OFF
    M117 All well heaters OFF


[gcode_macro WAIT_LYSIS_TEMP]
description: Wait for all Lysis heaters (1,3,5,7) to reach target temperature
gcode:
    {% set TEMP = params.TEMP|default(70)|float %}
    SET_LYSIS_TEMP TEMP={TEMP}
    RESPOND TYPE=echo MSG="Waiting for LYSIS heaters (1,3,5,7) to reach {TEMP}°C"
    TEMPERATURE_WAIT SENSOR="heater_generic Lysis_Wells" MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater3"      MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater5"      MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater7"      MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    M117 Lysis heaters ready at {TEMP}°C
    RESPOND TYPE=echo MSG="All LYSIS heaters at target temperature"


[gcode_macro WAIT_ELUTION_TEMP]
description: Wait for all Elution heaters (2,4,6,8) to reach target temperature
gcode:
    {% set TEMP = params.TEMP|default(85)|float %}
    SET_ELUTION_TEMP TEMP={TEMP}
    RESPOND TYPE=echo MSG="Waiting for ELUTION heaters (2,4,6,8) to reach {TEMP}°C"
    TEMPERATURE_WAIT SENSOR="heater_generic Elution_Wells" MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater4"        MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater6"        MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    TEMPERATURE_WAIT SENSOR="heater_generic heater8"        MINIMUM={TEMP-2} MAXIMUM={TEMP+5}
    M117 Elution heaters ready at {TEMP}°C
    RESPOND TYPE=echo MSG="All ELUTION heaters at target temperature"

4. PID Tuning Macro for All 8 Heaters

To simplify tuning of the external heater board, a single macro runs PID calibration for all 8 heaters, then saves the configuration.

Lysis heaters are tuned at 70 °C

Elution heaters are tuned at 85 °C

[gcode_macro PID_TUNE_ALL]
description: Run PID tuning for LYSIS (1,3,5,7) at 70°C and ELUTION (2,4,6,8) at 85°C, then save config
gcode:
    {% set lysis_heaters = ["Lysis_Wells", "heater3", "heater5", "heater7"] %}
    {% set elution_heaters = ["Elution_Wells", "heater4", "heater6", "heater8"] %}
    {% set lysis_target = 70.0 %}
    {% set elution_target = 85.0 %}

    M117 Starting PID tuning sequence...
    RESPOND TYPE=echo MSG="Starting PID tuning for all 8 well heaters..."

    ; --- LYSIS GROUP (1,3,5,7) ---
    RESPOND TYPE=echo MSG="Tuning LYSIS heaters (1,3,5,7) at {lysis_target}°C"
    {% for h in lysis_heaters %}
        RESPOND TYPE=echo MSG="Starting PID tune for {h} to {lysis_target}°C"
        M117 PID tuning {h} to {lysis_target}°C
        PID_CALIBRATE HEATER={h} TARGET={lysis_target}
        G4 P2000
    {% endfor %}

    ; --- ELUTION GROUP (2,4,6,8) ---
    RESPOND TYPE=echo MSG="Tuning ELUTION heaters (2,4,6,8) at {elution_target}°C"
    {% for h in elution_heaters %}
        RESPOND TYPE=echo MSG="Starting PID tune for {h} to {elution_target}°C"
        M117 PID tuning {h} to {elution_target}°C
        PID_CALIBRATE HEATER={h} TARGET={elution_target}
        G4 P2000
    {% endfor %}

    RESPOND TYPE=echo MSG="PID tuning sequence complete. Saving configuration..."
    M117 Saving PID config...
    SAVE_CONFIG
    M117 PID tuning done for all well heaters!

PID Tuning Usage

From the Klipper console:

PID_TUNE_ALL


This will:

Iterate over all 4 Lysis heaters (1,3,5,7) at 70 °C

Iterate over all 4 Elution heaters (2,4,6,8) at 85 °C

Call SAVE_CONFIG to write updated PID parameters into printer.cfg (or heater.cfg if you split it).

5. Summary

Added a dedicated USB heater expander board ([mcu expander]) based on STM32G0B1.

Mapped 8 external MOSFET + NTC channels to:

Lysis_Wells, Elution_Wells, heater3, heater4, heater5, heater6, heater7, heater8

Implemented:

Group temperature macros for Lysis and Elution wells

Safety macros to turn off all heaters

A single macro (PID_TUNE_ALL) to tune and save PID for all 8 heaters.

You can now fully test the external heater board independently of the Manta MP5, while keeping the Klipper configuration readable and version-controlled in this repo.
