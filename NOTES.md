# Flashing ESP32's to Espruino

## General Information
The ESP32 Typical Serial Port on my Mac: `/dev/tty.usbserial-0001`

I installed `esptool` using `brew` which placed the resulting `esptool.py` in my `$PATH`.

I also aliased `esptool` to `esptool.py` so I didn't have to type in the `.py` file extension every time I wanted to run
it.

## How to flash an ESP32 for the FIRST Time (i.e. it needs the bootloader also)

You can obtain the latest version of the firmware tarball (that includes the `bootloader.bin`) from [here](http://www.espruino.com/binaries/travis/master/).

* Download the latest firmware using the link above (if you desire too, elsewise there is a version in this repo under `firmware_files` directory that can be used)
* navigate your terminal window to this project and then to its subdirectory of `firmware_files`.
* Inside you should find the 3 main `bin` files:
  * `bootloader.bin`
  * `partitions_espruino.bin`
  * `espruino_esp32.bin`
* From that directory, and with the ESP32 plugged in via USB, figure out which tty COM port the ESP32 is showing up on.
  * To do so, run `ls /dev/tty*`.  You'll probably get a fairly long list you'll have to scroll through.
  * What you are looking for is one that says something like `usbserial` or `ACM0`.
  * Typically on my Mac, when I plugged in an Inland ESP32 board, it shows up as `/dev/tty.usbserial-0001`.
  * If you don't find anything that looks to be it, you may need to
  install a FTDI driver for CP21xx for your OS.
* With all this connected and in the `firmware_files` directory in the terminal and knowing which `tty` the ESP32 is on,
  you'll run the following command to actually flash the ESP32 the **first** time. (If it is a subsequent flash, skip
  this and go to the next main bullet (not sub-bullet))
  ```shell
  esptool \
    --chip esp32 \
    --port /dev/tty.usbserial-0001 \
    --baud 921600 \
    --after hard_reset write_flash \
    -z \
    --flash_mode dio \
    --flash_freq 40m \
    --flash_size detect \
    0x1000 bootloader.bin \
    0x8000 partitions_espruino.bin \
    0x10000 espruino_esp32.bin
  ```
<br />
<br />
  * You should get an output like this:

```shell
[10:36:59] firmware_files
// $ esptool \
  --chip esp32 \
  --port /dev/tty.usbserial-0001 \
  --baud 921600 \
  --after hard_reset write_flash \
  -z \
  --flash_mode dio \
  --flash_freq 40m \
  --flash_size detect \
  0x1000 bootloader.bin \
  0x8000 partitions_espruino.bin \
  0x10000 espruino_esp32.bin

esptool.py v4.6.1
Serial port /dev/tty.usbserial-0001
Connecting....
Chip is ESP32-D0WDQ6 (revision v1.0)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: c8:f0:9e:ae:4e:b8
Uploading stub...
Running stub...
Stub running...
Changing baud rate to 921600
Changed.
Configuring flash size...
Auto-detected Flash size: 4MB
Flash will be erased from 0x00001000 to 0x00005fff...
Flash will be erased from 0x00008000 to 0x00008fff...
Flash will be erased from 0x00010000 to 0x00177fff...
Compressed 19024 bytes to 12893...
Wrote 19024 bytes (12893 compressed) at 0x00001000 in 0.6 seconds (effective 276.7 kbit/s)...
Hash of data verified.
Compressed 3072 bytes to 171...
Wrote 3072 bytes (171 compressed) at 0x00008000 in 0.1 seconds (effective 186.2 kbit/s)...
Hash of data verified.
Compressed 1471440 bytes to 961264...
Wrote 1471440 bytes (961264 compressed) at 0x00010000 in 15.9 seconds (effective 739.0 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```

* If you are doing a **subsequent** (not the first time) flash of firmware, then the command will look like this:

```shell
esptool \
  --chip esp32 \
  --port /dev/tty.usbserial-0001 \
  --baud 921600 \
  --before esp32r0 \
  --after hard_reset write_flash \
  -z \
  --flash_mode dio \
  --flash_freq 40m \
  --flash_size detect \
  0x10000 espruino_esp32.bin
```

### After Flashing bootloader and firmware...
Proceed to a new project with your own javascript files for your ESP to run! :)
