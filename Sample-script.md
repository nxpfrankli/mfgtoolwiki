# Download spl and uboot for imx8mm and imx8mq

    uuu_version 1.0.1
    SDP: boot -f _flash.bin
    # This command will be run when use SPL
    SDPU: delay 1000
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    SDPU: done

# Burn bootloader to eMMC boot partition

    uuu_version 1.0.1
    SDP: boot -f _flash.bin
    # This command will be run when use SPL
    SDPU: delay 1000
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    # This command will be run when ROM support stream mode
    SDPS: boot -f _flash.bin

    FB: ucmd setenv fastboot_dev mmc
    FB: ucmd setenv mmcdev ${emmc_dev}
    FB: ucmd mmc dev ${emmc_dev}
    FB: flash bootloader _flash.bin
    FB: ucmd mmc partconf ${emmc_dev} 0 1 0
    FB: Done

# Burn android to eMMC

    uuu_version 1.0.1
    SDP: boot -f _flash.bin
    # This command will be run when use SPL
    SDPU: delay 1000
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    # This command will be run when ROM support stream mode
    SDPS: boot -f _flash.bin

    FB: ucmd setenv fastboot_dev mmc
    FB: ucmd setenv mmcdev ${emmc_dev}
    FB: ucmd mmc dev ${emmc_dev}
    FB: flash gpt partition-table.img
    FB: flash boot_a boot-imx8qxp.img
    FB: flash system_a system.img
    FB: flash vendor_a vendor.img
    FB: flash vbmeta_a vbmeta-imx8qxp.img
    #FB: ucmd setenv fastboot_buffer ${loadaddr}
    #FB: download -f u-boot-imx8qxp.imx
    #FB: ucmd setexpr fastboot_blk ${fastboot_bytes}
    #FB: ucmd setexpr fastboot_blk ${fastboot_blk} + 0x1FF
    #FB: ucmd setexpr fastboot_blk ${fastboot_blk} / 0x200
    #FB: ucmd mmc partconf ${emmc_dev} 1 1 1
    #FB: ucmd echo ${fastboot_buffer}
    #FB: ucmd echo ${fastboot_blk}
    #FB: ucmd mmc write ${fastboot_buffer} 0x40  ${fastboot_blk}
    FB: flash bootloader u-boot-imx8qxp.imx
    FB: ucmd mmc partconf ${emmc_dev} 0 1 0
    FB: Done

# Burn yocto image to eMMC
    
    uuu_version 1.0.1
    SDP: boot -f _flash.bin
    # This command will be run when use SPL
    SDPU: delay 1000
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    # This command will be run when ROM support stream mode

    FB: ucmd setenv fastboot_dev mmc
    FB: ucmd setenv mmcdev ${emmc_dev}
    FB: ucmd mmc dev ${emmc_dev}
    FB: flash -raw2sparse all yocto.sdcard
    FB: flash bootloader _flash.bin
    FB: ucmd mmc partconf 0 0 1 0
    FB: Done

# Use kernel burn image to eMMC (similar with old mfgtools xml)

    uuu_version 1.0.1
    SDPS: boot -f flash_mfg.bin
    FBK: ucp mksdcard.sh t:/tmp
    FBK: ucmd chmod 777 /tmp/mksdcard.sh
    FBK: ucmd /tmp/mksdcard.sh /dev/mmcblk0
    FBK: ucmd dd if=/dev/zero of=/dev/mmcblk0 bs=1k seek=4096 count=1
    FBK: ucmd sync
    FBK: ucmd echo 0 > /sys/block/mmcblk0boot0/force_ro
    FBK: ucp flash.bin t:/tmp
    FBK: ucmd dd if=/tmp/flash.bin of=/dev/mmc0boot0 bs=1K seek=32
    FBK: ucmd echo 1 > /sys/block/mmcblk0boot0/force_ro
    FBK: ucmd while [ ! -e /dev/mmcblk0p1 ]; do sleep 1; done
    FBK: ucmd mkfs.vfat /dev/mmcblk0p1
    FBK: ucmd mkdir -p /mnt/mmcblk0p1
    FBK: ucmd mount -t vfat /dev/mmcblk0p1 /mnt/mmcblk0p1
    FBK: ucp Image t:/mnt/mmcblk0p1
    FBK: ucp fsl-imx8qxp-mek.dtb t:/mnt/mmcblk0p1
    FBK: ucmd umount /mnt/mmcblk0p1
    FBK: ucmd mkfs.ext3 -F -E nodiscard /dev/mmcblk0p2
    FBK: ucmd mkdir -p /mnt/ext3
    FBK: ucmd mount /dev/mmcblk0p2 /mnt/ext3
    FBK: acmd tar -jxv -C /mnt/ext3
    FBK: ucp rootfs.tar.bz2 t:-
    FBK: Sync
    FBK: ucmd umount /mnt/ext3
    FBK: DONE