# Luciole_mini_slave_v1

Luciole is an RGB controller controlled though WIFI. No need extra applications because the board carry a Webserver. Luciole has multiple operating mode in addition to the basics functionalities that you can find in a classic RGB controller. The goal with this board is to provide and very simple way to control RGB LED for a very low price.

The Luciole_mini_slave_v1 is simpler than the master one. In fact, it just need to wait for informations sent from the master and use them to command each LEDs.

<p  align="center">
<a href="https://www.tindie.com/stores/julfi/?ref=offsite_badges&utm_source=sellers_julfi&utm_medium=badges&utm_campaign=badge_medium"><img src="https://d2ss6ovg47m0r5.cloudfront.net/badges/tindie-mediums.png" alt="I sell on Tindie" width="150" height="78"></a>
</p>

<p align="center"><i>Overview of the board that i sell with this code.</i></p>
<p align="center">
  <img src="https://imgur.com/SNt15PF.jpg" width="500">
</p>

<b>Click [here](https://easyeda.com/weldybox/luciole-mini) to see his schematic!</b>


# Contents

- [Implementation](#implementation)
  - [Librairy](#librairy)
  - [Flashing ESP8266](#flashing-esp8266)
    - [Platformio](#platformio)
    - [Arduino IDE](#arduino-ide)
- [How it works?](#how-it-works)
- [My networks](#my-networks)
- [Support me](#support-me)


# Implementation

## Librairy

The code is really simple and need just one librairy to work, it's the Radiohead one! You can download it manually [here](https://github.com/sparkfun/SparkFun_RadioHead_Arduino_Library) or simply use the librairy manager.

In addition the librairy i want to attract you attention on the LEDs pins declaration. These can be differents depending on the LED strip configuration that you have.

```cpp
#include <SPI.h>
#include <RH_NRF24.h>

#define REDPIN 4
#define GREENPIN 5
#define BLUEPIN 16
```

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

Using arduino IDE is pretty straight forward comparing to the master's code. Just copy the code from */src/main.cpp* and past it in your Arduino IDE sketch.

Just make sure that you have all the necessary things to upload the code into an ESP8266. See the screen below :

<p align="center">
  <img src="https://imgur.com/P5R15kP.jpg" width="420">
  <img src="https://imgur.com/7Ty1Hft.jpg" width="420">

</p>


# How it works?

In this section we will rapidly see how the code works. This one is based on the basic emitter/transmitter NRF24 example. I've not touched to the void setup() function, so i will not write about it here.

The void loop() function though is quite interesting to review. The first part is about testing if the NRF24 is working and building a buffer that will contains the received data.
```cpp
  if (nrf24.available())
  {
    //Buffer with a length as long as the max message length from NRF24
    uint8_t buf[RH_NRF24_MAX_MESSAGE_LEN];
    uint8_t len = sizeof(buf);
```
Then we test if we are receiving something.
```cpp
    if (nrf24.recv(buf, &len))
    {
```
And finally, we stock the data under a string type and we categorized the message received. The first char show which color is concerned by the folowings.

For example, messages that asking to display a white color will look like this:
```
r255
g255
b255
```
So it is in the next three chars where the useful information is taken by and wrote on the analog pins.
```cpp
   String str((char*)buf);

      for(int i = 0; i<len; i++){
        //Then we test the first char to determine whether it's a message for the red, green or blue color
        if(buf[i] == 'r'){
          Serial.print("rouge = ");
          analogWrite(REDPIN, (str.substring(i+1,i+4)).toInt());
          Serial.println(str.substring(i+1,i+4)); //debug print
        }if(buf[i] == 'g'){
          Serial.print("vert = ");
          analogWrite(GREENPIN, (str.substring(i+1,i+4)).toInt());
          Serial.println(str.substring(i+1,i+4)); //debug print
        }if(buf[i] == 'b'){
          Serial.print("bleu = ");
          analogWrite(BLUEPIN, (str.substring(i+1,i+4)).toInt());
          Serial.println(str.substring(i+1,i+4)); //debug print
```

# My networks

 - Twitter: https://twitter.com/julfiofficial
 - Blog : http://weldybox.com/
 - Youtube channel : https://www.youtube.com/channel/UCTNOaG1svq1xgBzBOvLj6vw?view_as=subscriber

# Support me

I've decide to share for free my work because i've learn tanks to that! For me, sharing project like this for free accelerate the knowledge circulation and that very cool.

But i'm student and like every student i'm broken :(
So, if you can, feel free to buy me a coffee :D

[![paypal](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/jvZuQ8SYo)
