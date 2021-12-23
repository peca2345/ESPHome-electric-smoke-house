# Postup
Je nutne mít Home Assistant (dále jen HA) server třeba na Raspberry Pi3 nebo lepší.
V HA si nainstalujte doplněk ESPHome (oficiální integrace)
V ESPHome vytvoř nové zařízení postavené na ESP8266 a zadej název tvojí WIFI a heslo.
Uprav zařízení a pod vygenerovaný kód nakopíruj kód níže






# Použité komponenty
ESP8266 4CH relay board: shorturl.at/qCTV0


# Schéma zapojení


# ESPHome code
```
interval:
  - interval: 5s
    then:
      if:
        condition:
          wifi.connected:
        then:
          - switch.turn_on: iudirna_led3_wifi_status
        else:
          - switch.turn_off: iudirna_led3_wifi_status
          
i2c:
  sda: GPIO4
  scl: GPIO5
  scan: false
  
mcp23017:
  - id: 'mcp23017_hub'
    address: 0x20

dallas: 
  - pin: GPIO2
    update_interval: 10s
      
###############################################################
##### PID TERMOSTAT ###########################################

climate:
  - platform: pid
    id: pid_climate
    name: "iUdirna PID Climate Controller"
    sensor: iudirna_teplota1
    default_target_temperature: 65°C
    visual:
      min_temperature: 0
      max_temperature: 99
      temperature_step: 1
    heat_output: heater
    control_parameters:
      kp: 0.22224  #0.03277 #0.49460  Pásmo proporcionality - určité změně na vstupu odpovídá určitá změna na výstupu
      ki: 0.00067  #0.00004 #0.00487  Integrační čas - určité změně na vstupu odpovídá určitá rychlost na výstupu
      kd: 18.41252 #6.69813 #12.56301 Derivační čas - určité rychlosti změny na vstupu odpovídá určitá poloha regulačního orgánu

###############################################################
###### LCD DISPLEJ ############################################

display:
  - platform: lcd_pcf8574
    dimensions: 20x4
    address: 0x27
    lambda: |-
      it.printf(0, 0, "Udirna: %.1f", id(iudirna_teplota1).state);
      it.printf(13, 0, "/ %.1f", id(pid_climate).target_temperature_low);
      it.printf(0, 1, "Maso 1: %.1f", id(iudirna_teplota2).state);
      it.printf(0, 2, "Maso 2: %.1f", id(iudirna_teplota3).state);
      it.printf(0, 3, "Dymbox: %.1f", id(iudirna_teplota4).state);
      it.printf(13, 3, " T=%.1f", id(iudirna_up_time).state);     

###############################################################  
#####  WIFI SIGNAL  ###########################################
sensor:
  - platform: wifi_signal
    name: "iudirna_wifi_signal"
    update_interval: 10s    

###############################################################  
#####  PID SENSORY  ###########################################

  - platform: pid
    name: "iUdirna PID Climate Result"
    type: RESULT
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate PROPORTIONAL"
    type: PROPORTIONAL 
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate INTEGRAL"
    type: INTEGRAL
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate DERIVATIVE"
    type: DERIVATIVE
    climate_id: pid_climate
  - platform: pid #slouzi pro grafanu
    name: "iUdirna PID Climate HEAT"
    type: HEAT
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate COOL"
    type: COOL
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate KP"
    type: KP
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate KI"
    type: KI
    climate_id: pid_climate
  - platform: pid
    name: "iUdirna PID Climate KD"
    type: KD
    climate_id: pid_climate

###############################################################   
#####  TEPLOMERY  #############################################
    
## UDIRNA
  - platform: dallas 
    name: "iudirna_dallas_teplota1"
    address: 0xC106219415E16428
    id: iudirna_teplota1
    unit_of_measurement: "°C"  

## MASO1
  - platform: dallas
    name: "iudirna_dallas_teplota2"
    address: 0xBE06219419160728
    id: iudirna_teplota2
    unit_of_measurement: "°C"  

## MASO2
  - platform: dallas
    name: "iudirna_dallas_teplota3"
    address: 0xB101215744DA0B28
    id: iudirna_teplota3
    unit_of_measurement: "°C"  

# DYMBOX
  - platform: dallas
    name: "iudirna_dallas_teplota4"
    address: 0xB9012157542A0228
    id: iudirna_teplota4
    unit_of_measurement: "°C"  

# UPTIME - doba uzeni
  - platform: uptime
    id: iudirna_up_time
    name: "iUdirna_time"
    update_interval: 60s
    filters:
      - lambda: return x / 3600;
    unit_of_measurement: "h"
     
###############################################################    
#####  TLACITKA  ##############################################

binary_sensor:

# LED KONTROLKA SVETLO VNITRNI 
  - platform: gpio
    name: "iudirna_button8_svetlo_vnitrni"
    pin:
      mcp23xxx: mcp23017_hub
      number: 9
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 10ms
    on_press:
    - switch.toggle: iudirna_svetlo_in_template
    
# LED KONTROLKA SVETLO VENKOVNI    
  - platform: gpio
    name: "iudirna_button9_svetlo_venkovni"
    pin:
      mcp23xxx: mcp23017_hub
      number: 8 
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 10ms
    on_press:
    - switch.toggle: iudirna_svetlo_out_template
    
# TLACITKO TERMOSTAT CILOVA TEPLOTA +1°C   
  - platform: gpio
    name: "iudirna_button10_set_temp_up"
    pin:
      mcp23xxx: mcp23017_hub
      number: 10
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 10ms
    on_press:
      then:
        - climate.control:
            id: pid_climate
            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature + 1.0);

# TLACITKO TERMOSTAT CILOVA TEPLOTA -1°C
  - platform: gpio
    name: "iudirna_button11_set_temp_down"
    pin:
      mcp23xxx: mcp23017_hub
      number: 11
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 10ms
    on_press:
      then:
        - climate.control:
            id: pid_climate
            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature - 1.0);
      
###############################################################          
#####  RELE  ##################################################

switch:

# RELE2 - vnitrni svetlo
  - platform: gpio
    pin: GPIO12
    id: iudirna_svetlo_in
  - platform: template # COOLDOWN SWITCH OFF
    name: "iudirna_rele2_svetlo_vnitrni"
    id: iudirna_svetlo_in_template
    turn_on_action:
      if:
        condition:
          - switch.is_off: iudirna_svetlo_in
        then:
          - switch.turn_on: iudirna_svetlo_in
          - switch.turn_on: iudirna_led1_vnitrni_svetlo
        else:
          - switch.turn_off: iudirna_svetlo_in
          - switch.turn_off: iudirna_led1_vnitrni_svetlo   
          
# RELE3 - venkovni svetlo    
  - platform: gpio
    pin: GPIO14
    id: iudirna_svetlo_out
  - platform: template # COOLDOWN SWITCH OFF
    name: "iudirna_rele3_svetlo_venkovni"
    id: iudirna_svetlo_out_template
    turn_on_action:
      if:
        condition:
          - switch.is_off: iudirna_svetlo_out
        then:
          - switch.turn_on: iudirna_svetlo_out
          - switch.turn_on: iudirna_led2_venkovni_svetlo
        else:
          - switch.turn_off: iudirna_svetlo_out
          - switch.turn_off: iudirna_led2_venkovni_svetlo 
          
# RELE4 - NEPOUZITO  
#  - platform: gpio
#    pin: GPIO13
#    name: iudirna_rele4_free
#    id: iudirna_rele4   

###############################################################
#####  LED KONTROLKY  #########################################

  # LED1 - kontrolka vnitrni svetlo - zluta
  - platform: gpio      
    name: "iudirna_led1_vnitrni_svetlo"
    id: "iudirna_led1_vnitrni_svetlo"
    pin:
      mcp23xxx: mcp23017_hub
      number: 1
      mode:
        output: true
        pullup: false
      inverted: false 

  # LED2 - kontrolka venkovni svetlo - zluta
  - platform: gpio      
    name: "iudirna_led2_venkovni_svetlo"
    id: "iudirna_led2_venkovni_svetlo"
    pin:
      mcp23xxx: mcp23017_hub
      number: 2
      mode:
        output: true
        pullup: false
      inverted: false 
      
  # LED3 - WIFI status - modra
  - platform: gpio      
    name: "iudirna_led3_wifi_status"
    id: "iudirna_led3_wifi_status"
    pin:
      mcp23xxx: mcp23017_hub
      number: 3
      mode:
        output: true
        pullup: false
      inverted: false 

###############################################################
#####  PID AUTOTUNE  ##########################################

# PID - autotune    
  - platform: template
    name: "iUdirna PID Climate Autotune"
    turn_on_action:
      - climate.pid.autotune: pid_climate
      
# PID - PWM output     
output:
  - platform: slow_pwm
    pin: GPIO16
    id: heater
    period: 30s
  
```
# Galerie

