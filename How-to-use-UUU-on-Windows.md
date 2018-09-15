UUU works for both Linux and Windows distributions. This page describes the steps on Windows.

## Download the needed files

To use UUU on windows the following files are required.

* `libusb-1.0.dll`
* `uuu.exe`

The files can be downloaded from the [GitHub's Release tab](https://github.com/NXPmicro/mfgtools/releases)

Besides the USB Lib DLL (`libusb-1.0.dll`) file and the application executable file (`uuu.exe`) the target artifacts (such as boot loaders, kernel, and root filesystem images) should in the same folder.

Additionally, the file `uuu.auto` must also be placed in the same folder as the application executable file. The `uuu.auto` is released inside the IMX BSP Release bundle [link] along with all IMX BSP Release artifacts such as the `.sdcard`, `zImage`, boot loaders and DTB files.

The file `uuu.auto` configures UUU to flash all needed artifacts to the target.

### L4.9.123_2.3.0_8MM GA

In the `L4.9.123_2.3.0_8mm-ga` case, the `uuu.auto` builds all the artifacts to the target eMMC. The folder content is:

```console
 Directory of C:\Users\people\L4.9.123_2.3.0_8mm-ga

15-Sep-18  09:36 AM    <DIR>          .
15-Sep-18  09:36 AM    <DIR>          ..
13-Sep-18  10:08 AM     2,264,924,160 fsl-image-validation-imx-imx8mmevk.sdcard
12-Sep-18  07:24 PM         1,349,460 imx-boot-imx8mmevk-sd.bin-flash_evk
12-Sep-18  03:56 PM           157,184 libusb-1.0.dll
12-Sep-18  04:17 PM               581 uuu.auto
12-Sep-18  10:07 AM           292,352 uuu.exe
```

:heavy_exclamation_mark: Use the command `type uuu.auto` to double check the images filenames used by default.

## Install the dependencies

The list of dependencies to use UUU on Windows is listed below.

1. `libusb-1.0.dll`
2. (optional) A serial console emulator (such as [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or [TeraTerm](https://osdn.net/projects/ttssh2/releases/))
3. (optional) The USB to serial converter driver.

## Using the UUU

:heavy_exclamation_mark: Open the lower `COM` port to monitor the Cortex A* log messages while UUU is being executed.

The `uuu` can be used from any window, just double click on the `uuu.exe` file, the file is executed following the instructions of `uuu.auto` script.

Other option is to open some shell, for example `CMD` (which is available on any Windows version) or `PorwerShell` (which can be downloaded and installed)

1. Turn one the board
    1. Connect the USB power supply to the `J302` connector (the USB Type-C POWER connector)
    2. Connect the USB Type-C cable to the serial download connector (`J301`) to the host machine
    3. Connect the serial debug (connector `J901`) to the host machine using a USB micro-B cable
    4. Set the boot mode switches to the `Download Mode` (see sticker or silk on the board)
    5. Turn on the board on ON/OFF switch (`SW101`)
2. Open `CMD`
3. Navigate to the folder with the `uuu.exe`, `uuu.auto`, `libusb-1.0.dll`, and the images to be installed on the target.
4. Execute `uuu.exe`

The UUU starts the flash process by loading the boot loader to the RAM, and using it to make the copy. In case everything works fine the final message shows zero errors. See the image below with the UUU execution on `CMD` on the left and the Cortex A53 log message on Putty on the right.

[[images/uuu-finish-with-no-error.png]]

## How to update only the bootloader on the SDCard

    uuu.exe -b sd imx-boot-imx8mmevk-sd.bin-flash_evk
