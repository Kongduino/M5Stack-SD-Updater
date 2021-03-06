# M5Stack-SD-Updater

**M5Stack-SD-Updater is an [Platform.io](https://platformio.org/lib/show/2575/M5Stack-SD-Updater)/[Arduino library](https://www.arduinolibraries.info/libraries/m5-stack-sd-updater) for [M5Stack](http://m5stack.com/) to package you apps on a SD card and load them from a menu.**
It is inspired by gamebuino, however it does not use a modified bootloader.

[![License: MIT](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/tobozo/M5Stack-SD-Updater/blob/master/LICENSE)
[![Build Status](https://travis-ci.org/tobozo/M5Stack-SD-Updater.svg?branch=master)](https://travis-ci.org/tobozo/M5Stack-SD-Updater)

[![ M5Stack Apps loaded from SD Card](https://img.youtube.com/vi/myQfeYxyc3o/0.jpg)](https://www.youtube.com/watch?v=myQfeYxyc3o)

[<img alt="Visual demo for M5Stack Info Window with QR Code" title="M5Stack Info Window with Credits and QR Code" src="https://camo.githubusercontent.com/06570c553f4ca575d60e7d7ff81ea2d8d555e40d/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f44596d4c75396e573041416c71682d2e6a7067" data-canonical-src="https://pbs.twimg.com/media/DYmLu9nW0AAlqh-.jpg" width=480>](https://twitter.com/TobozoTagada/status/972845518966743040)

🏭 PREREQUISITES:
-----------------
**Make sure you have the following libraries:**
  
- M5Stack-SD-Updater (this project): get it from the [Arduino Library Manager](https://www.arduinolibraries.info/libraries/m5-stack-sd-updater) or by performing a [manual installation](https://www.arduino.cc/en/Guide/Libraries#toc5)
- MSStack: get it from the [Arduino Library Manager](https://www.arduinolibraries.info/libraries/m5-stack-sd-updater) or by performing a manual installation from the [M5Stack repo](https://github.com/m5stack/M5Stack).
- ArduinoJSON: [https://github.com/bblanchon/ArduinoJson/](https://github.com/bblanchon/ArduinoJson/) available in the Arduino Library Manager
- QRCode: [https://github.com/ricmoo/qrcode](https://github.com/ricmoo/qrcode) available in the Arduino Library Manager
- M5StackSAM: [https://github.com/tomsuch/M5StackSAM](https://github.com/tomsuch/M5StackSAM) 

🍱 UNPACKING
------------
Open both sketches from the "examples/M5Stack-SD-Update" menu.


**1) Compile the "M5Stack-SD-Menu.ino" example.** This sketch is the menu app. It must be compiled once (sketch / export compiled binary) and saved on the SD Card root directory as "menu.bin" for persistence. It should also be flashed on the M5Stack.


**2) Merge and compile the "M5Stack-SDLoader-Snippet.ino" example.** It is possible to embed any M5 app by implementing the 
  M5Stack SD Loader Snippet. This sketch can be used as a boilerplate to code an app from scratch.

  For an existing M5 app, find the line:

      #include <M5Stack.h>
      
  And add this:
      
      #include "M5StackUpdater.h"
      
  In your setup() function, find the following statements:

      M5.begin();
      Wire.begin()

  And add this after 'Wire.begin();':

      if(digitalRead(BUTTON_A_PIN) == 0) {
        Serial.println("Will Load menu binary");
        updateFromFS(SD);
        ESP.restart();
      }
      
  Export the compiled binary (Sketch/Export Compiled Binary) and choose one of the above methods to get the app on the M5Stack:

  - Manually copy it to the sd card
  - Existing installations (menu.bin already copied and loaded on the M5Stack): clear the content of the [examples/M5Stack-SD-Menu/data](https://github.com/tobozo/M5Stack-SD-Updater/tree/master/examples/M5Stack-SD-Menu/data) folder, copy the compiled binary there and use the [ESP32 Sketch Data Uploader](https://github.com/me-no-dev/arduino-esp32fs-plugin) utility (available from the Tools menu in the Arduino IDE).


**3) Repeat this for all applications**, jpg images or json meta files to import


📚 USAGE:
---------
When an app is loaded in memory, booting the M5Stack with the BTN_A pushed will flash back the menu.bin into memory. 

When the menu is loaded in memory, it will list all available apps on the sdcard and load them on demand. 

Booting the M5Stack with the BTN_A pushed will power it off.

The [ESP32 Sketch Data Uploader](https://github.com/me-no-dev/arduino-esp32fs-plugin) can be used to send .bin, jpg, json, mod and mp3 files onto the M5Stack. The menu.bin will take care of dumping them on the SD Card.

Some artwork/credits can be added for every uploaded binary, the menu will scan for these file types:
  
  - .bin compiled application binary
    
  - .jpg image/icon (max 200x100)
    
  - .json file with dimensions descriptions: 
  
  `{"width":128,"height":128,"authorName":"tobozo","projectURL":"http://blah","credits":"thanks"}`


  ⚠️ The jpg/json file names must match the bin file name, case matters!
  jpg/json files are optional but must both be set if provided.
  The value for *projectURL* property will be rendered as a QR Code in the info window.


🚫 LIMITATIONS:
---------------
- SD Library limits file names (including path) to 32 chars, M5StackSAM has a slightly higher limit.
- FAT specifications prevent having more than 512 files on the SD Card, but this menu is limited to 256 Items anyway.
- Long file names will eventually get covered by the jpg image, better stay under 8 chars (not including the extension).


🔘 OPTIONAL:
------------
The JoyPSP Controls for M5Stack SD Menu necessary code can safely be disabled in the menu example.
It's using a [4 Wires PSP JoyPad breakout](https://www.google.fr/search?q=psp+joypad+breakout) on Pins 35 and 36, which are appropriate for analog reading.

The JoyPSP only handles up/down actions for the meantime, more controls will be added soon.

KNONW ISSUES
------------
Compilation #pragma warnings/errors in the Arduino IDE can be solved by setting the debug level to "default" in the Arduino preferences window.
See [#3](https://github.com/tobozo/M5Stack-SD-Updater/issues/3) 

vMicro: currently can't compile at all, see [#5](https://github.com/tobozo/M5Stack-SD-Updater/issues/5). Looking for a solution that works with both vMicro and Arduino IDE.


🛣 ROADMAP:
----------
Not defined yet, but looking at how fast this [library landed in platform.io](https://platformio.org/lib/show/2575/M5Stack-SD-Updater), there's a possibility it will soon exist in different flavours (i.e. as an ESP-IDF component) or with more [features](https://github.com/m5stack/faces). Contributors welcome!


#️⃣  REFERENCES:
--------------
- :clapper: [Generic Demo](https://youtu.be/myQfeYxyc3o)
- :clapper: [Demo with Pacman+sound](https://youtu.be/36fgNCecoEg) ([source](https://github.com/tobozo/M5Stack-Pacman-JoyPSP))
- :clapper: [NyanCat Demo](https://youtu.be/Zxh2mtWwfaE) ([source](https://github.com/tobozo/M5Stack-NyanCat))
- 🎓 [Macsbug'article](https://macsbug.wordpress.com/2018/03/12/m5stack-sd-updater/) (Japanese)


🙏 CREDITS
==========
- 👍 MSStack: [https://github.com/m5stack/M5Stack](https://github.com/m5stack/M5Stack)
- 👍 M5StackSam [https://github.com/tomsuch/M5StackSAM](https://github.com/tomsuch/M5StackSAM)
- 👍 ArduinoJSON: [https://github.com/bblanchon/ArduinoJson/](https://github.com/bblanchon/ArduinoJson/)
- 👍 QRCode: [https://github.com/ricmoo/qrcode](https://github.com/ricmoo/qrcode)
- 👍 [@Reaper7](https://github.com/reaper7) [https://github.com/reaper7](https://github.com/reaper7)


 
