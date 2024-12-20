## IoT Home Weather Statiom

---

### The goal of this project is to create an indoor and outdoor weather monitor.
#### The monitor will be build using an microcontroller and various sensors to display the following:

- **Temperature**
- **Humidity** 
- **Pressure** 
- **Wind Speed**
- **Direction**
- **Rainfall**


These readings will be sent real time wirelessly to a Raspberry Pi which will then give a live display of the readings in a GUI interface. 

---

### The Project

The hardware for this project will consist of the SparkFun weather station kit<br><img src="Images/Station.jpg" width="400" height="600"><br><br>And the SparkFun MicroMod carrier board paired with a ESP32 proccessor<br><img SRC="Images/mmWeatherboard.jpg"><br><br> This takes all the desired readings and uses MQTT to send messages over WiFi to a Raspberry Pi which acts as the MQTT broker and then uses Python and Matlab to plot the messages as data on graphs, I also included a live text display so I can see climate trends over time and a live current reading.<br> The graph is a simple Matlab format put into a Tkinter window<br><img src="Images/live_graph.jpg"><br>

---

### Communication

The data from the outdoor weather station will need to be sent to the Pi inside over a short distance.<br> The [SparkFun](https://thepihut.com/products/sparkfun-arduino-iot-weather-station) Comes with an ESP32 board so I can use its WiFi capabilities to send the data with the MQTT protocol by setting the the board as publisher and the RaspPi and a subscriber.<br> To do this I am using the Mosquitto MQTT broker on the Pi. 

---
### Display


Using Tkinter I have created a executable desktop icon that runs the Tkinter GUI module which is connected to the Matplot graphs creating one intergrated display.<br> To do this I created a desktop entry in a bash script which points to another bash script where which opens the GUI.py module in my WeatherStation directory. This might be an overly complicated way to open from an icon but it works for what I need.

---
### Weather Proofing

This kit doesn't come with any outside gear so I had to come up with a solution to thisyself.<br> The BME280 chip is on the board so a fully enclosed space wouldn't display the correct data<br>To solve this issue I bought a waterproof junction box and a mesh lined vent then I cut out the shape of the vent in the bottom of the box and securrd the mesh park inside the box and sealed with a waterproof silicone sealant. then attached the vented part on the outside of the box.<br>Between the vent, mesh and position on the box even during heavy rain water should not get inside the box whilst still allowing airflow. 

---
### External Power

For practically the station will need to be self powered. To do this I have chosen a 2000mAh LiPoly battery which once fully charged can last a few days of constant use before needing recharge.<br>Paired with this I have chosen to use a solar panel connected to a Adafruit power management board. This board connects the LiPoly to the board and then the solar panel to the battery to charge once voltage drops below a defined level.<br>This board also accepts USB-C input if the weather doesn't allow a constant solar charge. <br>
<img src="Images/solarSetup.jpg"><br>

---
### Finished Main Project

At its current stage I am saying this project is finished. The main goals of creating an IoT weather station to display the temperature, humidity,rainfall, wind speed and direction is complete alongside a live text display and charts for some weather trends and patterns.<br><br>
<img src="Images/PXL_20241214_130608843.jpg"><br>
<img SRC="Images/updatedDisplay.jpeg">

---

### **Issues**
- **Power**
When I finished the project and was happy with the Python and C++ side of it I started to look into external power. I've settled with a 2000mAh Lipoly battery and a 6W solar panel with a Adafruit bq24074 charging board. This powered the board via the battery and the solar panel keeps the battery topped up.<br> The first issues was the solar panel is not the greatest and with cloudy and dark weather it can't charge the battery as fast as the battery drains to power the board.<br> This I found was the ESP was sending the messages every 10 seconds using a lot of power, I changed this to every 60 seconds the power issues carried on.<br>
- **Resolve**
To fix this I looked into ESP low power mode functions. As using modem mode or sleep mode caused connection issues or still battery draining issues I opted to use light power mode on a rolling cycle of sleeping for a set time, waking up for a set time and back to sleep.<br><br>
- **Power connection**
Looking into external power for this board SparkFun dont have a well documented way it can be easily done. The main power for this board is a USB-C port which can be plugged into a 5V plug as it has a built in 5v to 3.3v regultor. But once this is outside in a weather proof box it wont be practical to run a cable from the box into the house or other means of power unless I install an external power socket outside which is just not needed.<br>Another issues with this board is it only has pin holes for external wires, when searched they advertise it as coming with a 2 pin JST connection so I bought a JST wire to go from the charging board to the carrier board which was a wrong fit.
- **Resolve**
To fix this physical power issue I stripped the JST pins and soldered the wires directly into the 3.3v pin and gnd on the board. The 2000mAh Lipoly battery runs as 3.7v so this is find to directly attach and this has solved the power issues.<br><br>

- **Watchdog timers**
There was issues with connection to wifi not being established which ended in a constant loop of attempting connection, to solve this I looked into setting up a WDT to catch the failed attempt and restart the code. This started to become complicated and the WDT event was being triggered but not restarting the code. I found the WDT was being trigged in a set up loop so as it reset the code it would catch this loop and crash out.<br>
- **Resovle**
As the board isn't saving any data itself I have no issue with a reset as it would react to the physical button being pressed so in times where the wifi connection cant be establised within a 60 second timeout the ESP uses a reset function on the  whole board and starts the process all over again.
  

---
### **Next steps**

The projects current code uses a rolling 60 second cycle of powering down waiting, powering back sending data for 60 seconds and so on, this is for testing purposes. When it is set up in my garden I will need to adjust this to maybe every 10, 30, 60 mins, this allows time for battery to charge and send some relevant data. once installed outside I will have to monitor how well the solar panel keeps the board running, if it struggles I will experiment with the board power off time.<br>

