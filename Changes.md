# 🔥 8-Channel Heater Expander (Klipper)  

This document describes the **external 8-channel heater board** used to control well temperatures in the machine.  
The board is a custom STM32G0B1-based controller, connected to Klipper as a second MCU over USB.

- Main motion + base heaters → Manta MP5  
- All **8 well heaters (Lysis + Elution)** → external USB **expander** board  

---

## 🧩 1. Extra MCU: `expander` (USB Heater Board)

Add this to your Klipper config to register the new board:

```ini
[mcu expander]
serial: /dev/serial/by-id/usb-Klipper_stm32g0b1xx_12345-if00
The expander drives MOSFETs and reads NTC thermistors for 8 individual wells.

🧪 2. Well Layout & Heater Mapping
Each physical well has its own heater + thermistor channel.

Well #	Function	Klipper Heater Name	Heater Pin	Thermistor Pin
1	Lysis	Lysis_Wells	!expander:PB12	expander:PA7
2	Elution	Elution_Wells	!expander:PB11	expander:PA6
3	Lysis	heater3	!expander:PB10	expander:PA0
4	Elution	heater4	!expander:PB2	expander:PA1
5	Lysis	heater5	!expander:PB1	expander:PA3
6	Elution	heater6	!expander:PB0	expander:PA2
7	Lysis	heater7	!expander:PB3	expander:PA4
8	Elution	heater8	!expander:PB4	expander:PA5

Logical Groups
Lysis group (odd wells) → Lysis_Wells, heater3, heater5, heater7

Elution group (even wells) → Elution_Wells, heater4, heater6, heater8

All macros below assume this mapping.

🔧 3. Heater Definitions (Klipper [heater_generic])
These are the initial heater + PID settings for the expander board.
After running PID tuning, Klipper will overwrite the PID values with SAVE_CONFIG.

ini
Copy code
[heater_generic Lysis_Wells]
# Well 1 (Lysis)
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
# Well 2 (Elution)
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
# Well 3 (Lysis)
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
# Well 4 (Elution)
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
# Well 5 (Lysis)
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
# Well 6 (Elution)
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
# Example from tuning: pid_Kp=28.024 pid_Ki=0.258 pid_Kd=761.934 (for reference)


[heater_generic heater7]
# Well 7 (Lysis)
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
# Well 8 (Elution)
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
🎛️ 4. Group Heater Control Macros
These macros make it easy to control all Lysis wells together or all Elution wells together, instead of touching each heater separately.

4.1 Set Group Temperatures
ini
Copy code
###############################################################################################
# HEATER GROUP CONTROL MACROS
###############################################################################################

[gcode_macro SET_LYSIS_TEMP]
description: Set temperature for all 4 Lysis heaters (wells 1,3,5,7)
gcode:
    {% set TEMP = params.TEMP|default(70)|float %}
    RESPOND TYPE=echo MSG="Setting LYSIS heaters (1,3,5,7) to {TEMP}°C"
    SET_HEATER_TEMPERATURE HEATER=Lysis_Wells TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater3      TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater5      TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater7      TARGET={TEMP}
    M117 Lysis heaters set to {TEMP}°C


[gcode_macro SET_ELUTION_TEMP]
description: Set temperature for all 4 Elution heaters (wells 2,4,6,8)
gcode:
    {% set TEMP = params.TEMP|default(85)|float %}
    RESPOND TYPE=echo MSG="Setting ELUTION heaters (2,4,6,8) to {TEMP}°C"
    SET_HEATER_TEMPERATURE HEATER=Elution_Wells TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater4       TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater6       TARGET={TEMP}
    SET_HEATER_TEMPERATURE HEATER=heater8       TARGET={TEMP}
    M117 Elution heaters set to {TEMP}°C
4.2 Turn Groups Off / All Off
ini
Copy code
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
4.3 Wait Until Groups Reach Temperature
ini
Copy code
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
📏 5. PID Tuning for All 8 Heaters
To check and tune the extra heater board independently, this macro runs PID calibration for all wells and then saves the configuration.

Lysis (1,3,5,7) tuned at 70 °C

Elution (2,4,6,8) tuned at 85 °C

ini
Copy code
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
How to Run PID Tuning
From the Klipper console:

text
Copy code
PID_TUNE_ALL
This will:

Tune all Lysis wells (1,3,5,7) at 70 °C

Tune all Elution wells (2,4,6,8) at 85 °C

Call SAVE_CONFIG to write back the new PID values.

⚙️ 6. Quick Command Cheat Sheet
For day-to-day use:

text
Copy code
# Set all Lysis wells (1,3,5,7) to 70°C
SET_LYSIS_TEMP

# Set all Elution wells (2,4,6,8) to 85°C
SET_ELUTION_TEMP

# Turn off Lysis or Elution group
LYSIS_OFF
ELUTION_OFF

# Kill all well heaters
ALL_HEATERS_OFF

# Wait until all Lysis wells reach 70°C
WAIT_LYSIS_TEMP

# Wait until all Elution wells reach 85°C
WAIT_ELUTION_TEMP

# Run PID tuning on all 8 wells and save
PID_TUNE_ALL
