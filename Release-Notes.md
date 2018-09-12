# 1.1.41
## New features
* Support SDP protocol (i.MX6x, i.MX7, i.MX8M, i.MX8MM)
* Support SDPS protocol (i.MX8QXP B0, i.MX8QM B0)
* Support SDPU protocol (uboot\SPL implemented SDP protocol)
* Support FB protocol (fastboot with uboot)
* Support FBK protocol (fastboot in kernel, daemon implement at imx-uuc)
* Support multi-device download
* Support built-in script

## Bug fixes
* Fix sometime open usb device failure at windows platform.  
* Fix protocol case sensitive problem in script
* Fix open zip file failure

## Known issue
* some old i.MX8MM board HID can't work in linux system.  Need apply ROM patch to fix. Please contact FAE. 