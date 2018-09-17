# Basic 

## Download boot loader for imx6\imx7

    uuu uboot.imx

## Download boot loader for imx8qxp

    uuu flash.bin

## Download SPL and uboot, such as imx8mq.

    uuu sdp: boot -f flash.bin
    uuu sdpu: delay 1000
    uuu sdpu: write -f flash.bin -offset 0x57c00
    uuu sdpu: jump

## Burn Android Image to eMMC

    uuu android.zip

# Talk with fastboot

## boot linux kernel

    uuu FB: ucmd setenv fastboot_buffer ${loadaddr}
    uuu FB: download –f Image
    uuu FB: ucmd setenv fastboot_buffer ${fdt_addr}
    uuu FB: download –f imx8qxp_mek.dtb
    uuu FB: acmd booti ${loadaddr} - ${fdt_addr}

Extended environment for fastboot

    fastboot_buffer      Image download address
    fastboot_bytes       reflect previous download image byte size 

## write image to emmc

    uuu FB: flash -raw2sparse all <image file>

# built-in script

    uuu -b emmc bootloader              Write bootloader to emmc
    uuu -b qspi bootloader              write bootloader to sd
 
# multi boards support

## For the same boards

   uuu -d uuu.auto                      The same boards connected

## For the difference boards

   uuu -d -m 1:1 2:1 boardA_uuu.auto           monitor port 1:1 and 2:1 for boardsA.
   uuu -d -m 1:3: 4:1 boardB_uuu.auto          monitor port 1:3 and 4:1 for boardsB. 

**Note: please avoid monitor the same port by difference uuu instance, which cause unexpected result.**        