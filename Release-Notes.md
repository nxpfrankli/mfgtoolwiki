## Version: 2.8.0

### New Features
* Support QXP B0 chip
* Support USB3.0
* Support BCDDevice distinguish QXP B0 and A0 

## Version: 2.1.0

This application is verified on WIN7 32bit/64bit and WINXP Professional. It is not verified on other windows platforms.

### Basic Features

* Both console and gui interfaces
* In gui interface color based flag used to indicate users the status of the work:
* Blue indicates the device is being processed
* Green indicates the device was successfully processed, and indicates to tester it can be replaced with a fresh device independent of the other devices’ progress. 
* Red indicates the device failed to process.
* Continuously monitors device removal/arrivals. 
* It will automatically restart the process when a processed device is replaced with a new device.
* Will start processing the first device detected, and allows replacing each device after completion instead of waiting for all devices to complete. 

### Change List

* UI change:
* Add console support so that the user can run the APP from command line.

### Known Issues

* In console interface, there isn’t any buttons and commands that can be input, so the process will be started automatically when run the application.
* If manufacturing tool v2.0 application is executed on Windows7, when it runs in the “updater” phase, a popup message will be shown to ask whether disk should be formatted. Please ignore it, or click the "Cancel" button. It will not affect any function.
* When the xml file (ucl2.xml) or the configuration file (cfg.ini or UICfg.ini) is modified while the application is running, the change will not work until the application restarts.
