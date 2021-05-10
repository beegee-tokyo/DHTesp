DHTesp [![Build Status](https://github.com/beegee-tokyo/DHTesp/workflows/Arduino%20Library%20CI/badge.svg)](https://github.com/beegee-tokyo/DHTesp/actions)
===
<!-- [![Build Status](https://travis-ci.com/beegee-tokyo/DHTesp.svg?branch=master)](https://travis-ci.com/beegee-tokyo/DHTesp) -->

_**This library is no longer maintained**_

An Arduino library for reading the DHT family of temperature and humidity sensors.    
Forked from [arduino-DHT](https://github.com/markruys/arduino-DHT)     
Original written by Mark Ruys, <mark@paracas.nl>.    

Why did I clone this library instead of forking the original repo and push the changes?
When I searched through Github for DHT libraries, I found a lot of them, some of them offers additional functions, some of them only basic temperature and humidity values. I wanted to combine all interesting functions into one library. In addition, none of the DHT libraries I found were written to work without errors on the ESP32. For ESP32 (a multi core/ multi processing SOC) task switching must be disabled while reading data from the sensor.    
Another problem I found is that many of the available libraries use the same naming (dht.h, dht.cpp), which easily leads to conflicts if different libraries are used for different platforms.    

_**According to users, the library works as well with DHT33 and DHT44 sensors. But as I do not own these sensors, I cannot test and confirm it. However, if you want to use this sensors, you can do so by using `setup(pin, DHTesp::DHT22)` and it should work.
Please give me feedback in the issues if you successfull use these sensors.
Thank you**_

The library is tested as well on ESP8266 and should work on AVR boards as well.    

Changes to the original library:
--------
- 2017-12-12: Renamed DHT class to DHTesp and filenames from dht.* to DHTesp.* to avoid conflicts with other libraries - beegee-tokyo, <beegee@giesecke.tk>.    
- 2017-12-12: Updated to work with ESP32 - beegee-tokyo, <beegee@giesecke.tk>.   
- 2017-12-12: Added function computeHeatIndex. Reference: [Adafruit DHT library](https://github.com/adafruit/DHT-sensor-library).    
- 2017-12-14: Added function computeDewPoint. Reference: [idDHTLib](https://github.com/niesteszeck/idDHTLib).    
- 2017-12-14: Added function getComfortRatio. Reference: [libDHT](https://github.com/ADiea/libDHT). (References about Human Comfort invalid)    
- 2017-12-15: Added function computePerception. Reference: [WikiPedia Dew point==> Relationship to human comfort](https://en.wikipedia.org/wiki/Dew_point) - beegee-tokyo, <beegee@giesecke.tk>.   
- 2018-01-02: Added example for multiple sensors usage.    
- 2018-01-03: Added function getTempAndHumidity which returns temperature and humidity in one call.    
- 2018-01-03: Added retry in case the reading from the sensor fails with a timeout.    
- 2018-01-08: Added ESP8266 (and probably AVR) compatibility.    
- 2018-03-11: Updated DHT example    
- 2018-06-19: Updated DHT example to distinguish between ESP8266 examples and ESP32 examples    
- 2018-07-06: Fixed bug in ESP32 example    
- 2018-07-17: Use correct field separator in keywords.txt    
- 2019-03-07: Added computeAbsoluteHumidity which returns the absolute humidity in g/m³. Reference: [How to convert relative humidity to absolute humidity](https://carnotcycle.wordpress.com/2012/08/04/how-to-convert-relative-humidity-to-absolute-humidity/) kudos to [Wurstnase](https://github.com/Wurstnase)    
- 2019-03-22: Fixed auto detection problem    
- 2019-07-31: Make getPin() public, Updated ESP8266 example        
- 2019-10-01: Using noInterrupts() & interrupts() instead of cli and sei
- 2019-10-05: Reduce CPU usage and add decimal part for DHT11 (thanks to Swiftyhu)
- 2019-10-06: Back to working version by removing the last commit
- 2021-02-20: Fix negative temperature problem (credits @helijunky)

Features
--------
  - Support for DHT11 and DHT22, AM2302, RHT03
  - Auto detect sensor model
  - Determine heat index
  - Determine dewpoint
  - Determine thermal comfort:
    * Empiric comfort function based on comfort profiles(parametric lines)
    * Multiple comfort profiles possible. Default based on http://epb.apogee.net/res/refcomf.asp  (References invalid)
    * Determine if it's too cold, hot, humid, dry, based on current comfort profile
    * More info at [Determining Thermal Comfort Using a Humidity and Temperature Sensor](https://www.azosensors.com/article.aspx?ArticleID=487)
  - Determine human perception based on humidity, temperature and dew point according to Horstmeyer, Steve (2006-08-15). [Relative Humidity....Relative to What? The Dew Point Temperature...a better approach](http://www.shorstmeyer.com/wxfaqs/humidity/humidity.html)

Functions
-----
_**`void setup(uint8_t pin, DHT_MODEL_t model=AUTO_DETECT);`**_    
- Call to initialize the interface, define the GPIO pin to which the sensor is connected and define the sensor type. Valid sensor types are:     
    - AUTO_DETECT     Try to detect which sensor is connected (default if 2nd parameter is not used)    
    - DHT11    
    - DHT22    
    - AM2302          Packaged DHT22    
    - RHT03           Equivalent to DHT22    
_**`void resetTimer();`**_    
- Reset last time the sensor was read    

_**`float getTemperature();`**_    
- Get the temperature in degree Centigrade from the sensor    
Either one of  _`getTemperature()`_ or  _`getHumidity()`_ or  _`getTempAndHumidity()`_ initiates reading a value from the sensor if the last reading was older than the minimal refresh time of the sensor.    
See example _`DHT_ESP32.ino`_ or _`DHT_Test.ino`_    

_**`float getHumidity();`**_    
- Get the humidity from the sensor     
Either one of  _`getTemperature()`_ or  _`getHumidity()`_ or  _`getTempAndHumidity()`_ initiates reading a value from the sensor if the last reading was older than the minimal refresh time of the sensor.    
See example _`DHT_ESP32.ino`_ or _`DHT_Test.ino`_    

_**`TempAndHumidity getTempAndHumidity();`**_    
- Get the temperature and humidity from the sensor     
Either one of _`getTemperature()`_ or  _`getHumidity()`_ or  _`getTempAndHumidity()`_ initiates reading a value from the sensor if the last reading was older than the minimal refresh time of the sensor.    
Return value is a struct of type _`TempAndHumidity`_ with temperature and humidity as float values.
See example _`DHT_Multi.ino`_    

_**`DHT_ERROR_t getStatus();`**_    
- Get last error if reading from the sensor failed. Possible values are:    
  - ERROR_NONE      no error occured
  - ERROR_TIMEOUT   timeout reading from the sensor    
  - ERROR_CHECKSUM  checksum of received values doesn't match

_**`const char* getStatusString();`**_    
- Get last error as a char *    

_**`DHT_MODEL_t getModel()`**_    
- Get detected (or defined) sensor type    

_**`int getMinimumSamplingPeriod();`**_    
- Get minimmum possible sampling period. For DHT11 this is 1000ms, for other sensors it is 2000ms    

_**`int8_t getNumberOfDecimalsTemperature();`**_    
- Get number of decimals in the temperature value. For DHT11 this is 0, for other sensors it is 1    

_**`int8_t getLowerBoundTemperature();`**_    
- Get lower temperature range of the sensor. For DHT11 this is 0 degree Centigrade, for other sensors this is -40 degree Centrigrade    

_**`int8_t getUpperBoundTemperature();`**_    
- Get upper temperature range of the sensor. For DHT11 this is 50 degree Centigrade, for other sensors this is 125 degree Centrigrade    

_**`int8_t getNumberOfDecimalsHumidity();`**_    
- Get number of decimals in the humidity value. This is always 0.    

_**`int8_t getLowerBoundHumidity();`**_    
- Get lower humidity range of the sensor. For DHT11 this is 20 percent, for other sensors this is 0 percent    

_**`int8_t getUpperBoundHumidity();`**_    
- Get upper temperature range of the sensor. For DHT11 this is 90 percent, for other sensors this is 100 percent    

_**`static float toFahrenheit(float fromCelcius);`**_    
- Convert Centrigrade value to Fahrenheit value    

_**`static float toCelsius(float fromFahrenheit) { return (fromFahrenheit - 32.0) / 1.8; };`**_    
- Convert Fahrenheit value to Centigrade value    

_**`float computeHeatIndex(float temperature, float percentHumidity, bool isFahrenheit=false);`**_    
- Compute the heat index. Default temperature is in Centrigrade.    

_**`float computeDewPoint(float temperature, float percentHumidity, bool isFahrenheit=false);`**_    
- Compute the dew point. Default temperature is in Centrigrade.    

_**`float computeAbsoluteHumidity(float temperature, float percentHumidity, bool isFahrenheit=false);`**_    
- Compute the absolute humidity in g/m³. Default temperature is in Centrigrade.    

_**`float getComfortRatio(ComfortState& destComfStatus, float temperature, float percentHumidity, bool isFahrenheit=false);`**_    
- Compute the comfort ratio. Default temperature is in Centrigrade. Return values:    
0 -> OK    
1 -> Too Hot    
2 -> Too cold    
4 -> Too dry    
8 -> Too humid    
9 -> Hot and humid    
5 -> Hot and dry    
10 -> Cold and humid    
6 -> Cold and dry    

_**`byte computePerception(float temperature, float percentHumidity, bool isFahrenheit=false);`**_    
- Compute the human perception. Default temperature is in Centrigrade. Return values:    
0 -> Dry    
1 -> Very comfortable    
2 -> Comfortable    
3 -> Ok    
4 -> Uncomfortable    
5 -> Quite uncomfortable    
6 -> Very uncomfortable    
7 -> Severe uncomfortable    

_**`uint8_t getPin(void);`**_    
- Returns the assigned GPIO for this instance. Usefull when connecting multiple sensors         

Usage
-----
See [examples](https://github.com/beegee-tokyo/DHTesp/blob/master/examples). For all the options, see [dhtesp.h](https://github.com/beegee-tokyo/DHTesp/blob/master/DHTesp.h).    

Installation
------------

In Arduino IDE open Sketch->Include Library->Manage Libraries then search for _**DHT ESP**_    
In PlatformIO open PlatformIO Home, switch to libraries and search for _**DHT ESP32**_. Or install the library in the terminal with _**`platformio lib install 2029`**_    

For manual installation [download](https://github.com/beegee-tokyo/DHTesp/archive/master.zip) the archive, unzip it and place the DHTesp folder into the library directory.    
In Arduino IDE this is usually _**`<arduinosketchfolder>/libraries/`**_    
In PlatformIO this is usually _**`<user/.platformio/lib>`**_    
