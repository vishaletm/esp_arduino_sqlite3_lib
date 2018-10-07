# Sqlite3 Arduino library for ESP8266
This library enables access to SQLite database files from SPIFFS or Micro SD Card through ESP8266 SoC using WeMos D1 mini and MicroSD Shield (shown below) or NodeMCU v1.0 with separate MicroSD Module.

![](d1_mini_msd_shield_strip.png?raw=true)

## Usage
Sqlite3 C API such as `sqlite3_open` can be directly invoked. Before calling please invoke:

```c++
   vfs_mount("/SD0", SS); // for Micro SD Shield
   File db_file_obj_1; vfs_set_spiffs_file_obj(&db_file_obj_1); // For SPIFFS
```
apart from `SPI.begin()` or `SPIFFS.begin()` as appropriate.

The SS Pin is D8 on the Micro SD Shield for WeMos D1 mini.  It can be changed accordingly.

Please see the examples for full illustration of usage for the two file systems. The databases need to be copied to the Micro SD card root folder before the SD example can be used.  Please see the comments section of the example.

## Installation
Please download this library, unzip it to the libraries folder of your ESP8266 sdk location. The location varies according to your OS.  For example, it is usually found in the following locations:
```
Windows: C:\Users\(username)\AppData\Roaming\Arduino15
Linux: /home/<username>/.arduino15
MacOS: /home/<username>/Library/Arduino15
```
Under Arduino15 folder please navigate to `packages/esp8266/hardware/esp8266/<version>/libraries`

If you do not have the ESP8266 sdk for Arduino, please see http://esp8266.github.io/Arduino/versions/2.0.0/doc/installing.html for installing it.

## Dependencies
The SdFat library is required for accessing MicroSD card.  This library can be donwloaded from https://github.com/greiman/SdFat.

The Sqlite3 code is included with the library.

## Limitations on ESP8266
* The default page size of 4096 leads to "Out of memory" as the size increases over 500 records. Please use page size of 512 using the commands `PRAGMA page_size=512; VACUUM;`, if you are planning to use your own sqlite3 files.
* Inserting records over a 1000 records gives "Out of memory"
* These problems exist on NodeMCU as well due to low memory on ESP8266
* Retrieving from db having 10 million records has been tested. But it needs stack space to be increased to atleast 6144 bytes.  Please modify cores/esp8266/cont.h to increase stack size.
* It takes around 1 second to retrieve from such dataset, even using the index.

## Limitations of this library
* Multiple SD Cards can be supported (using multiple CS Pins). But as of now only one SD Card is supported (`/SD0`).  
* Before opening database files from SPIFFS, the `vfs_set_spiffs_file_obj()` should be called with a reference to SPIFFS file object
* A prefix (in front of filenames) such as `/FLASH/` is to be used for SPIFFS and `/SD0/` is to be used for Micro SD,  for opening databases.

## Acknowledgements
* This library was developed by modifying the VFS layer developed by [Luiz Felipe Silva](https://github.com/luizfeliperj). The documentation can be found [here](https://nodemcu.readthedocs.io/en/master/en/modules/sqlite3/).
* The census2000 and baby names databases were taken from here: http://2016.padjo.org/tutorials/sqlite-data-starterpacks/. But no license information is available.
* The mdr512.db (Million Domain Rank database) was created with data from [The Majestic Million](https://majestic.com/reports/majestic-million) and is provided under CC 3.0 Attribution license.
* The [ESP8266 core for Arduino](https://github.com/esp8266/Arduino)
* [The Arduino platform](https://arduino.cc)
