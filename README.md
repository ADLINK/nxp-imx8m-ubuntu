<img src="https://assets.ubuntu.com/v1/8dd99b80-ubuntu-logo14.png" width="200" align="right">
<br>
<br>
<br>



SD Card image with LEC-iMX8M 4G Memory on LEC Base 2.0 Carrier board for the quick evaluation:
* [download link](https://hq0epm0west0us0storage.blob.core.windows.net/$web/public/SMARC/LEC-iMX8M/Ubuntu/LEC-iMX8M-4G_Ubuntu18.04_sdcard_20210122.zip)

**Note:**
   The kernel used is not Ubuntu native. Ubuntu rootfs image which is bootstrapped by the iMX8M u-boot & Kernel.

<br>
How to flash image to sd card or eMMC:

* see [documentation](https://github.com/ADLINK/nxp-imx8m-ubuntu/wiki#step-4-flashing-installer-to-sd-card) for more details.



**Note:** 
 
* "X" is your sd card device name
* Login information: user name: imx8m / password: adlink123 

* The prepared SD card can now be ejected and used for booting i.MX8M.
* Please note that on first boot after card preparation, board takes around 10 minutes to setup Ubuntu rootfs in the target. You will see Ubuntu terminal alone available on first boot and this is normal.
* Please change the boot switch setting from SD to eMMC,  if eMMC mode is selected during SD card preparation. Do not reset or power cycle the board during this initial setup time. 
* Once setup is complete, board will automatically reboot and launch Ubuntu desktop.
 


<br>
How to build Ubuntu 18.04 LTS Image

* see [documentation](https://github.com/ADLINK/nxp-imx8m-ubuntu/wiki) for more details.

  





 


