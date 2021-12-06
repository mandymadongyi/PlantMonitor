#  Plant Monitor – Talking Robin

## Dongyi Ma  unfndma@ucl.ac.uk
### MSc Connected Environment, Centre for Advanced Spatial Analysis, UCL 
Nov 2021
## 1. Overview
Plants are not able to talk or move to express their feelings like humans. To better understand the plant’s health condition, I designed a plant monitor to sense real-time soil moisture, store the date, time, and moisture figure in raspberry pi, then visualise real-time data and demonstrate the trends using Grafana. After that, I have three extensions supplementary to the primary plant monitor to make it a more useful IoT device. Then I evaluated the three versions regarding the role connected devices plays in helping us make sense of the environment around us and reflected the data reliability of my sensor system.  
## 2. Prerequisite tools 
### •	Hardware needed:
1. [Adafruit Feather HUZZAH ESP8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266)
2. [Raspberry Pi 4 Computer](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) 
3. [DHT22 Sensor](https://www.kandrsmith.org/RJS/Misc/Hygrometers/calib_dht22.html) 
4. Nail Soil Sensor (DIY)
### •	Software needed:
1. [Arduino IDE](https://www.arduino.cc/en/software)
2. [MQTT Explorer](http://mqtt-explorer.com/) 
3. [Raspberry Pi Imager](https://www.raspberrypi.com/software/)  
4. [Grafana](https://grafana.com/) 

 
## 3.Steps 
1. Install Arduino IDE, MQTT Explorer
2. Create a new repo on GitHub
3.  Set up Feather by opening Arduino-preferences-settings and adding URL of esp8266 board package, then go to Arduino-tools-board manager-search the board name-install board package, then test if the board is working by using [Arduino blink example](https://www.arduino.cc/en/Tutorial/BuiltInExamples/Blink).
4. Use [1._connect_board_to_wifi.ino](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/1._connect_board_to_wifi/1._connect_board_to_wifi.ino) to connect to WIFI, open serial monitor and click on show timestamp to view network connection. 
![1.png](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/1.png?raw=true) 

 Figure 1. Serial monitor screenshot of a successfully connected board

5. Install the ezTime library through the Sketch > Include Library > Manage Libraries. Record the date and time that data was taken using [2._get_ezTime_for_recording.ino](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/2._get_ezTime_for_recording/2._get_ezTime_for_recording.ino), if successful, will see real-time in the serial monitor. 
6. Use script [3._push_data_to_mqtt.ino](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/3._push_data_to_mqtt/3._push_data_to_mqtt.ino) push data to MQTT. Use [arduino_secrets.h](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/3._push_data_to_mqtt/arduino_secrets.h) file to store the usernames and passwords by clicking on the little arrow at the top of the sketch and adding a new tab, and add in the four #defines... lines in the comments with the real user names and passwords. Change client name before publishing to distinguish from other students from here client. publish("testmandy", msg).
7. Connect to MQTT with host mqtt.cetools.org and port 1884 (read-only and 1884 read and write), see testmandy and follow the incremental number to make sure data is successfully pushed to MQTT. Publish 1 to turn on LED and 0 to turn off the LED. 
8. Connect a DHT22 temperature/humidity sensor to the circuit and build a pair of nails. Look at the below image for instruction. 

 ![2](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/2.png?raw=true)

Figure 2. Instruction of sensor circuit connection

Then run [4._sense_moisture_data](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/4._sense_moisture_data/4._sense_moisture_data.ino) to sense moisture data and show on the serial plotter. Should see from the data ranges around 150-350 and when contacting the two nails together. After that, detach feather from breadboard and solder it printed circuit board (PCB) 
 
 ![3](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/3.png?raw=true)
Figure 3. A photo of DIY nails and connected PCB

9. Run [5_final_DataWithNails_mqtt.ino](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/scripts/5_final_DataWithNails_mqtt/5_final_DataWithNails_mqtt.ino) to show plant data on MQTT under the topic. Data is updated once a second, and if the nails' position changes, the moisture data will change accordingly. Water the plant to adjust the moisture and see if the “I am thirsty” reactions consequently. 

 ![4](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/4.png?raw=true)

Figure 4. Real-time data of robin displayed on MQTT Explore under topic #ucfndma

[Here](https://youtu.be/WxioRQvocaw) is a video of serial plotter displaying moisture data of my Talking Robin.

10. Download raspberry pi imager and take out a flashcard to set raspberry pi up, then change the hostname of it and plug the memory card back to the pi, connect the pi to power, open terminal and type the command [1. set raspberry pi as gateway](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/raspberry%20pi%20terminal%20commands/1.%20set%20raspberry%20pi%20as%20gateway%20.xlsx).
11. follow the instructions on [2. Installing InfluxDB on RPi](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/raspberry%20pi%20terminal%20commands/2.%20Installing%20InfluxDB%20on%20RPi%20.xlsx) to install influx DB and then browse to http://localhost:3000 and see the Grafana welcome page - the default user pass is admin admin.

12. follow the instructions on [3. Collecting and storing soil data](https://github.com/mandymadongyi/Plant-Monitor/blob/main/resources/raspberry%20pi%20terminal%20commands/3.%20Collecting%20and%20storing%20soil%20data%20.xlsx) to collect data and store them into raspberry pi storage. Change the configurations of telegraf.conf file like is shown in the picture.

 ![5](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/5.png?raw=true)

Figure5. Telegraf Configuration

13. Grafana
The final step is to visualise the MQTT data stored in influx DB. Set up a new database and use a new dashboard to visualise our data. 

     ![6](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/6.jpeg?raw=true)
![7](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/7.jpeg?raw=true)
Figure 6&7. grafana visualisation outcomes of real-time MQTT data of mandy’s plant



Here are some mistakes and solutions worth noting from my experiment

![8](https://github.com/mandymadongyi/Plant-Monitor/blob/main/pictures/8.png?raw=true)


Figure 8. Common mistakes and solutions during experiment


Reference:

1. https://learn.adafruit.com/adafruit-feather-huzzah-esp8266
2. https://www.raspberrypi.com/products/raspberry-pi-4-model-b/
3. https://www.kandrsmith.org/RJS/Misc/Hygrometers/calib_dht22.html
4. https://www.arduino.cc/en/software
5. http://mqtt-explorer.com/
6. https://www.raspberrypi.com/software/
7. https://grafana.com/
8. https://www.arduino.cc/en/Tutorial/BuiltInExamples/Blink
9. https://workshops.cetools.org/codelabs/CASA0014-2-Plant-Monitor/index.html?index=..%2F..casa0014#0
10. https://github.com/ucl-casa-ce/casa0014
11. https://youtu.be/WxioRQvocaw

