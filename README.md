# ESPHome - electric smoke house and smokebox

This is a tutorial on automating a smokehouse with a smoke generator.

## Description:

Features:

- PID thermal regulation
- measurement of smoke, smokehouse and internal meat temperature
- fan automation - speed control according to smoke temperature
- vibrator automation - when the smoke drops, it vibrates with the wood chips for better combustion
- WIFI/cloud control (via Home Assistant server) + android or google home notification
- wattmeter
- LCD display
- manual control buttons
- fuel: wood chips

Button function:
- TEMP UP: target temp. +1°C (long press +5°C and very long press set thermostat to 65°C)
- TEMP DOWN: target temp. -1°C (long press -5°C and very long press set thermostat to 0°C)
- THERMOSTAT: ON/OFF thermostat
- LIGHT IN: ON/OFF indoor light
- LIGHT OUT: ON/OFF outdoor light
- AUT. VIB: ON/OFF vibrator automation (vibrates with the chip at low smoke temperature)
- AUT. FAN: ON/OFF fan automation (speed control according to smoke temperature)
- FAN UP: +10% speed (long press max. speed)
- FAN DOWN: -10% speed (long press OFF)

## Required knowledge:

- Home Assistant: [YTB](https://www.youtube.com/watch?v=u_aKcf_F1MM) 

- ESPHome:        [YTB](https://www.youtube.com/watch?v=mj-24SZLQKk) 

- Grafana:        [YTB](https://www.youtube.com/watch?v=rKIHoqxxMek) 

- Soldering:      [YTB](https://www.youtube.com/watch?v=6rmErwU5E-k) 

## Used components:

- ESP12F 4CH 1pcs: [ALI](https://www.aliexpress.com/item/1005001651180110.html?spm=a2g0o.productlist.0.0.3753351fOHL2kh&algo_pvid=da105da4-7014-4b55-bb39-96ff547ae06c&algo_exp_id=da105da4-7014-4b55-bb39-96ff547ae06c-13&pdp_ext_f=%7B%22sku_id%22%3A%2212000017011631427%22%7D&pdp_pi=-1%3B167.83%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- MCP23017 1pcs: [ALI](https://www.aliexpress.com/item/1005003141912496.html?spm=a2g0o.productlist.0.0.1ed148dasW0v52&algo_pvid=2d82d3bc-dff2-4938-b175-366b837c4bcd&algo_exp_id=2d82d3bc-dff2-4938-b175-366b837c4bcd-30&pdp_ext_f=%7B%22sku_id%22%3A%2212000024317936226%22%7D&pdp_pi=-1%3B79.81%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- SSR DIN 25A 1pcs: [ALI](faaf-47be-8fb2-69755da82c57-0&pdp_ext_f=%7B%22sku_id%22%3A%2212000027001195887%22%7D&pdp_pi=-1%3B297.08%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- PWM driver 2pcs: [ALI](https://www.aliexpress.com/item/1005001902539055.html?spm=a2g0o.productlist.0.0.55aa25c2GnqXDh&algo_pvid=16b38bb6-1d2d-4fd0-b4b6-a7989aac81fe&algo_exp_id=16b38bb6-1d2d-4fd0-b4b6-a7989aac81fe-45&pdp_ext_f=%7B%22sku_id%22%3A%2212000018072058627%22%7D&pdp_pi=-1%3B24.39%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Buttons 9pcs: [ALI](https://www.aliexpress.com/item/32850618919.html?spm=a2g0o.productlist.0.0.5d872a47Yax7qv&algo_pvid=6443c32b-9e2f-425b-8ad9-328703a715bd&algo_exp_id=6443c32b-9e2f-425b-8ad9-328703a715bd-42&pdp_ext_f=%7B%22sku_id%22%3A%2265177915549%22%7D&pdp_pi=-1%3B37.69%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- LCD 20x4 5V 1pcs: [ALI](https://www.aliexpress.com/item/4001135515638.html?spm=a2g0o.productlist.0.0.70094d0fWGY9Jz&algo_pvid=392c7a21-9633-491f-82bb-b0336551b1ff&algo_exp_id=392c7a21-9633-491f-82bb-b0336551b1ff-2&pdp_ext_f=%7B%22sku_id%22%3A%2210000014747778203%22%7D&pdp_pi=-1%3B89.57%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- AC-DC 12V 3.3A MD-40-12 DIN 1pcs: [ALI](https://www.aliexpress.com/item/1005002024975538.html?spm=a2g0o.productlist.0.0.5b66639bc7g2C6&algo_pvid=29039fdf-9abb-480c-a1ec-fdcd6659b322&aem_p4p_detail=2022022304181914498879841991630001808887&algo_exp_id=29039fdf-9abb-480c-a1ec-fdcd6659b322-0&pdp_ext_f=%7B%22sku_id%22%3A%2212000018453302440%22%7D&pdp_pi=-1%3B380.44%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Switch AC 250V 16A 2pcs: [ALI](https://www.aliexpress.com/item/32806585772.html?spm=a2g0o.productlist.0.0.4c533cadEbQ4TY&algo_pvid=42fad9ed-7d3e-41f7-be61-bb7bcfac7a62&algo_exp_id=42fad9ed-7d3e-41f7-be61-bb7bcfac7a62-35&pdp_ext_f=%7B%22sku_id%22%3A%2264444197255%22%7D&pdp_pi=-1%3B27.27%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Dallas temp. Sensor 4pcs: [ALI](https://www.aliexpress.com/item/32839776524.html) 

- FAN 5025 12V 6200rpm 1pcs: [ALI](https://www.aliexpress.com/item/33049767300.html?spm=a2g0o.order_list.0.0.21ef1802FV0N98) 

- Vibrator DC 1.5-6V 22400RPM 1pcs: [ALI](https://www.aliexpress.com/item/1005003309418737.html?spm=a2g0o.order_list.0.0.21ef1802FV0N98) 

- Wattmeter PZEM-004T 100A 1pcs: [ALI](https://www.aliexpress.com/item/33043137964.html?spm=a2g0o.productlist.0.0.39996e27jijA6Q&algo_pvid=1952f64e-d59d-4ee7-b973-b8eb7d3aefad&algo_exp_id=1952f64e-d59d-4ee7-b973-b8eb7d3aefad-39&pdp_ext_f=%7B%22sku_id%22%3A%2267438540147%22%7D&pdp_pi=-1%3B318.14%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Heater 2600W 230V 1pcs: [ALI](https://www.aliexpress.com/item/1005003584466174.html?spm=a2g0o.detail.1000014.9.6a92713dVDrXSB&gps-id=pcDetailBottomMoreOtherSeller&scm=1007.13338.177756.0&scm_id=1007.13338.177756.0&scm-url=1007.13338.177756.0&pvid=0e2cb83b-8521-45df-9570-7829ea86d0f4&_t=gps-id:pcDetailBottomMoreOtherSeller,scm-url:1007.13338.177756.0,pvid:0e2cb83b-8521-45df-9570-7829ea86d0f4,tpp_buckets:668%232846%238108%231977&pdp_ext_f=%257B%2522sku_id%2522%253A%252212000027300025510%2522%252C%2522sceneId%2522%253A%252230050%2522%257D&pdp_pi=-1%253B603.47%253B-1%253B-1%2540salePrice%253BCZK%253Brecommend-recommend) 

- LED diode set 1pcs: [ALI](https://www.aliexpress.com/item/1005003323707856.html?dp=61e453ee0fa2025c4ba43400&cn=ah&aff_fcid=bd9ed3937ddf4288850b01c823d9a1d7-1645625483275-03644-_d6jWDbY&aff_fsk=_d6jWDbY&aff_platform=link-c-tool&sk=_d6jWDbY&aff_trace_key=bd9ed3937ddf4288850b01c823d9a1d7-1645625483275-03644-_d6jWDbY&terminal_id=f09dc59f89d1480fb0cfa10490f657c7&afSmartRedirect=n) 

- Resistors set 1pcs: [ALI](https://www.aliexpress.com/item/1005002631550177.html?spm=a2g0o.productlist.0.0.1ed36405DfJzoB&algo_pvid=91b60b6b-d8f3-4351-9d1b-b23bf0ba2b69&aem_p4p_detail=202202230706528513372260889730002489868&algo_exp_id=91b60b6b-d8f3-4351-9d1b-b23bf0ba2b69-0&pdp_ext_f=%7B%22sku_id%22%3A%2212000021480015802%22%7D&pdp_pi=-1%3B64.29%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- LED light outdoor 20W 230V 1pcs: [ALI](https://www.aliexpress.com/item/4001183488400.html?spm=a2g0o.productlist.0.0.630446b6KVFPmV&algo_pvid=2f0e5a2a-f81a-4789-89be-66a6cdb0ea9a&algo_exp_id=2f0e5a2a-f81a-4789-89be-66a6cdb0ea9a-50&pdp_ext_f=%7B%22sku_id%22%3A%2212000027495977514%22%7D&pdp_pi=-1%3B264.27%3B-1%3B-1%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Oven light 25W 230V 1pcs: [ALI](https://www.aliexpress.com/item/1005001265388691.html?spm=a2g0o.productlist.0.0.66ad92e8N4pHp6&algo_pvid=4f47a72a-3b68-4385-8d38-53bfc0c93ff2&algo_exp_id=4f47a72a-3b68-4385-8d38-53bfc0c93ff2-2&pdp_ext_f=%7B%22sku_id%22%3A%2212000015518228734%22%7D&pdp_pi=-1%3B17.74%3B-1%3B1751%40salePrice%3BCZK%3Bsearch-mainSearch) 

- Oven E14 holder/socket 1pcs: [ALI](https://www.aliexpress.com/item/1005002315411008.html?spm=a2g0o.productlist.0.0.13b1360dM2UuIt&algo_pvid=8430a2c1-8756-48be-ad43-43a0ccd8533b&algo_exp_id=8430a2c1-8756-48be-ad43-43a0ccd8533b-10&pdp_ext_f=%7B%22sku_id%22%3A%2212000020046504898%22%7D&pdp_pi=-1%3B37.47%3B-1%3B3370%40salePrice%3BCZK%3Bsearch-mainSearch) 


## Schema:

![Schema](https://github.com/peca2345/ESPHome-electric-smoke-house/blob/main/SCHEMA_V5_FINAL.png?raw=true)

## Animation:

![LCD](https://github.com/peca2345/ESPHome-electric-smoke-house/blob/main/animation.gif?raw=true)

## LCD:

![LCD](https://github.com/peca2345/ESPHome-electric-smoke-house/blob/main/LCD3.png?raw=true)

## Home Assistant: (web browser)

![HA](https://github.com/peca2345/ESPHome-electric-smoke-house/blob/main/HA2.png?raw=true)

## Grafana:

![Grafana](https://github.com/peca2345/ESPHome-udirna/blob/main/grafana.png?raw=true)

## Android notification:

![android](https://user-images.githubusercontent.com/58307338/155407713-74f10288-5d43-4d7d-bf97-64e25dd9b7d2.png)

## Gallery:

https://photos.app.goo.gl/iQ432f7vACJFKvrUA

## ESPHome code
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
  level: NONE # log must be NONE to release the wattmeter
  hardware_uart: UART1
  
api:
ota:
  password: "a025f35ef14fd7ed30fba24567e45e60"
             # USE YOUR KEY GENERATED BY ESPHOME
wifi:
  networks:
  - ssid: !secret wifi_ssid
    password: !secret wifi_password 

########################################################### 
    
uart: 
  rx_pin: GPIO3
  tx_pin: GPIO1
  baud_rate: 9600
  # for wattmeter pzem004t (the logger must be "NONE" to release the uart)
  
i2c: # for LCD, mcp23017
  sda: GPIO4
  scl: GPIO5
  scan: false
  # if you don't know the addresses of i2c devices, enable scan (true) and read the ID from the log
  
mcp23017:
  - id: 'mcp23017_hub'
    address: 0x20 
    # adress detected from log with i2c scan enabled
    
dallas: 
  - pin: GPIO12
    update_interval: 5s
    
########################################################### LCD DISPLAY

display: 
  - platform: lcd_pcf8574
    dimensions: 20x4
    address: # detected from log with i2c scan enabled
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


########################################################### THERMOSTAT PID

climate: 

  # PID TERMOSTAT
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
      kp: 0.22224  
      ki: 0.00067  
      kd: 18.41252 
      # for functional autotune set kp,ki,kd: 0 
      # overwrite with log values after autotune is complete
      

sensor:
########################################################### WATTMETER

  # WATTMETER PZEM004T - UART   
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

########################################################### PID AUTOTUNE

  # PID HEAT SENSOR (SENSOR FOR GRAFANA)
  - platform: pid #sensor 
    name: "iUdirna PID Climate HEAT"
    type: HEAT
# ENABLE PID SENSORS BELOW ONLY DURING AUTOTUNE #
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
# ENABLE THE PID SENSORS ABOVE ONLY DURING AUTOTUNE #

########################################################### TEMP. SENSOR

  # UDIRNA
  - platform: dallas #sensor 
    name: "iudirna_dallas_teplota1"
    address: 0xC106219415E16428 
    id: iudirna_teplota1
    unit_of_measurement: "°C"  
    # address detected from the log
    
  # MASO1
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota2"
    address: 0xBE06219419160728 
    id: iudirna_teplota2
    unit_of_measurement: "°C" 
    # address detected from the log
    
  # MASO2
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota3"
    address: 0xB101215744DA0B28 
    id: iudirna_teplota3
    unit_of_measurement: "°C"  
    # address detected from the log
    
  # DYMBOX
  - platform: dallas #sensor
    name: "iudirna_dallas_teplota4"
    address: 0xB9012157542A0228 
    id: iudirna_teplota4
    unit_of_measurement: "°C"  
    # address detected from the log

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

  # PID MODE LAMBDA FOR LCD (gets thermostat status - heat/off)
  - platform: template #sensor
    id: "pid_mode"
    name: iudirna_pid_mode
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(pid_climate).mode;
      
  # FAN SPEED LAMBDA FOR LCD (gets a speed value)
  - platform: template #sensor
    id: "fan_speed"
    name: iudirna_fan_speed
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(fan_pwm).speed;
      
  # VIB SPEED LAMBDA FOR LCD (gets a speed value)
  - platform: template #sensor
    id: "vibrator_speed"
    name: "iudirna_vibrator_speed"
    update_interval: 1s
    accuracy_decimals: 0
    lambda: |-
      return id(vibrator_pwm).speed;
  
########################################################### WIFI SIGNAL  

  # WIFI SIGNAL SENSOR
  - platform: wifi_signal #sensor
    name: "iudirna_signal_wifi2"
    update_interval: 10s     
    
interval: 

  # WIFI - CHECKING THE CONNECTION AND CONTROLLING THE LED INDICATOR LIGHT
  - interval: 1s
    then:
      if:
        condition:
          wifi.connected:
        then:
          - switch.turn_on: iudirna_led3_wifi_status
        else:
          - switch.turn_off: iudirna_led3_wifi_status
          
########################################################### TERMOSTAT - LED interval

  # LED DIODE - THERMOSTAT
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

########################################################### FAN AUTO 

  # FAN AUTO - change temperature and speed at will
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
              speed: !lambda return (id(fan_pwm).speed = 7.0); # SPEED
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
                  speed: !lambda return (id(fan_pwm).speed = 5.0); # SPEED           
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
                      speed: !lambda return (id(fan_pwm).speed = 3.0); # SPEED   
                      
########################################################### VIBRATOR AUTO

  # VIBRATOR AUTO                    
  - interval: 10min
    id: interval_vibrator_auto
    then:
      if:
        condition:
          and: # a condition that is triggered manually by a button
            - switch.is_on: vibrator_auto 
            - sensor.in_range:
                id: iudirna_teplota4
                above: 0  # vyse
                below: 30 # nize
        then:
          - fan.turn_on:
              id: vibrator_pwm
              speed: !lambda return (id(vibrator_pwm).speed = 10.0);  # MAX SPEED
          - delay: 3s # ceka 3s
          - fan.turn_on:
              id: vibrator_pwm
              speed: !lambda return (id(vibrator_pwm).speed = 1.0); # SET 1 (1=OFF)
          - fan.turn_off: # OFF vibrator
              id: vibrator_pwm
        else:
          if:
            condition:
              and:
                - switch.is_on: vibrator_wait_temp # pcondition that is enabled only after the temperature is reached from the start
                - sensor.in_range:
                    id: iudirna_teplota4
                    above: 0  
                    below: 30 
            then:
              - fan.turn_on:
                  id: vibrator_pwm
                  speed: !lambda return (id(vibrator_pwm).speed = 10.0); # MAX SPEED
              - delay: 3s # ceka 3s
              - fan.turn_on:
                  id: vibrator_pwm
                  speed: !lambda return (id(vibrator_pwm).speed = 1.0); # SET 1 (1=OFF)
              - fan.turn_off: # OFF VIBRATOR
                  id: vibrator_pwm 
                  
binary_sensor:              

########################################################### BUTTONS

  # BUTTON THERMOSTAT 
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
              
  # BUTTON LIGHT IN
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
    
  # BUTTON LIGHT OUT    
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
    
  # BUTTON THERMOSTAT TARGET TEMP +1°C   
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
            target_temperature: !lambda return (id(pid_climate).target_temperature + 1.0);
    - min_length: 500ms
      max_length: 2000ms
      then:
        - climate.control:
            id: pid_climate
            target_temperature: !lambda return (id(pid_climate).target_temperature + 5.0);    
    - min_length: 2000ms
      max_length: 6000ms
      then:
        - climate.control:
            id: pid_climate
            target_temperature: !lambda return (id(pid_climate).target_temperature = 65.0);   
            
  # BUTTON THERMOSTAT TARGET TEMP -1°C 
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
            target_temperature: !lambda return (id(pid_climate).target_temperature - 1.0);
    - min_length: 500ms
      max_length: 2000ms
      then:
        - climate.control:
            id: pid_climate
            target_temperature: !lambda return (id(pid_climate).target_temperature - 5.0);
    - min_length: 2000ms
      max_length: 6000ms
      then:
        - climate.control:
            id: pid_climate
            target_temperature: !lambda return (id(pid_climate).target_temperature = 0.0);
            
  # BUTTON FAN SPEED +1
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
            
  # BUTTON FAN SPEED -1
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
            
  # BUTTON FAN AUTO 
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
        
  # BUTTON VIBRATOR AUTO 
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
            
fan:
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
    
output: 

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
   
########################################################### PID AUTOTUNE

  # PID - PWM output     
  - platform: slow_pwm #output
    pin: GPIO16
    id: heater
    period: 30s
    
switch: 

  # PID - autotune    
  - platform: template #switch
    name: "iUdirna PID Climate Autotune"
    turn_on_action:
      - climate.pid.autotune: pid_climate

########################################################### RELAY

  # FAN AUTO - enables the automatic control interval condition
  - platform: template
    name: "iudirna_fan_auto"
    id: fan_auto
    optimistic: true
    
  # VIBRATOR AUTO - enables the interval condition of the automatic button control
  - platform: template
    name: "iudirna_vibrator_auto"
    id: vibrator_auto
    optimistic: true
    turn_off_action: 
        # when manually switching off with the button it also switches off the wait_temp condition
      - switch.turn_off: vibrator_wait_temp 
      - fan.turn_on: # nastavi rychlost na minimum (1= vypnuto)
          id: vibrator_pwm
          speed: !lambda return (id(vibrator_pwm).speed = 1.0);
      - fan.turn_off: # a vypne vibrator
          id: vibrator_pwm 
          
  # VIBRATOR AUTO WAIT FOR TEMP             
  - platform: template                      
    name: "iudirna_vibrator_wait_temp"                                            
    id: vibrator_wait_temp                  
    optimistic: true
    # automatic control interval condition 
    # only after reaching a given temperature from the start does the thermometer switch it on
    # this will ensure that it will not vibrate from the beginning when melting but only 
    # after reaching the specified temperature
    
  # RELAY2 - INDOOR LIGHT
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
        
  # RELAY3 - OUTDOOR LIGHT  
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

########################################################### LED DIODE OUTPUT

  # LED1 - THERMOSTAT STATUS (RED)
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
      
  # LED2 - OUTDOOR LIGHT (YELLOW)
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
      
  # LED3 - OUTDOOR LIGHT (YELLOW)
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
      
  # LED3 - WIFI status (BLUE)
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

  # PID THERMOSTAT REMOTE
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

