# StmMultiBoot
Source files for Serial bootloader for use on the STM based Multi Module

To use this bootloader, you will need first to flash it to the module. Download the source files from the Github repository. Double click on the StmBoot.ino file to open the Arduino IDE. I'm assuming you already have this configured for building for the STM processor as it is needed for the Multi code.
In "Tools", set the board to Generic STM32F013C series, the variant to 128K flash, and the Upload method to "Serial".
You will need to connect to the serial port of the board, using a FTDI, or similar, interface, and fit the link to make it boot in the inbuilt bootloader. Again, this is the same as used for flashing the Multi code.
Apply power to the Multi board, and click "Upload" in the Arduino IDE. The code should be compiled and flashed to the board.

Next, build the Multi code for flashing using a bootloader, in "Tools", set the Upload method to STM32duino bootloader, then use the "Export compiled binary" option to build the Multi firmware. This will put a .bin file in the sketch directory, you may wish to rename the file to have a shorter filename. Copy this file to "FIRMWARE" directory of the SD card in your radio.

You will also need to update the ersky9x firmware on your radio with the P221 "e2" test version (which I'll post soon). Start the radio in "maintenance mode", select "Update Multi", select the flashing options that depend on how your module is installed, and choose "Update". Select the file you put on the SD card and confirm to start flashing.

If you build using the "multiprotocol.ino" file I posted above, with "CHECK_FOR_BOOTLOADER" uncommented in _config.h, then flashing again later should work in the same way. If the Multi code doesn't have this bootlaoder check in, then you need the dial set to 0 and have the bind button pressed at power on to force the bootloader to run.

This bootloader will also work if you use the serial port, however you need to do some fiddling. First you need a .hex file of the Multi code, which you need to edit so it loads at address 2000 instead of 08002000. Then you use AvrDude set to use an ATMEGA128 and an Arduino programmer at 57600 baud to do the flashing.

When you do the build, a .bin file is exported, but you should find a .elf file in the "build" directory. I obtain a .hex file with:
arm-none-eabi-objcopy -O ihex ..\..\Build\Multiprotocol.ino.elf multi.hex
This hex file will start with a line:
:020000040800F2
change this to:
:020000040000FA
Further down the file, if the firmware is large enough, you will find a line:
:020000040801F1
change this to:
:020000040001F9
Then just use AvrDude to flash this .hex file to the STM board, but choose a processor of ATMEGA128, programmer Arduino, baudrate 57600 and the COM port of your FTDI adapter.

