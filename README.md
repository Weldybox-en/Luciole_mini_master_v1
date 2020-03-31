# Luciole_mini_slave_v1

Luciole is an RGB controller controlled though WIFI. No need extra applications because the board carry a Webserver. Luciole has multiple operating mode in addition to the basics functionalities that you can find in a classic RGB controller. The goal with this board is to provide and very simple and lowcost way to control RGB LED.

The Luciole_mini_master_v1 is very important. It work like the luciole classic, but each time he change his colors, he will send messages to any slaves that listen to the same channel thanks to an NRF24 module.

<p  align="center">
<a href="https://www.tindie.com/stores/julfi/?ref=offsite_badges&utm_source=sellers_julfi&utm_medium=badges&utm_campaign=badge_medium"><img src="https://d2ss6ovg47m0r5.cloudfront.net/badges/tindie-mediums.png" alt="I sell on Tindie" width="150" height="78"></a>
</p>

<p align="center"><i>Overview of the board that i <b>will</b> sell with this code.</i></p>
<p align="center">
  <img src="https://imgur.com/SNt15PF.jpg" width="500">
</p>

<b>Click [here](https://easyeda.com/weldybox/luciole-mini) to see his schematic!</b>


# Contents
- [Webserver quick view](#webserver-quick-view)
- [Goals](#goals)
- [Implementation](#implementation)
  - [Librairy](#librairy)
  - [Flashing ESP8266](#flashing-esp8266)
    - [Platformio](#platformio)
    - [Arduino IDE](#arduino-ide)
- [How it works?](#how-it-works)
- [My networks](#my-networks)
- [Support me](#support-me)

# Webserver quick view

The webserver looked like this :

<p align="center">
  <img src="https://imgur.com/XMOOdEB.jpg" width="300">
  <img src="https://imgur.com/52pnw5x.jpg" width="300">
</p>


The frist page is the most important because all the functionalities can be reach from here while the second is simply the alarm settings.

# Goals

All the goals that i want to be implemented/is implemented on luciole's board.

- [x] Any color selection **w/Brightness**
- [x] Color storing **(up to 5)**
- [x] Smart light **/w colors settings**
- [x] Alarm light
- [x] RF master/slave communication

# Implementation

## Librairy

In order to make luciole working there is some things to do. Firstly, luciole use some librairy that i have manually modified to fit perfectly with the app.

Here is the links of the two modified librairy that you need to make it work:
- [WiFiManagerByjulfi](https://github.com/Weldybox-en/WiFiManagerByjulfi)
- [NTPClientByjulfi](https://github.com/Weldybox-en/NTPClientByjulfi)


 Here is the librairy list that you have to had:

```cpp
#include <Arduino.h>
#include <ESP8266WiFi.h>
#include <EEPROM.h>
#include <ArduinoOTA.h>
#include <SPI.h>
#include <WebSocketsServer.h>
#include <ESP8266WebServer.h>
#include <FS.h>
#include <ArduinoJson.h>
#include <ESP8266HTTPClient.h>
#include <WiFiUdp.h>
#include <NTPClientByjulfi.h>
#include <WiFiManagerByWeldy.h>
```
## Timezone settings

For the moment there is no way to modify your time zone though the user interface. By default it's set at UTC+1 and this setting is given thanks to the NTPClientByjulfi librairy.

The unique way to do it is by attribut you utc shift value in the utcOffsetInSeconds variable.

```cpp
unsigned long utcOffsetInSeconds = "your utc shift" ;
```
For me in France, the winter utc shift is *__3600__* and the summer is *__7200__*. I invite you to test and modify it according to your timezone.

## Flashing ESP8266

__NOTE__ : If you have order a board from me, you will automatically have this code flashed. No boards are sent faulty!

__BE CAREFUL__ : This version of Luciole don't have any microUSB port. So you need to have an FTDI chip in order to program it properly. In this version we don't use the classic pins at the bottom of it but those on the side. I advice you to solder male pins like i did on the photo bellow.

<p align="center">
  <img src="https://imgur.com/ZK2JCPy.jpg" width="500">
</p>

On the boards there is a little drawing that symbolizes the ground. Make sure you connect the FTDI corretly when you want to upload a sketch.


<p align="center">
  <img src="https://imgur.com/WAMRYbg.jpg" width="500">
</p>


### Platformio

This github repository is already a platformio project. So you just need to clone it on your project folder. Then run the following command line on the terminal or use the upload button upload the code.

```cpp
pio run -t upload
```

If you have any problems with the board, the first thing you need to do is erease the flash memory and retry to upload your sketch. Here is the command that you need to run in platformio in order to do that:

```cpp
esptool.py --port <your_ESP_port> erase_flash
```

<p align="center"><i>For more informations about the upload methods or the general uses of platformio you can go read the <a href="https://docs.platformio.org/en/latest/platforms/espressif8266.html" target="_blank">platformio documentation</a></i></p>

### Arduino IDE

Using arduino IDE harder but it's possible. Just make sure that you have all the necessary things to upload the code into an ESP8266. See the screen below :

<p align="center">
  <img src="https://imgur.com/P5R15kP.jpg" width="420">
  <img src="https://imgur.com/7Ty1Hft.jpg" width="420">

</p>

Copy the code found in the <i>src/main.cpp</i> and make sure to have all the labrairies on your arduino librairy folder. To upload , select "LOLIN(WEMOS) D1 R2 & mini" esp8266 type of board the FTDI programmation port.


# How it works?

In this section we will rapidly see how the code works. Because i've already make a documentation about the main features of the luciole project i invite to [see](https://github.com/Weldybox-en/Luciole-v1.0/blob/master/README.md) it if your not aware about what we are talking about. Here i will only write about the new feature relative to the NRF24 radio module that come with this version.

This one is based on the basic emitter/transmitter NRF24 example. I've not touched the void setup() function. In fact, this one is pretty simple, the program will test whether or not the board is ready to go and then set his communication channel to 1.
```cpp
  if (!nrf24.init())
  Serial.println("init failed"); // Defaults after init are 2.402 GHz (channel 2), 2Mbps, 0dBm

  if (!nrf24.setChannel(1)) //We are communicating on the channel 1, you can go up to 125 channels
    Serial.println("setChannel failed");
  if (!nrf24.setRF(RH_NRF24::DataRate2Mbps, RH_NRF24::TransmitPower0dBm))
    Serial.println("setRF failed");
}
```
Finally, we will focus on sending the data each time the LED's color change. The fiew lines bellow are thus placed each time we wan't to update the slave's color.

```cpp
//NRF24 transmit
int colorValArray[3] = {red, green, blue}; //int color array
String colorNameArray = "rgb"; //string that contains the r, g and b key words

for(int c = 0;c<=2;c++){
   String colorNRF = String(colorNameArray[c]);
   colorNRF += colorValArray[c];
   uint8_t data[sizeof(colorNRF)]; //memory space that will contains the usingned int data of the word sent
   for(int i = 0;i<sizeof(colorNRF);i++){
     data[i]=uint8_t(colorNRF[i]); //converter from char (ex:"r345") to unsigned int
   }

//data sent
nrf24.send(data, sizeof(data));
nrf24.waitPacketSent();
```
- The first part is the declaration of the word to send. As you might guess, the order to change the slave's color is composed of three messages, red, green and blue symbolized by the chars 'r', 'g' and 'b'.

- The second part will construct the messages based on the preview declaration word and the color's value that is from 0 to 255.

- Finally, the module send the data to wait for an acknowledgment.

# My networks

 - Twitter: https://twitter.com/julfiofficial
 - Blog : http://weldybox.com/
 - Youtube channel : https://www.youtube.com/channel/UCTNOaG1svq1xgBzBOvLj6vw?view_as=subscriber

# Support me

I've decide to share for free my work because i've learn tanks to that! For me, sharing project like this for free accelerate the knowledge circulation and that very cool.

But i'm student and like every student i'm broken :(
So, if you can, feel free to buy me a coffee :D

[![paypal](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/jvZuQ8SYo)
