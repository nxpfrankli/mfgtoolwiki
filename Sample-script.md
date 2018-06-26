# Burn android to eMMC

    uuu_version 1.0.1
    SDPS: boot -f flash.bin
    FB: flash gpt partition-table.img
    FB: flash boot_a boot-imx8qxp.img
    FB: flash system_a system.img
    FB: flash vendor_a vendor.img
    FB: flash vbmeta_a vbmeta-imx8qxp.img
    FB: ucmd setenv fastboot_buffer ${loadaddr}
    FB: download -f u-boot-imx8qxp.imx
    FB: ucmd setexpr fastboot_blk ${fastboot_bytes}
    FB: ucmd setexpr fastboot_blk ${fastboot_blk} + 0x1FF
    FB: ucmd setexpr fastboot_blk ${fastboot_blk} / 0x200
    FB: ucmd mmc partconf 0 1 1 1
    FB: ucmd echo ${fastboot_buffer}
    FB: ucmd echo ${fastboot_blk}
    FB: ucmd mmc write ${fastboot_buffer} 0x40  ${fastboot_blk}
    FB: ucmd mmc partconf 0 1 1 0
    FB: Done

# Burn yocto image to eMMC
    
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