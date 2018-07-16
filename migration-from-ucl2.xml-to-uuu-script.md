Most case user can use uboot fastboot protocol to finish image program work. 
In case you have to load kernel to burn whole image. 

The below simple map ucl2.xml to uuu script. 

	<CMD state="BootStrap" type="boot" body="BootStrap" file ="firmware/flash.bin" ifdev="MX8QXPB0">Loading boot image</CMD>

SDPS: boot -f flash.bin

	<CMD state="BootStrap" type="boot" body="BootStrap" file ="firmware/flash.bin" ifdev="MX8MM">Loading U-boot</CMD>

SDP: boot -f flash.bin

SDPU: write -f flash.bin -offset 0x57c00

SDPU: jump

	<CMD state="BootStrap" type="load" file="firmware/Image" address="0x80280000"
		loadSection="OTH" setSection="OTH" HasFlashHeader="FALSE" ifdev="MX8QXP MX8QM">Loading Kernel.</CMD>

FB: ucmd setenv fastboot_buffer ${loadaddr}

FB: ucmd download -f Image

		
	<CMD state="BootStrap" type="load" file="firmware/initramfs.cpio.gz.uboot" address="0x83800000"
		loadSection="OTH" setSection="OTH" HasFlashHeader="FALSE" ifdev="MX8QM MX8QXP">Loading Initramfs.</CMD>

FB: ucmd setenv fastboot_buffer ${initrd_addr}

FB: ucmd download -f initramfs.cpio.gz.uboot

        <CMD state="BootStrap" type="load" file="firmware/fsl-imx8qxp.dtb" address="0x83000000"
		loadSection="OTH" setSection="OTH" HasFlashHeader="FALSE" ifdev="MX8QXP">Loading device tree.</CMD>

FB: ucmd setenv fastboot_buffer ${fdt_addr}

FB: ucmd download -f fsl-imx8qxp.dtb
	
	<CMD state="BootStrap" type="jump" > Jumping to OS image. </CMD>

FB: acmd booti ${loadaddr} ${initrd_addr} ${fdt_addr}

	<!-- create partition -->
	<CMD state="Updater" type="push" body="send" file="mksdcard.sh.tar">Sending partition shell</CMD>

FBK: ucp mksdcard.sh.tar t:/tmp

	<CMD state="Updater" type="push" body="$ tar xf $FILE "> Partitioning...</CMD>

FBK: ucmd tar xf /tmp/mksdcard.sh.tar -d /tmp

	<CMD state="Updater" type="push" body="$ sh mksdcard.sh /dev/mmcblk%mmc%"> Partitioning...</CMD>

FBK: ucmd mksdcard.sh /dev/mmcblk0mmc

	<!-- burn uboot -->
	<CMD state="Updater" type="push" body="send" file="files/imx-boot-imx8qxp-sd.bin" ifdev="MX8QXPB0">Sending u-boot.bin</CMD>
	
FBK: ucp imx-boot-imx8qxp-sd.bin t:/tmp

	<CMD state="Updater" type="push" body="$ dd if=/dev/zero of=/dev/mmcblk0 bs=1k seek=4096 conv=fsync count=8">clear u-boot arg</CMD>

FBK: ucmd dd if=/dev/zero of=/dev/mmcblk0 bs=1k seek=4096 conv=fsync count=8

	<CMD state="Updater" type="push" body="$ dd if=$FILE of=/dev/mmcblk0 bs=1k seek=33 conv=fsync" ifdev="MX8QM MX8QXP MX8MQ">write u-boot.bin to sd card</CMD>

FBK: ucmd dd if=/tmp/imx-boot-imx8qxp-sd.bin of=/dev/mmcblk0 bs=1k seek=33 conv=fsync

	<CMD state="Updater" type="push" body="$ while [ ! -e /dev/mmcblk0p1 ]; do sleep 1; echo waiting...; done ">Waiting for the partition ready</CMD>

FBK: ucmd while [ ! -e /dev/mmcblk%mmc%p1 ]; do sleep 1; echo waiting...; done

	<CMD state="Updater" type="push" body="$ mkfs.vfat /dev/mmcblk0p1">Formatting rootfs partition</CMD>

FBK: ucmd mkfs.vfat /dev/mmcblk0p1

	<CMD state="Updater" type="push" body="$ mkdir -p /mnt/mmcblk0p1"/>

FBK: ucmd mkdir -p /mnt/mmcblk0p1

	<CMD state="Updater" type="push" body="$ mount -t vfat /dev/mmcblk0p1 /mnt/mmcblk0p1"/>

FBK: ucmd vfat /dev/mmcblk0p1 /mnt/mmcblk0p1

	<!-- burn zImage -->
	<CMD state="Updater" type="push" body="send" file="files/Image">Sending kernel</CMD>

FBK: ucp Image t:/tmp

	<CMD state="Updater" type="push" body="$ cp $FILE /mnt/mmcblk0p1/Image">write kernel image to sd card</CMD>

FBK: ucmd /tmp/Image /mnt/mmcblk0p1/Image

	<!-- burn dtb -->
	<CMD state="Updater" type="push" body="send" file="files/fsl-imx8qxp.dtb" ifdev="MX8QXP MX8QXPB0">Sending Device Tree file</CMD>

FBK: ucp fsl-imx8qxp.dtb /tmp

	<CMD state="Updater" type="push" body="$ cp $FILE /mnt/mmcblk0p1/fsl-imx8qm.dtb" ifdev="MX8QM">write device tree to sd card</CMD>

FBK: ucmd cp /tmp/fsl-imx8qxp.dtb /mnt/mmcblk0p1/

	<CMD state="Updater" type="push" body="$ umount /mnt/mmcblk0p1">Unmounting vfat partition</CMD>

FBK: ucmd umount /mnt/mmcblk0p1

	<!-- burn rootfs -->
	<CMD state="Updater" type="push" body="$ mkfs.ext3 -F -j /dev/mmcblk0p2">Formatting rootfs partition</CMD>

FBK: ucmd mkfs.ext3 -F -j /dev/mmcblk0p2

	<CMD state="Updater" type="push" body="$ mkdir -p /mnt/mmcblk0p2"/>

FBK: ucmd mkdir -p /mnt/mmcblk0p2

	<CMD state="Updater" type="push" body="$ mount -t ext3 /dev/mmcblk0p2 /mnt/mmcblk0p2"/>

FBK: ucmd mount -t ext3 /dev/mmcblk0p2 /mnt/mmcblk0p2

	<CMD state="Updater" type="push" body="pipe tar -jxv -C /mnt/mmcblk0p2" file="files/rootfs.tar.bz2">Sending and writting rootfs</CMD>

FBK: acmd tar -jxv -C /mnt/mmcblk0p2

FBK: ucp rootfs.tar.bz2 t:-

	<CMD state="Updater" type="push" body="frf">Finishing rootfs write</CMD>

FBK: sync

	<CMD state="Updater" type="push" body="$ umount /mnt/mmcblk0p2">Unmounting rootfs partition</CMD>

FBK: ucmd umount /mnt/mmcblk0p2

	<CMD state="Updater" type="push" body="$ echo Update Complete!">Done</CMD>

FBK: done