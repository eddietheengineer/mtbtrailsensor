# Mountain Bike Trail Sensor

Goal: Standalone IoT device to monitor real trail conditions

Most mountain bike trails today have some sort of "trail status" webpage--most of them are updated by trail stewards who regularly ride the trails and update them depending on the conditions. Generally there are four potential statuses

  Red: Wet/muddy, riding will damage the trail surface
  Yellow: Mostly dry except a few isolated spots, ride with caution
  Green: Good to go!
  Blue: Trail is frozen (good)
  
This system works okay when the trail is frequently ridden, but on more remote/less used trails it becomes problematic because the trail sometimes may not get an update for weeks. This project is to develop a sensor that will take regular measurements of the environment and automatically update trail conditions via the internet. 

Current state of the project:
  Primary device: 
  
      Particle Electron (cellular IoT device with built in Lipo charging circuit and deep sleep support)
      FreedomPop SIM Card (200MB/month free data)
      Lipo Battery (2000mAh)
      BME280 Sensor (Air Temp/Humidity/Pressure)
      DS18B20 Sensor (Waterproof Temperature Sensor -- Ground Temperature)
      TMP36 Sensor (Air Temp inside Enclosure for development--if it gets too hot)
      Capacitive Soil Moisture Sensor V1.2 (Soil Moisture)
      NPN Transistor (Switch power to sensors off during sleep)
      2.5W 5V/500mA Solar Panel
     
Current readings (still tweaking code):
  https://thingspeak.com/channels/852007
