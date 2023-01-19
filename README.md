# kou_comodule_task

Comodule GPS coordinates beacon task

## System definition

The system is a ble beacon, which receives GPS coordinates from UART peripheral and advertises the coordinates as UUID NUS ble service

## Minimum requirements

- an nrf52840 devkit
- if needed to reprogram, an IDE with configured env. SES or µVision Keil and nRF SDK V16 or higher. Copy/replace the main.c file into a copy of the SDK following directory ../<SDK_directory>/examples/ble_peripheral/ble_app_uart then build and download to target devkit
- a computer with terminal emulator for UART entry i.e. PuTTY, Tera Term, arduino IDE, Automated Serial Terminal (these programs are suggested for Windows, for any other OS any similar program would the same and some of them have versions for different OS)
- a smartphone with nRF Connect app for displaying out the ble advertised data

## Brief run steps

- power up the devkit (recommended through USB) and reset it
- open nRF Connect app on the smart phone, connect to "GPS beacon", enable UART TX characteristic, from the Attribute table
- open the terminal emulator (adjust settings -if needed- info in the following section Run process)
- write GPS coordinates or messages on the terminal emulator and watch the UART TX Characteristic change in the nRF Connect phone app
- accepted GPS coordinates formats and accuracy
  - 12.123456, 123.123456
  - 12.123456: 123.123456

## Installation and run process

- power up the devkit, where the program has been flashed
  - if needed, download the program into an erased devkit
- reset the devkit
- open nRF Connect app on the smart phone
  - scan devices
  - connect to device "GPS beacon"
  - extend the Attribute Table and enable read value of UART TX Characteristic
- open the terminal emulator
  - for PuTTY
    - select connection type serial
    - select COM device for instance "COM3"
    - select baud rate Speed 115200
  - for Tera Term
    - if Tera Term V >= 4, Setup menu > Restore setup and browse "TERATERM.INI" and modify the COM port if different
    - select serial and choose the device COM port
    - from Setup menu > Serial choose baud rate speed 115200
    - from Setup menu > Terminal
      - new line > receive choose CR+LF
      - new line > transmit choose CR+LF
      - if one needs to see the last sent message from the terminal mark Local echo checkbox (recommended)
  - for arduino
    - Tools > Serial Monitor or open serial monitor
    - choose the COM port and update baud rate speed
- write GPS coordinates or messages on the terminal emulator and watch the UART TX Characteristic change in the nRF Connect phone app
  - accepted GPS coordinates formats and accuracy are as the following
    - 12.123456, 123.123456
    - 12.123456: 123.123456
    - any value of latitude more than or less than 90.0 or -90.0 respectively, will return an error message
    - any value of longitude more than or less than 180.0 or -180.0 respectively, will return an error message
    - either parameter latitude or longitude decimal precision less than 5 places will contain extra message that the accuracy is too low, and the coordinates will be advertised still
    - either parameter latitude or longitude decimal precision more than 6 places will be approximated to 6 places

## Logic behind the beacon

- The program was built on top of ble_app_uart of nrf SDK, where it basically initializes all modules needed logging, uart, ble, power management and then enters an idle state of waiting a devie to be connected, once a device does, it starts handling uart interrupts and advertise user-defined messages; in this case input from terminal emulator
- The input format is Google maps standard, and it was chosen for demonstration purpose, however the GNSS format of nRF91 for instance is can be found here <https://devzone.nordicsemi.com/guides/cellular-iot-guides/b/software-and-protocols/posts/nrf91-series-gnss-only-mode-and-lte-gnss-field-verification>
- The input accuracy is GNSS nRF91 normal accuracy mode that requires 4 satellites to calculate a 3D fix, it could be less accurate, for instance Google maps usually show pinpoints to 5 decimal places <https://en.wikipedia.org/wiki/Decimal_degrees> according to decimal degrees notation precision, a range of 5-6 decimal places should be sufficient to locate trees, objects, or people
- The output is shown in between 5-6 decimal places, with comments if less accurate for instance; a wrong input format would show an error message; an out-of-range coordinates will show an error message as well, nominally latitude must be between -90 and 90 degrees, and longitude must be between -180 and 180 degrees

## Result

- Write a GPS coordinates onto the terminal emulator, press enter and watch the client side (nRF Connect phone app) advertises the coordinates, to show results a client has to be connected

## Notes

- Please note that the solution could have been done in other ways
- Some embedded software concepts were bypassed or done in demonstrative manner to show language proficiency and vise-versa, if there are notices that the software could be better, you could not be more right
