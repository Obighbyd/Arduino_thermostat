## DS18B20 thermostat with logging
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/header.png" alt="header">

> ESP8266 thermostat with DS18B20 oneWire sensor

This is a 'simple' thermostat controller, using a DS18B20 oneWire temperature sensor on a WeMos D1 Mini Pro. The DS18B20 has a +/-0.5 &deg;C tolerance and is perfectly stable (and even cheaper). I tried it first with an DHT11 sensor, but it's very unreliable, unstable and has a +/-2 &deg;C tolerance (and who needs a humidity sensor anyway, except perhaps if you use it in a sauna).

It uses two relays for safety reasons, to be sure that there is no current on either one of the power lines when switched off. I couldn't find a shield with two relays, so I bought it as a kit and soldered all components to the board myself (relays, terminal strip, and the fun part: the SMD components). It just had to be rewired to switch two separate wires independently, instead of switching current from the same wire to two different consumers.

The thermostat will power up the relays if the temperature is greater than temp\_max and power off if temperature falls below temp\_min if the appliance is set as a _cooler_, and accordingly, power them up if temperature falls below temp\_min and power off if the temperature is greater than temp\_max if set as a _heater_.

The _www_ branch contains the (php) files needed for the webserver (get log data, keep settings, render Google graph, provide a settings form for one or more such devices). In the settings form, some standard values are filled in, some basic checks performed and the SHA1 fingerprint is calculated from the server's certificate (to make sure the WeMos sends the data to the right logserver).

The display shows following data: the SSID (if connected to WiFi, or the IP and credentials for the AP it provides in config mode), the LAN IP, the outside IP (which it refreshes only once an hour, to not get banned from ipinfo.io), the temperature (also min/max on the large display), the device type (heater/cooler), the relais state (On/Off) and the current operating mode (manual/automatic).

A capacitive touch sensor toggles the relay if pressed less than 500ms thus changing to manual operating mode (means that relais isn't controlled by the temperature readings anymore). If held longer than 500ms, it switches back to automatic mode. I used touch sensors because I didn't have buttons, but it's way cooler with touch sensors anyway. The main advantage is, you don't have to bore holes for the buttons, just stick them to the inner side of the enclosure and add appropriate stickers to the outside.

I know that there are simpler or fancyer methods to do some of this (like OpenHAB, Blynk, etc.), but I wanted to do it all by myself with already available services, as an exercise, and to learn stuff in the process (so please don't contact me with such tips). All other feedback is welcome.

---

### Table of Contents
- [Features](#features)
- [Libraries](#Libraries-needed)
- [TODO](#TODO)
- [Bugs](#Bugs)
- [Links](#Links)
- [Images](#Images)
- [Screenshots](#Screenshot-graph-page)
- [License](#License)
[??? goto top](#DS18B20-thermostat-with-logging)

---

### Features
- **.96" B/W or 1.44" color TFT display for: SSID, LAN and internet IP, temperature, mode (man/auto) and relay status**
- **Uses WiFiManager library, so you don't have to hardcode the WiFi credentials**
- **Uses a webserver to log the measurements (HTTPS)**
- **Compares the logservers certificate SHA1 fingerprint with the one provided in the settings**
- **Progress bar on the serial console, showing time percentage until next log update**
- **Manual relay override via capacitive touch sensor; press longer than 1s to switch back to automatic mode**
- **Option to change between _'cooler'_ and _'heater'_ thermostats (fire up on `temp >= temp_max` or `temp <= temp_min`)**
- **Draws a google graph from the logged data and a gauge which also shows hysteresis**
- **Shows last update date/time in the browser's timezone**
- **Settings button on the webpage, which opens the device's settings page**
- **Provides a setup page for configuration variables that are sent to the WeMos, which then stores them as JSON to the webserver and SPIFFS:**
    - WeMos D1 hostname (in case you have more than one device logging to the same webserver)
    - SHA1 fingerprint (calculated by the php script from localhosts certificate)
    - Logserver hostname
    - Logserver port (HTTPS)
    - Refresh/sensor reading interval
    - Minimum/maximum temperature values for the hysteresis
    - Temperature deviation for calibrating the sensor
    - Thermostat device type (cooler/heater)
    - Operation mode (manual/automatic)
    - Time interval between measurements.

[??? goto top](#DS18B20-thermostat-with-logging)

---

### Libraries needed
- WiFiManager
- ESP8266HTTPClient
- ESP8266WebServer
- ESP8266mDNS
- FS
- OneWire
- DallasTemperature
- ArduinoJson
- Adafruit\_GFX
- Adafruit\_ST7735
- SPI
- TFT\_eSPI


[??? goto top](#DS18B20-thermostat-with-logging)

---

### TODO
- [x] Clean up code and add comments
- [ ] CSS-styled mouseover tooltip for the graph, including relay status
- [ ] Change the graph's time range, enable zooming and panning
- [ ] Change the horrible design (now I've added even more of it)
- [ ] Responsive design for mobile devices
- [ ] \(Perhaps) Enable OTA
- [ ] \(Perhaps) Keep log data in a local file if logserver is not reachable and send it all when connectivity is restored
- [ ] \(Perhaps) Documentation, including 'fritzing'. In the mean time, look at my sources in the [Links (software)](#software) below (fritzing should be pretty straight forward anyway)
- [ ] \(Perhaps) Change checking logserver validity from comparing SHA1 fingerprint, to check against an uploaded CA certificate (longer validity then a letsencrypt signed certificate)

[??? goto top](#DS18B20-thermostat-with-logging)

---

### Bugs
- [ ] .96" Display won't work if not initialized just before use (init should happen only once, in setup()).

[??? goto top](#DS18B20-thermostat-with-logging)

---

### Links

#### Hardware

- [16MB WeMos D1 Mini Pro # NodeMcu # ESP8266 ESP-8266EX CP2104 for Arduino NodeMCU](https://www.ebay.de/itm/16MB-WeMos-D1-Mini-Pro-NodeMcu-ESP8266-ESP-8266EX-CP2104-for-Arduino-NodeMCU/272405937539?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)

- [DS18b20 Wasserdichter Temperatursensor Thermometer Temperaturf??hler LS4G](https://www.ebay.de/itm/DS18b20-Wasserdichter-Temperatursensor-Thermometer-Temperaturf%C3%BChler-LS4G/232860153205?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)

- [Dual Relais Shield f??r WEMOS D1 Mini (ESP 8266) WIFI Modul Kit/Bausatz -Neu!](https://www.ebay.de/itm/Dual-Relais-Shield-fur-WEMOS-D1-Mini-ESP-8266-WIFI-Modul-Kit-Bausatz-Neu/143054227109?_trkparms=aid%3D333200%26algo%3DCOMP.MBE%26ao%3D1%26asc%3D20140107084234%26meid%3Df7ec875706584a5497b0bdaa8d4e8cbc%26pid%3D100011%26rk%3D3%26rkt%3D12%26sd%3D143021212740%26itm%3D143054227109&_trksid=p2047675.c100011.m1850)

- [0,96 Zoll I2C IIC serielle 128 x 64 OLED LCD LED Anzeige Modul f??r Arduino blau](https://www.ebay.de/itm/0-96-Zoll-I2C-IIC-serielle-128-x-64-OLED-LCD-LED-Anzeige-Modul-f%C3%BCr-Arduino-blau/173489709045?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)

- [Display 1.4 Shield V1.0.0 Erweiterung Oled Anzeige Karte](https://www.ebay.de/itm/Display-1-4-Shield-V1-0-0-Erweiterung-Oled-Anzeige-Karte/253565247621?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)

- [TTP223 Capacitive Kapazitiver Touch Sensor Modul | Arduino Raspberry Pi ](https://www.ebay.de/itm/5x-TTP223-Capacitive-Kapazitiver-Touch-Sensor-Modul-Arduino-Raspberry-Pi/273501890807?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)

#### Software

- [WiFiManager](https://github.com/tzapu/WiFiManager)

- [Moment.js](https://momentjs.com/)

- [prettyprint.js](https://github.com/padolsey-archive/prettyprint.js)

- [Adding WiFiManager](http://www.martyncurrey.com/esp8266-and-the-arduino-ide-part-5-adding-wifimanager/#more-7245)

- [ESP8266-12E-DHT-Thermostat](https://github.com/dmainmon/ESP8266-12E-DHT-Thermostat)

- [amake](https://github.com/pavelmc/amake)

[??? goto top](#DS18B20-thermostat-with-logging)

---

### Images

<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-01.png" alt="pic-01" width="290px" height="210px"> <img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-02.png" alt="pic-02" width="290px" height="210px"> <img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-03.png" alt="pic-03" width="290px" height="210px">

<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-04.png" alt="pic-04" width="440px"><img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-05.png" alt="pic-05" width="440px">

<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-06.png" alt="pic-06" width="290px" height="210px" padding="10px"><img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-07.png" alt="pic-07" width="290px" height="210px" padding="10px"><br>
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-08.png" alt="pic-08" width="290px" height="310px" padding="10px"><img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-09.png" alt="pic-09" width="290px" height="310px" padding="10px">
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/pic-10.png" alt="pic-10" width="440px">

[??? goto top](#DS18B20-thermostat-with-logging)

### Screenshot graph page

<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-01.png" alt="screen-01">
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-02.png" alt="screen-02">

### Screenshot settings page
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-03.png" alt="screen-03">

### Screenshot console log
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-04.png" alt="screen-04">

### Screenshot device response
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-05.png" alt="screen-05">

### Screenshots device WiFi configuration
<img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-06.png" alt="screen-06" width="320px"><img src="https://github.com/netphantm/Arduino_thermostat/raw/master/pics/screen-07.png" alt="screen-07" width="320px">

[??? goto top](#DS18B20-thermostat-with-logging)

---

### License

[![License](http://img.shields.io/:license-mit-blue.svg?style=flat-square)](http://badges.mit-license.org)

- **[MIT license](http://opensource.org/licenses/mit-license.php)**
- Copyright 2018 ?? [Hugo](https://github.com/netphantm).

[??? goto top](#DS18B20-thermostat-with-logging)

