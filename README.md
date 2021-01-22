<img src="https://assets.ubuntu.com/v1/8dd99b80-ubuntu-logo14.png" width="200" align="right">
<br>
<br>
<br>



SD Card image with LEC-iMX8M 4G Memory for the quick evaluation:
* [download link](https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/Ubuntu18_A3_4G/install.sh)

How to flash image to sd card:

* please plug your SD card to your host machine and enter the following commands:
```
$ sudo chmod +x install.sh
$ sudo ./install.sh -sd /dev/sdX
```
**Note:** 
* "X" is your sd card device name
* Login information: user name: imx8m / password: imx8m 

* The prepared SD card can now be ejected and used for booting i.MX8M.
* Please note that on first boot after card preparation, board takes around 10 minutes to setup Ubuntu rootfs in the target. You will see Ubuntu terminal alone available on first boot and this is normal.
* Please change the boot switch setting from SD to eMMC,  if eMMC mode is selected during SD card preparation. Do not reset or power cycle the board during this initial setup time. 
* Once setup is complete, board will automatically reboot and launch Ubuntu desktop.
 


<br>
How to build Ubuntu 18.04 LTS Image

* see [documentation](https://github.com/ADLINK/nxp-imx8m-ubuntu/wiki) for more details.

  





 


