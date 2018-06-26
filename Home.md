Welcome to the uuu. Old name mfgtools.

[Usage Example](Example)

[Sample-script](Sample-script)

# Usage:


    uuu [-d -m -v] u-boot.imx\flash.bin
             Download u-boot.imx\flash.bin to board by usb
             -d         Deamon mode, wait for forever.
                        Start download once detect known device attached
             -v         Print build in protocal config informaiton   -m USBPATH Only monitor these pathes. -m 1:2 -m 2:3
  
    uuu [-d -m -v] cmdlist
            Run all commands in file cmdlist

    uuu [-d -m -v] SDPS: boot flash.bin
            Run command SPDS: boot flash.bin
    uuu -s
            Enter shell mode. uuu.inputlog record all input's command
            you can use "uuu uuu.inputlog" next time to run all commands

     ---------------------------------------------------------------------
     Command Format PROTOCOL COMMAND ARG
     PROTOCOL
              CFG:  Config protocol of specific usb device vid/pid
                    SDPS|SDP|FB\Fastboot|FBK -chip <chip name> -pid <pid> -vid <vid> [-bcdversion <ver>]
              SDPS: Stream download after MX8QXPB0
                          boot  -f  <filename>
                          done  #last command for whole flow
              SDP:  iMX6/iMX7 HID download protocol.
                          dcd   -f  <filename>
                          write -f  <filename> [-addr 0x000000] [-ivt 0]
                          jump  -f  <filename> [-ivt 0]
                          boot  -f  <filename> [-nojump]
                          done  #last command for whole flow
              FB:\Fastboot: android fastboot protocol
                          getvar
                          ucmd <any uboot command>
                          acmd <any never returned uboot command, like booti, reboot>
                          flash [-raw2sparse] <partition> <filename>
                          download -f <filename>
              FBK: community with kernel with fastboot protocol. DO NOT compatible with fastboot tools.
                          ucmd <any kernel command> and wait for command finish
                          acmd <any kernel command> don't wait for command finish
                          sync                      wait for acmd processs finish.
                          ucp <soure> <destinate>   copy file from/to target
                                                    T:<filename> means target board file.
                                                    T:- means copy data to target's stdio pipe.
                                                    copy image T:/root/image ;download image to path /root/image
                                                    copy T:/root/image image ;upload /root/image to file image.

                          Example for transfer big file
                                  acmd tar -            ; run tar background and get data from stdio
                                  ucp rootfs.tar.gz T:- ; send to target stdio pipe
                                  sync                  ; wait for tar process exit.
     For example:
              SDPS: boot -f <filename>
              SDP:  boot -f <filename>
              CFG: SDP: -chip imx6ull -pid 0x1234 -vid 0x5678
     SDP: boot -f u-boot-imx7dsabresd_sd.imx -nojump
     SDP: write -f zImage -addr 0x80800000
     SDP: write -f zImage-imx7d-sdb.dtb -addr 0x83000000
     SDP: write -f fsl-image-mfgtool-initramfs-imx_mfgtools.cpio.gz.u-boot -addr 0x83800000
     SDP: jump -f u-boot-dtb.imx -ivt
