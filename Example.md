# Basic 

## Download boot loader for imx6\imx7

    uuu uboot.imx

## Download boot loader for imx8qxp

    uuu flash.bin

## Download SPL and uboot, such as imx8mq.

    uuu sdp: boot -f flash.bin
    uuu sdpu: write -f flash.bin -offset 0x57c00 -addr 0x40800000
    uuu spdu: jump -addr 0x40800000

## Burn Android Image to eMMC

    uuu android.zip

## Burn Yocto Image to eMMC

   uuu yocto.sdcard

# Talk with fastboot

## boot linux kernel

   uuu FB: ucmd setenv fastboot_buffer ${loadaddr}
   uuu FB: download –f Image
   uuu FB: ucmd setenv fastboot_buffer ${fdt_addr}
   uuu FB: download –f imx8qxp_mek.dtb
   uuu FB: acmd booti ${loadaddr} - ${fdt_addr}

## write image to emmc

   uuu FB: flash -raw2sparse raw <image file>

 
