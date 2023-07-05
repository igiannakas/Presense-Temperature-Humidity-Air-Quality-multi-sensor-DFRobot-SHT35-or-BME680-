# Introduction
This is a step by step guide on how to build an room multi-sensor for HomeAssistant. You can:
1. Set it up as a multi sensor - to detect presense, humidity, temperature, air quality, air pressure
2. Set it up as a single sensor - to detect temperature only.
For a presense only sensor see my Github page here: https://github.com/igiannakas/mmwave-d1mini

The custom module in the code above is used to implement the above and exposes the presense detection sensor, the radar's configuration variables and the room temperature, humidity, air quality and air pressure values to Home Assistant via the ESPHome integration.


# Bill of Materials:
To make this DIY room presense sensor you'll need the below components:
1. D1 Mini ESP32
3. DFRobot SEN0395 (from Digi Key, Mouser, Arrow.com, Farnell.com, AliExpress)
4. A temperature and humidity sensor. I use the BME680 together in the mmWave radar sensor configuration to create a powerfull all in one sensor. For the temperature and humidity only configuration, I use the SHT35 sensor from PiHut
5. Mini USB cable and a USB power supply (I use my old phone chargers)
6. Soldering iron
7. 5 cm / 2 inch of wire
8. 3d printed case. My custom designed case can be found here: https://www.printables.com/model/520547-esp32-temperature-and-optional-mmwave-radar-presen


# Wiring instructions:
To get the smallest possible size we stack the sensor on top of the D1 Mini using the pins that come with the D1 mini. The wiring diagram below reflects a stacked configuration:
Sensor Pin -> D1 Mini ESP32 Pin
**DF Robot Radar:**
1. TX -> 22
2. RX -> 21
3. IO1 -> not connected
4. IO2 -> 16
5. G -> G
6. V -> 5V
**Temperature Sensor (BME680)**
1. SDA -> 25
2. SCL -> 27
3. V -> 3.3V
4. G -> G
**(or) Temperature Sensor (SHT35)**
1. SDA -> 18
2. SCL -> 19
3. V -> 3.3V
4. G -> G


# Installing ESPHome and the mmWave code

1. Setup your esphome environment. For instructions: https://esphome.io
2. Clone this repository to your build environment. Download the code zip and unpack it in your esphome build directory
3. Open the sensor.yaml file and modify the following variables to match your setup:

**device_name**: the sensor's device name. This must be in lower case and any words separated with hyphens (-). For example: living-room-occupancy-sensor<br>
**device_name_pretty**: This is the name of the occupancy binary sensor that will be exposed to home assistant. It can be upper and lower case and can contain spaces. For example: Room Name Occupancy Sensor<br>
**ssid**: type your 2.4ghz wifi SSID<br>
**wifi_password**: type in your wifi password<br>

4. Do not modify the uart_tx_pin, uart_rx_pin, gpio_pin values unless you're using a different pinout connection.
5. Deploy the code. I have installed esphome on my mac so I use the following command to deploy the code: esphome run sensor.yaml . If it is a fresh D1 Mini, it doesnt contain the esphome code. For the first flash, you'll need to plug it in to your computer or HA box and flash it. Any subsequent updates will happen over the air. 

For a tutorial on how to setup your esphome instance read up here:
1. From the command line & using docker: https://esphome.io/guides/getting_started_command_line.html
2. From home assistant OS: https://esphome.io/guides/getting_started_hassio.html


# Setting the sensor up in HomeAssistant and configuring its parameters
The sensor should be autodetected in your homeassistant dashboard. Go to Settings - Devices & Services and add the integration. Then you should be presented with the device dashboard

Here you can:
1. **See the occupancy sensor value (clear / detected)**. This is the sensor you will use in your automations. 
2. **Distance**: this variable can be used to set the maximum distance the sensor can see. It defaults to the sensor default value.
3. **Latency**: this is the sensor cool down period, i.e. how long should no presence be detected before the occupancy sensor is set to "Clear". It defaults to the sensor default value.
4. **Led**: a toggle switch to turn the sensor LED on or off. On initial setup it defaults to off but the sensor LED is on and blinking. So if you want to turn the sensor LED off, switch it on, wait for 10 seconds then switch it off again. The value should now persist in the sensor's memory
5. **mmwave_sensor**: This toggle switch turns the motion sensor (radar) on or off. It defaults to on, but is reported as off until the first time presense is detected. Can be usefull if you need to disable motion sensing from an automation or script.
6. **Sensitivity**: How sensitive the sensor is to movement. The Radar sensor is **very** sensitive to movement in order to deliver meaningfull presense detection but it can be triggered falsely with the movement of curtains, clothes etc. If you want to reduce sensitivity reduce this number. It defaults to 7, which is a good balance but if you are finding that the sensor reporting as clear when the room is occupied increase this to 9.
7. **use_safe_mode**: restarts the D1 mini in safe mode
8. **Restart**: restarts the sensor
9. **Factory reset mmWMCU**: resets the radar to its factory default settings. (distance, latency, led, sensitivity)

The sensor distance, latency, mmwave_sensor, sensitivity values are read from the radar's presistent memory. They persist reboots but reporting the values to HomeAssistant is delayed. It will take 5-10 minutes after you reboot the D1 Mini for the values to be reported so be patient until they are populated before making any changes.

Every time you change a value you need to wait ~15 - 20 seconds for the value to be written to the DFRobot radar sensor memory and for the radar to restart. While the D1 mini is writing the values to the radar's memory you'll see the LED light turn red. Once it starts blinking or is off (depending on your settings) the values are now written in memory and are persistent through reboots. 

Finally if you are using the SHT35 sensor, I have implemented a variable in HomeAssistant to prevent it from going into deep sleep mode, to allow you to perform OTA updates. For this to work, you'll need to setup an input helper - toggle in homeassistant and name it "sht_sensor_prevent_sleep". When the toggle is on, the sensor will not sleep. Normally you'll want the sensor to sleep for as long as possible and as quickly as possible to minimise self heating on the SHT35 module.

# Sensor case
I've designed a basic case for the sensor which should provide a snug fit to its components. The STL files can be found here: https://www.printables.com/model/520547-esp32-temperature-and-optional-mmwave-radar-presen

# Images


![4](https://github.com/igiannakas/Presense-Temperature-Humidity-Air-Quality-multi-sensor-DFRobot-SHT35-or-BME680-/assets/59056762/8fc328fb-440e-4ad3-8e84-b08441062752)
![5](https://github.com/igiannakas/Presense-Temperature-Humidity-Air-Quality-multi-sensor-DFRobot-SHT35-or-BME680-/assets/59056762/14958fe0-bc12-4dc0-b12c-8d23aa47a988)
![6](https://github.com/igiannakas/Presense-Temperature-Humidity-Air-Quality-multi-sensor-DFRobot-SHT35-or-BME680-/assets/59056762/b7d012fd-cb62-41dd-8ce6-a01617784b3d)
![7](https://github.com/igiannakas/Presense-Temperature-Humidity-Air-Quality-multi-sensor-DFRobot-SHT35-or-BME680-/assets/59056762/99a86c6f-b173-4e20-9dd9-7b4d52ff69bb)


