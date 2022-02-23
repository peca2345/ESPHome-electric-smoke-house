## ESPHome - smoke house /w smokebox


# Description:

This is a tutorial on automating a smokehouse with a smoke generator.

Features:

-PID thermal regulation

-measurement of smoke, smokehouse and internal meat temperature

-auto fan/fire speed control

-auto vibrator (improve wood chip burning)

-WIFI/cloud control (via Home Assistant server)

-LCD display

-manual control buttons

-fuel: wood chips

# Required knowledge:

Home Assistant: [YTB](https://www.youtube.com/watch?v=u_aKcf_F1MM) 

ESPHome:        [YTB](https://www.youtube.com/watch?v=mj-24SZLQKk) 

Soldering:      [YTB](https://www.youtube.com/watch?v=6rmErwU5E-k) 

# Used components:

[ESP8266 4CH relay board](https://www.aliexpress.com/item/32948585494.html?dp=608841a56be8eefc6c2eed35&cn=ah&aff_fcid=6b02168ff7f54818a786e9c21d401c2f-1640297845925-05877-_9IM1Y0&tt=CPS_NORMAL&aff_fsk=_9IM1Y0&aff_platform=portals-tool&sk=_9IM1Y0&aff_trace_key=6b02168ff7f54818a786e9c21d401c2f-1640297845925-05877-_9IM1Y0&terminal_id=d7554e9ae9fe4e76878e8694ca0d1dcc)

![This is an image](/assets/images/esp12f_4ch_board.png)

[SSR rele](https://shorturl.at/ltyCD)

[SSR rele chladič V1](https://shorturl.at/nxMNQ)

[SSR rele chladič V2](https://shorturl.at/pLQYZ)

[LCD displej](https://shorturl.at/jyRVX)

https://dratek.cz/arduino/1753-linearni-regulator-napeti-dc-lm317.html

# Schema:


# Gallery

https://photos.app.goo.gl/iQ432f7vACJFKvrUA

# 3D print model:


# ESPHome code
```
esphome:
  name: iudirna
  platform: esp8266
  board: esp12e
  on_boot:
    then:
      - switch.turn_off: fan_auto
      - switch.turn_off: vibrator_auto
      - delay: 5s
      - climate.control:
          id: pid_climate
          mode: 'OFF'
          
logger:
  level: NONE # musi byt vypnuty aby se uvolnil uart pro wattmeter
  hardware_uart: UART1
  
api:
ota:
  password: "a025f35ef14fd7ed30fba26567e45e60"
  
wifi:
  networks:
  - ssid: !secret wifi_ssid
    password: !secret wifi_password 
  manual_ip:
    static_ip: 192.168.1.181
    gateway: 192.168.1.1
    subnet: 255.255.255.0  
    dns1: 192.168.1.1
    dns2: 1.1.1.1
    
###############################################################
###### PINOUT #################################################

# GPIO0   nepouzivat (slouzi pro boot)
# GPIO2   nepouzivat (slouzi pro boot)  
# GPIO9   nepouzivat (pamet)
# GPIO10  nepouzivat (pamet)
# GPIO15  nepouzivat (slouzi pro boot)


# ADC     FREE - analogovy vstup
# GPIO4   i2c sda (mcp23017, lcd)
# GPIO5   i2c scl (mcp23017, lcd)

# GPIO12  dallas                         
# GPIO13  PWM controller fan_output (nejde pres expander)           
# GPIO14  PWM controller vibrator_output (nejde pres expander)    
# GPIO16  SSR rele id: heater (nejde pres expander)                 

#multiplexer MCP23017:
## pin A0-A3 cesta pod deskou pres odpor a zvlast dupont vystup vpravo nahore
#mcp23xxx  pin0   A0  OUT  LED iudirna_led0_termostat             
#mcp23xxx  pin1   A1  OUT  LED iudirna_led2_venkovni_svetlo         
#mcp23xxx  pin2   A2  OUT  LED iudirna_led1_vnitrni_svetlo          
#mcp23xxx  pin3   A3  OUT  LED iudirna_led3_wifi_status          
#mcp23xxx  pin4   A4  OUT  FREE
#mcp23xxx  pin5   A5  IN   BTN iudirna_button5_termostat                                     
#mcp23xxx  pin6   A6  OUT  RELE iudirna_svetlo_in                 
#mcp23xxx  pin7   A7  OUT  RELE iudirna_svetlo_out                

#mcp23xxx  pin8   B0  IN   iudirna_button9_svetlo_venkovni
#mcp23xxx  pin9   B1  IN   iudirna_button8_svetlo_vnitrni    
#mcp23xxx  pin10  B2  IN   iudirna_button10_set_temp_up
#mcp23xxx  pin11  B3  IN   iudirna_button11_set_temp_down
#mcp23xxx  pin12  B4  IN   iudirna_button12_set_fan_up             
#mcp23xxx  pin13  B5  IN   iudirna_button13_set_fan_down           
#mcp23xxx  pin14  B6  IN   iudirna_button14_fan_auto            
#mcp23xxx  pin15  B7  IN   iudirna_button15_vibrator_auto       

###############################################################
###### FUNKCE #################################################

# PREDNI STRANA:
# tlacitko1   +               kratke: +1°C termostat   /   dlouhe: +1 rychlost FANu
# tlacitko2   -               kratke: -1°C termostat   /   dlouhe: -1 rychlost FANu
# tlacitko3   spirala         manualni ovladani spiraly (nejde ovladat pres WIFI)
# tlacitko4   AUTO FAN        kratke: zapne automatickou regulaci FANu
# tlacitko5   AUTO VIBRATOR   kratke: zapne automatickou regulaci vibratoru  
# tlacitko6   svetlo IN       kratke: zapne svetlo uvnitr
# tlacitko7   svetlo OUT      kratke: zapne svetlo venku

# ZADNI STRANA:
# tlacitko1   VIB. MAN        kratke: manualni zap/vyp vibratoru na 5s
# tlacitko2   TIME            kratke: reset doby uzeni

###########################################################

########################################################### 
    
uart: # pro wattmeter pzem004t (musi byt vypnuty logger aby se uvolnil uart)
  rx_pin: GPIO3
  tx_pin: GPIO1
  baud_rate: 9600
  
i2c: # pro dallas, lcd, mcp23017
  sda: GPIO4
  scl: GPIO5
  scan: false # pokud neznas adresy i2c zarizeni tak povol scan (true) a z logu vycti ID 
  
mcp23017:
  - id: 'mcp23017_hub'
    address: 0x20 # zjisteno z logu s povolenym scanem i2c

dallas: 
  - pin: GPIO12
    update_interval: 5s
    
###########################################################

########################################################### LCD DISPLEJ

display: #////////////////////////////////////////////////////////////////////  DISPLAY
  - platform: lcd_pcf8574 #display
    dimensions: 20x4
    address: 0x27
    lambda: |-
      it.printf(0, 0, "Udirna: %.0f", id(iudirna_teplota1).state);
      it.printf(12, 0, "T=%.0f", id(pid_climate).target_temperature_low);
      it.printf(17, 0, "%s", id(pid_mode).state ? "ON" : "OFF");
      it.printf(0, 1, "Maso 1: %.0f", id(iudirna_teplota2).state);
      it.printf(11, 1, " F=%.0f", id(fan_speed).state);
      it.printf(17, 1, "%s", id(fan_auto).state ? "AUT" : "MAN");
      it.printf(0, 2, "Maso 2: %.0f", id(iudirna_teplota3).state);
      it.printf(11, 2, " V=%.0f", id(vibrator_speed).state);
      it.printf(17, 2, "%s", id(vibrator_auto).state ? "AUT" : "MAN"); 
      it.printf(0, 3, "Dymbox: %.0f", id(iudirna_teplota4).state);
      it.printf(11, 3, " H=%.0f", id(iudirna_up_time).state); 

###########################################################

########################################################### TERMOSTAT PID

climate: #////////////////////////////////////////////////////////////////////  CLIMATE

  # PID TERMOSTAT
  - platform: pid # climate
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
      kp: 0.22224  
      ki: 0.00067  
      kd: 18.41252 
      # pro funkcni autotune nastav kp,ki,kd: 0 
      # po dokonceni autotune prepis hodnotama z logu
      
###########################################################
sensor: #////////////////////////////////////////////////////////////////////// SENSOR
########################################################### WATTMETER

  # WATTMETER PZEM004T   
  - platform: pzemac
    update_interval: 10s
    current:
      name: "iudirna_pzem_proud"
    voltage:
      name: "iudirna_pzem_napeti"
    energy:
      name: "iudirna_pzem_spotreba"
    power:
      name: "iudirna_pzem_prikon"
      id: pzem_energy
    frequency:
      name: "iudirna_pzem_frekvence"
    power_factor:
      name: "iudirna_pzem_ucinik"

###########################################################

########################################################### PID AUTOTUNE

  # PID HEAT SENSOR (PRO GRAFANU)
  - platform: pid #sensor 
    name: "iUdirna PID Climate HEAT"
    type: HEAT
# PID SENZORY NIZE POVOLIT JEN PRI AUTOTUNE #
#  - platform: pid #sensor
#    name: "iUdirna PID Climate Result"
#    type: RESULT
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate PROPORTIONAL"
#    type: PROPORTIONAL 
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate INTEGRAL"
#    type: INTEGRAL
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate DERIVATIVE"
#    type: DERIVATIVE
#    climate_id: pid_climate
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate COOL"
#    type: COOL
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate KP"
#    type: KP
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate KI"
#    type: KI
#    climate_id: pid_climate
#  - platform: pid #sensor
#    name: "iUdirna PID Climate KD"
#    type: KD
#    climate_id: pid_climate
# PID SENZORY VYSE POVOLIT JEN PRI AUTOTUNE #

###########################################################

########################################################### TEPLOMERY

  # UDIRNA
  - platform: dallas #sensor 
    name: "iudirna_dallas_teplota1"
    address: 0xC106219415E16428 
    id: iudirna_teplota1
    unit_of_measurement: "°C"  
    ## adresa zjistena z logu s povolenym scanem i2c
    
  # MASO1
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota2"
    address: 0xBE06219419160728 
    id: iudirna_teplota2
    unit_of_measurement: "°C" 
    ## adresa zjistena z logu s povolenym scanem i2c
    
  # MASO2
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota3"
    address: 0xB101215744DA0B28 
    id: iudirna_teplota3
    unit_of_measurement: "°C"  
    ## adresa zjistena z logu s povolenym scanem i2c
    
  # DYMBOX
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota4"
    address: 0xB9012157542A0228 
    id: iudirna_teplota4
    unit_of_measurement: "°C"  
    ## adresa zjistena z logu s povolenym scanem i2c
    
    # on_value_range:
    #   above: 14 # pri prekroceni dane teploty se spusti automatizace pro vibrator
    #   then:
    #     - switch.turn_on: vibrator_wait_temp # povoli dalsi podminku pro auto vibrator
    
###########################################################

########################################################### UPTIME SENSOR

  # UPTIME - doba uzeni
  - platform: uptime #sensor
    id: iudirna_up_time
    name: "iudirna_up_time2"
    update_interval: 60s
    filters:
      - lambda: return x / 3600;
    unit_of_measurement: "h"
    
###########################################################

########################################################### FAN + VIB + PID SENSOR

  # PID MODE LAMBDA PRO ZOBRAZENI NA LCD (ziska stav termostatu - heat/off)
  - platform: template #sensor
    id: "pid_mode"
    name: iudirna_pid_mode
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(pid_climate).mode;
      
  # FAN SPEED LAMBDA PRO ZOBRAZENI NA LCD (ziska stupen rychlosti)
  - platform: template #sensor
    id: "fan_speed"
    name: iudirna_fan_speed
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(fan_pwm).speed;
      
  # FAN SPEED LAMBDA PRO ZOBRAZENI NA LCD (ziska stupen rychlosti)
  - platform: template #sensor
    id: "vibrator_speed"
    name: "iudirna_vibrator_speed"
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(vibrator_pwm).speed;
      
###########################################################

########################################################### WIFI SIGNAL  

  # WIFI SIGNAL SENSOR
  - platform: wifi_signal #sensor
    name: "iudirna_signal_wifi2"
    update_interval: 10s     
    
interval: #//////////////////////////////////////////////////////////////////// INTERVAL

  # WIFI SIGNAL - KONTROLA SPOJENI A OVLADANI LED KONTROLKY
  - interval: 1s
    then:
      if:
        condition:
          wifi.connected:
        then:
          - switch.turn_on: iudirna_led3_wifi_status
        else:
          - switch.turn_off: iudirna_led3_wifi_status
          
###########################################################

########################################################### TERMOSTAT - LED

  # LED KONTOLKA TERMOSTATU
  - interval: 100ms
    then:
      if:
        condition: 
          - sensor.in_range:
              id: pid_mode
              above: 1
              below: 5
        then:
          - switch.turn_on: iudirna_led0_termostat
        else:
          - switch.turn_off: iudirna_led0_termostat
          
########################################################### 

########################################################### FAN AUTO 

  # FAN AUTO - zmen teploty a rychlosti dle libosti
  - interval: 10min
    id: interval_fan_auto
    then:
      if:
        condition:
          and:
            - switch.is_on: fan_auto
            - sensor.in_range:
                 id: iudirna_teplota4
                 above: -20 # vyse
                 below: 30  # nize
        then:
          - fan.turn_on:
              id: fan_pwm
              speed: !lambda return (id(fan_pwm).speed = 7.0); # rychlost
        else:               
          if:
            condition:
              and:
                - switch.is_on: fan_auto
                - sensor.in_range:
                     id: iudirna_teplota4
                     above: 30 # vyse
                     below: 50 # nize
            then:
              - fan.turn_on:
                  id: fan_pwm
                  speed: !lambda return (id(fan_pwm).speed = 5.0); # rychlost           
            else:
              if:
                condition:
                  and:
                    - switch.is_on: fan_auto
                    - sensor.in_range:
                        id: iudirna_teplota4
                        above: 55 # vyse
                        below: 80 # nize
                then:
                  - fan.turn_on:
                      id: fan_pwm
                      speed: !lambda return (id(fan_pwm).speed = 3.0); # rychlost   
                      
###########################################################

########################################################### VIBRATOR AUTO

  # VIBRATOR AUTO                    
  - interval: 10min
    id: interval_vibrator_auto
    then:
      if:
        condition:
          and:
            - switch.is_on: vibrator_auto # podminka ktera se spusti manualne tlacitkem
            - sensor.in_range:
                id: iudirna_teplota4
                above: 0  # vyse
                below: 30 # nize
        then:
          - fan.turn_on:
              id: vibrator_pwm
              speed: !lambda return (id(vibrator_pwm).speed = 10.0);  # spusti vibrator na max
          - delay: 3s # ceka 3s
          - fan.turn_on:
              id: vibrator_pwm
              speed: !lambda return (id(vibrator_pwm).speed = 1.0); # snizi rychlost (1=OFF)
          - fan.turn_off: # vypne vibrator
              id: vibrator_pwm
        else:
          if:
            condition:
              and:
                - switch.is_on: vibrator_wait_temp # podminka ktera se povoli az po dosazeni teploty od startu
                - sensor.in_range:
                    id: iudirna_teplota4
                    above: 0  # vyse
                    below: 30 # nize
            then:
              - fan.turn_on:
                  id: vibrator_pwm
                  speed: !lambda return (id(vibrator_pwm).speed = 10.0); # spusti vibrator na max
              - delay: 3s # ceka 3s
              - fan.turn_on:
                  id: vibrator_pwm
                  speed: !lambda return (id(vibrator_pwm).speed = 1.0); # snizi rychlost (1=OFF)
              - fan.turn_off: # vypne vibrator
                  id: vibrator_pwm 
                  
binary_sensor: #//////////////////////////////////////////////////////////////// BINARY_SENSOR                  
                  
###########################################################

########################################################### TLACITKA

  # TLACITKO TERMOSTAT 
  - platform: gpio #binary_sensor
    id: "iudirna_button5_termostat"
    pin:
      mcp23xxx: mcp23017_hub
      number: 5
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 20ms
    on_press:
      - switch.toggle: pid_switch
              
  # TLACITKO SVETLO VNITRNI 
  - platform: gpio #binary_sensor
    id: "iudirna_button8_svetlo_vnitrni"
    pin:
      mcp23xxx: mcp23017_hub
      number: 9
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 20ms
    on_press:
    - switch.toggle: iudirna_svetlo_in
    
  # TLACITKO SVETLO VENKOVNI    
  - platform: gpio #binary_sensor 
    id: "iudirna_button9_svetlo_venkovni"
    pin:
      mcp23xxx: mcp23017_hub
      number: 8 
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 20ms
    on_press:
    - switch.toggle: iudirna_svetlo_out
    
  # TLACITKO TERMOSTAT CILOVA TEPLOTA +1°C   
  - platform: gpio #binary_sensor
    id: "iudirna_button10_set_temp_up"
    pin:
      mcp23xxx: mcp23017_hub
      number: 10
      mode:
        input: true
        pullup: true
      inverted: true
    on_click:
    - min_length: 20ms
      max_length: 500ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature + 1.0);
    - min_length: 500ms
      max_length: 2000ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature + 5.0);    
    - min_length: 2000ms
      max_length: 6000ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature = 65.0);   
            
  # TLACITKO TERMOSTAT CILOVA TEPLOTA -1°C
  - platform: gpio #binary_sensor
    id: "iudirna_button11_set_temp_down"
    pin:
      mcp23xxx: mcp23017_hub
      number: 11
      mode:
        input: true
        pullup: true
      inverted: true
    on_click:
    - min_length: 20ms
      max_length: 500ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature - 1.0);
    - min_length: 500ms
      max_length: 2000ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature - 5.0);
    - min_length: 2000ms
      max_length: 6000ms
      then:
        - climate.control:
            id: pid_climate
#            mode: 'HEAT'
            target_temperature: !lambda return (id(pid_climate).target_temperature = 0.0);
            
  # TLACITKO FAN SPEED +1
  - platform: gpio #binary_sensor
    id: iudirna_button12_set_fan_up
    pin:
      mcp23xxx: mcp23017_hub
      number: 12
      mode:
        input: true
        pullup: true
      inverted: true
    on_click:
    - min_length: 20ms
      max_length: 500ms
      then:
        - fan.turn_on:
            id: fan_pwm
            speed: !lambda return (id(fan_pwm).speed + 1.0);
    - min_length: 500ms
      max_length: 3s
      then:
        - fan.turn_on:
            id: fan_pwm
            speed: !lambda return (id(fan_pwm).speed = 10.0);
            
  # TLACITKO FAN SPEED -1
  - platform: gpio #binary_sensor
    id: iudirna_button13_set_fan_down
    pin:
      mcp23xxx: mcp23017_hub
      number: 13
      mode:
        input: true
        pullup: true
      inverted: true
    on_click:
    - min_length: 20ms
      max_length: 500ms
      then:
      - if:
          condition:
            or:
              - lambda: 'return id(fan_pwm).speed == 1.0;'
              - lambda: 'return id(fan_pwm).speed == 2.0;'
          then:
            - fan.turn_on:
                id: fan_pwm
                speed: !lambda return (id(fan_pwm).speed - 1.0);
            - fan.turn_off:
                id: fan_pwm
          else:
            - fan.turn_on:
                id: fan_pwm
                speed: !lambda return (id(fan_pwm).speed - 1.0);  
    - min_length: 500ms
      max_length: 3s
      then:
        - fan.turn_on:
            id: fan_pwm
            speed: !lambda return (id(fan_pwm).speed = 1.0);
        - fan.turn_off:
            id: fan_pwm
            
  # TLACITKO FAN AUTO 
  - platform: gpio #binary_sensor
    id: iudirna_button14_fan_auto
    pin:
      mcp23xxx: mcp23017_hub
      number: 14 # B6
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
        - delayed_on: 20ms
    on_press:
      then:
        - switch.toggle: fan_auto
        
  # TLACITKO VIBRATOR AUTO 
  - platform: gpio #binary_sensor
    id: iudirna_button15_vibrator_auto
    pin:
      mcp23xxx: mcp23017_hub
      number: 15 # B7
      mode:
        input: true
        pullup: true
      inverted: true
    on_click:
    - min_length: 20ms
      max_length: 500ms
      then:
        - switch.toggle: vibrator_auto
    - min_length: 500ms
      max_length: 5s
      then:
        - fan.turn_on:
            id: vibrator_pwm
            speed: !lambda return (id(vibrator_pwm).speed = 10.0);
        - delay: 3s
        - fan.turn_on:
            id: vibrator_pwm
            speed: !lambda return (id(vibrator_pwm).speed = 1.0);
        - fan.turn_off:
            id: vibrator_pwm
            
########################################################### 
fan: #////////////////////////////////////////////////////////////////////////// FAN
########################################################### FAN PWM OUTPUT

  # FAN PWM SPEED
  - platform: speed #fan
    output: fan_output
    id: fan_pwm
    name: "iudirna_fan_pwm_switch"
    speed_count: 10
    restore_mode: ALWAYS_OFF
    
  # VIBRATOR PWM SPEED
  - platform: speed
    output: vibrator_output
    id: vibrator_pwm
    name: "iudirna_vibrator_pwm_switch"
    speed_count: 10
    restore_mode: ALWAYS_OFF
    
output: #/////////////////////////////////////////////////////////////////////// OUTPUT 

  # FAN PWM OUTPUT
  - platform: esp8266_pwm #output
    pin: GPIO13
    frequency: 80 Hz # 12V FAN 5015
    id: fan_output
    min_power: 0
    max_power: 0.7


    
  # VIBRATOR PWM OUTPUT
  - platform: esp8266_pwm
    pin: GPIO14
    frequency: 600 Hz # 1.5-6V vibrator
    id: vibrator_output
    max_power: 0.5 # 1=12V 0.5=6V
    
########################################################### 

########################################################### PID AUTOTUNE

  # PID - PWM output     
  - platform: slow_pwm #output
    pin: GPIO16
    id: heater
    period: 30s
    
switch: #/////////////////////////////////////////////////////////////////////// SWITCH  

  # PID - autotune    
  - platform: template #switch
    name: "iUdirna PID Climate Autotune"
    turn_on_action:
      - climate.pid.autotune: pid_climate
      
########################################################### 

########################################################### RELE

  # FAN AUTO - povoluje podminku intervalu automaticke regulace
  - platform: template
    name: "iudirna_fan_auto"
    id: fan_auto
    optimistic: true
    
  # VIBRATOR AUTO - povoluje podminku intervalu automaticke regulace tlacitkem
  - platform: template
    name: "iudirna_vibrator_auto"
    id: vibrator_auto
    optimistic: true
    turn_off_action: 
      - switch.turn_off: vibrator_wait_temp # pri manualnim vypnuti tlacitkem vypne i podminku wait_temp
      - fan.turn_on: # nastavi rychlost na minimum (1= vypnuto)
          id: vibrator_pwm
          speed: !lambda return (id(vibrator_pwm).speed = 1.0);
      - fan.turn_off: # a vypne vibrator
          id: vibrator_pwm 
          
  # VIBRATOR AUTO WAIT FOR TEMP               # podminka intervalu automaticke regulace 
  - platform: template                      # az po dosazeni dane teploty od startu ho zapne teplomer
    name: "iudirna_vibrator_wait_temp"      # tim se zajisti ze nebude vibrovat od zacatku pri roztapeni ale az po dosazeni urcene teploty
    id: vibrator_wait_temp
    optimistic: true
    
  # RELE2 - vnitrni svetlo
  - platform: gpio #switch
    id: iudirna_svetlo_in
    name: "iudirna_rele2_svetlo_vnitrni"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      inverted: false
      number: 6
      mode:
        output: true
        pullup: false
    on_turn_on:
        - switch.turn_on: iudirna_led1_vnitrni_svetlo
    on_turn_off:
        - switch.turn_off: iudirna_led1_vnitrni_svetlo
        
  # RELE3 - venkovni svetlo    
  - platform: gpio #switch
    id: iudirna_svetlo_out
    name: "iudirna_rele3_svetlo_venkovni"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      inverted: false
      number: 7
      mode:
        output: true
        pullup: false
    on_turn_on:
        - switch.turn_on: iudirna_led2_venkovni_svetlo
    on_turn_off:
        - switch.turn_off: iudirna_led2_venkovni_svetlo 
        
########################################################### 

########################################################### LED KONTROLKY

  # LED1 - kontrolka vnitrni svetlo - zluta
  - platform: gpio #switch     
    id: "iudirna_led0_termostat"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      number: 0 # A0
      mode:
        output: true
        pullup: false
      inverted: false 
      
  # LED1 - kontrolka vnitrni svetlo - zluta
  - platform: gpio #switch     
    id: "iudirna_led1_vnitrni_svetlo"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      number: 2 # A2
      mode:
        output: true
        pullup: false
      inverted: false 
      
  # LED2 - kontrolka venkovni svetlo - zluta
  - platform: gpio #switch     
    id: "iudirna_led2_venkovni_svetlo"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      number: 1 # A1
      mode:
        output: true
        pullup: false
      inverted: false 
      
  # LED3 - WIFI status - modra
  - platform: gpio #switch     
    id: "iudirna_led3_wifi_status"
    restore_mode: ALWAYS_OFF
    pin:
      mcp23xxx: mcp23017_hub
      number: 3 # A3
      mode:
        output: true
        pullup: false
      inverted: false 
      
      
      
  - platform: template                   
    id: pid_switch
    turn_on_action:
      if:
        condition:
          - sensor.in_range:
              id: pid_mode # 0=OFF 3=HEAT
              above: 1 # => ON
              below: 5 # => ON
        then:
          - climate.control:
              id: pid_climate
              mode: 'OFF'
        else:
          - climate.control:
              id: pid_climate
              mode: 'HEAT'
      

      
      
```

