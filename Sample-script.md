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
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    # This command will be run when ROM support stream mode
    SDPS: boot -f _flash.bin

    FB: ucmd setenv fastboot_buffer ${loadaddr}
    FB: download -f _Image
    FB: ucmd setenv fastboot_buffer ${fdt_addr}
    FB: download -f _board.dtb
    FB: ucmd setenv fastboot_buffer ${initrd_addr}
    FB: download -f _initramfs.cpio.gz.uboot
    FB: ucmd setenv mfgtool_args ${mfgtool_args} mfg_mmcdev=${emmc_dev}
    FB: ucmd run mfgtool_args
    FB: acmd booti ${loadaddr} ${initrd_addr} ${fdt_addr}

    # get mmc dev number from kernel command line
    FBK: ucmd cmdline=`cat /proc/cmdline`;cmdline=${cmdline#*mfg_mmcdev=};cmds=($cmdline);echo ${cmds[0]}>/tmp/mmcdev
    # Wait for mmc
    FBK: ucmd mmc=`cat /tmp/mmcdev`; while [ ! -e /dev/mmcblk${mmc} ]; do sleep 1; echo "wait for /dev/mmcblk${mmc} appear"; done;
    # create partition
    FBK: ucmd mmc=`cat /tmp/mmcdev`; PARTSTR=$'10M,500M,0c\n600M,,83\n'; echo "$PARTSTR" | sfdisk --force /dev/mmcblk${mmc}

    FBK: ucmd mmc=`cat /tmp/mmcdev`; dd if=/dev/zero of=/dev/mmcblk${mmc} bs=1k seek=4096 count=1
    FBK: ucmd sync
    FBK: ucmd mmc=`cat /tmp/mmcdev`; echo 0 > /sys/block/mmcblk${mmc}boot0/force_ro
    FBK: ucp  _flash.bin t:/tmp
    FBK: ucmd mmc=`cat /tmp/mmcdev`; dd if=/tmp/_flash.bin of=/dev/mmc${mmc}boot0 bs=1K seek=32
    FBK: ucmd mmc=`cat /tmp/mmcdev`; echo 1 > /sys/block/mmcblk${mmc}boot0/force_ro
    FBK: ucmd mmc=`cat /tmp/mmcdev`; while [ ! -e /dev/mmcblk${mmc}p1 ]; do sleep 1; done
    FBK: ucmd mmc=`cat /tmp/mmcdev`; mkfs.vfat /dev/mmcblk${mmc}p1
    FBK: ucmd mmc=`cat /tmp/mmcdev`; mkdir -p /mnt/fat
    FBK: ucmd mmc=`cat /tmp/mmcdev`; mount -t vfat /dev/mmcblk${mmc}p1 /mnt/fat
    FBK: ucp  _Image t:/mnt/fat
    FBK: ucp  _board.dtb t:/mnt/fat
    FBK: ucmd umount /mnt/fat
    FBK: ucmd mmc=`cat /tmp/mmcdev`; mkfs.ext3 -F -E nodiscard /dev/mmcblk${mmc}p2
    FBK: ucmd mkdir -p /mnt/ext3
    FBK: ucmd mmc=`cat /tmp/mmcdev`; mount /dev/mmcblk${mmc}p2 /mnt/ext3
    FBK: acmd tar -jxv -C /mnt/ext3
    FBK: ucp  _rootfs.tar.bz2 t:-
    FBK: Sync
    FBK: ucmd umount /mnt/ext3
    FBK: DONE

# Download kernel and mount nfs by usb ncm

    uuu_version 1.0.1

    # Please replace below item with actual name
    # @_flash.bin                   | boot loader
    # @_Image                       | linux kernel image, zImage for arm32, Image for arm64
    # @_board.dtb                   | board dtb file
    # @_initramfs.cpio.gz.uboot     | initramfs
    # @_nfspath                     | rootfs path without ip address, ip address will auto detect

    SDP: boot -f _flash.bin
    # This command will be run when use SPL
    SDPU: write -f _flash.bin -offset 0x57c00
    SDPU: jump
    # This command will be run when ROM support stream mode
    SDPS: boot -f _flash.bin

    FB: ucmd setenv fastboot_buffer ${loadaddr}
    FB: download -f _Image
    FB: ucmd setenv fastboot_buffer ${fdt_addr}
    FB: download -f _board.dtb
    FB: ucmd setenv fastboot_buffer ${initrd_addr}
    FB: download -f _initramfs.cpio.gz.uboot
    FB: ucmd setenv nfsroot _nfspath
    FB: ucmd setenv bootargs console=${console},${baudrate} nfsroot=${nfsroot} init=/linuxrc root=/dev/nfs
    #uncomment below line to stop at initramfs
    #FB: ucmd setenv bootargs console=${console},${baudrate} nfsroot=${nfsroot}
    FB: acmd ${kboot} ${loadaddr} ${initrd_addr} ${fdt_addr}
    FB: done