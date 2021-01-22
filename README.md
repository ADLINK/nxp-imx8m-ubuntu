### Download the A3 LEC-iMX8M 4G with Ubuntu 18.04 LTS image: [click here](https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/Ubuntu18_A3_4G/install.sh)
   
### How to flash image to SD Card

```
$ sudo chmod +x install.sh
$ sudo ./install.sh -sd /dev/sdX
```

**Note:** "X" is your sd card device name


<br>


#### How to Build Ubuntu
---

This procedure describes how to create Ubuntu image with SD Card on **ADLINK LEC-iMX8M 4G memory Module **. The version of Ubuntu used is 18 (bionic).

## Recommended Hardware

To setup the build environment for Ubuntu image creation, a Linux host with the following configuration is recommended. A host machine with adequate processing power and disk space is ideal as the packages can run several GBs in size and the build can take a lot of time.

- Intel Core-i7 processor (>= 4 cores)
- 8 GB RAM
- 1 TB disk space
- High speed network connectivity
- OS: Ubuntu 18.04 LTS/Ubuntu 20.04 LTS

Update apt repositories list on your host machine and install required packages.

```
$ sudo apt update
$ sudo apt install build-essential
```

## Getting Started

The following are the steps required to generate Ubuntu image for i.MX8M

- Step 1: Create a Ubuntu root file system
- Step 2: Prepare u-boot, Linux kernel and DTS files
- Step 3: Prepare Ubuntu installer for flashing to SD card
- Step 4: Flash the installer to SD card and boot into Ubuntu 

These steps are detailed below.

## Step 1: Preparing Ubuntu Root File System

1. Create a new working directory and change directory into it.

   ```shell
   $ mkdir $HOME/imx8m
   $ cd $HOME/imx8m
   ```

2. Create a rootfs directory.

   ```shell
   $ mkdir rootfs
   ```

3. Install debootsrap and its dependencies.

   ```shell
   $ sudo apt install debootstrap binfmt-support qemu-user-static
   ```

   ```shell
   $ sudo debootstrap --arch=arm64 --foreign bionic rootfs
   ```

### Configure the Rootfs

1. Add host name to `/etc/hostname` 

   ​	Here is an example.

   ```shell
   $ sudo bash -c "echo 'adlink' > rootfs/etc/hostname"
   ```

2. Add host entry in `/etc/hosts`

   ```shell
   $ sudo bash -c "printf '127.0.0.1\tlocalhost\n127.0.1.1\tadlink\n\n' > rootfs/etc/hosts"
   ```

   ```shell
   $ sudo bash -c "echo 'root::0:0:root:/root:/bin/bash' > rootfs/etc/passwd"
   ```

3. Prepare QEMU

   ```shell
   $ sudo wget https://github.com/multiarch/qemu-user-static/releases/download/v4.2.0-6/qemu-aarch64-static -O rootfs/usr/bin/qemu-aarch64-static
   $ sudo chmod +x rootfs/usr/bin/qemu-aarch64-static
   ```

4. Get your network ready

   ```shell
   $ sudo cp /etc/resolv.conf rootfs/etc/
   ```

5. Mount the proc sys dev file systems

   ```shell
   $ for f in proc sys dev dev/pts; do sudo mount --bind /$f rootfs/$f; done
   ```

6. Change root

   ```shell
   $ sudo chroot rootfs /bin/bash
   ```

7. Add user name

   ```shell
   $ /debootstrap/debootstrap --second-stage
   $ adduser imx8m (provide new password and name for the user in the prompt)
   $ usermod -a -G sudo imx8m
   ```


8. Add required apt repositories to sources.list

   ```shell
   $ printf 'deb http://ports.ubuntu.com/ubuntu-ports bionic main restricted\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic main restricted\n\ndeb http://ports.ubuntu.com/ubuntu-ports bionic-updates main restricted\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates main restricted\n\ndeb http://ports.ubuntu.com/ubuntu-ports bionic universe\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic universe\n\ndeb http://ports.ubuntu.com/ubuntu-ports bionic-updates universe\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates universe\n\ndeb http://ports.ubuntu.com/ubuntu-ports bionic multiverse\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic multiverse\n\ndeb http://ports.ubuntu.com/ubuntu-ports bionic-updates multiverse\n# deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates multiverse\n' > /etc/apt/sources.list
   ```
  
9. Configure locales

    You will be prompted through GUI menu to make certain selections such as those for date/time/region. Please make the appropriate selections and proceed.

   ```shell
   $ apt update
   $ dpkg-reconfigure locales
   ```
   
10. Install ubuntu-desktop and necessary packages

    ```shell
    $ apt install ubuntu-desktop rng-tools
    ```

11. Clean up and exit from chroot

     ```shell
    $ rm -rf /var/cache/apt/archives/*.deb
    $ sync
    $ exit
     ```

12. Unmount the mounted files

    ```shell
    $ for f in proc sys dev/pts dev; do sudo umount rootfs/$f; done
    ```

13. Remove emulator and resolv.conf file

    ```shell
    $ sudo rm rootfs/usr/bin/qemu-aarch64-static rootfs/etc/resolv.conf
    ```

## Step 2: Preparing U-boot, Linux Kernel and DTS Files

Download u-boot, kernel and DTS files

   ```shell
$ cd $HOME/imx8m
$ wget https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/UbuntuNecessaryFiles/IMX8M_A3_4G/flash.bin
$ wget https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/UbuntuNecessaryFiles/IMX8M_A3_4G/Image
$ wget https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/UbuntuNecessaryFiles/IMX8M_A3_4G/adlink-lec-imx8m.dtb
   ```

## Step 3: **Preparing Ubuntu Installer**

Ubuntu installer is a tool used to prepare SD card for eMMC or SD booting based on user's choice.
This section will detail how to generate Ubuntu installer for rootfs and boot files created in earlier steps.

1. Download Ubuntu installer base and extract

   ```shell
   $ cd $HOME/imx8m
   $ wget https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/UbuntuNecessaryFiles/ubuntu-installer-base.tar.xz
   $ tar -axf ubuntu-installer-base.tar.xz
   ```

2. Copy u-boot (flash.bin) , kernel (Image) and DTS (adlink-lec-imx8m.dtb) files downloaded earlier to installer boot folder

   ```shell
   $ sudo cp flash.bin Image adlink-lec-imx8m.dtb $HOME/imx8m/ubuntu-installer-base/boot
   ```
   
3. Download boot script and copy to root file system

   ```shell
   $ cd $HOME/imx8m/ubuntu-installer-base
   $ wget https://hq0epm0west0us0storage.blob.core.windows.net/development/LEC-iMX8M/Images/Ubuntu%20/UbuntuNecessaryFiles/boot.scr
   $ sudo cp boot.scr $HOME/imx8m/rootfs/boot
   ```

4. Pack and copy rootfs to installer location

   ```shell
   $ sudo tar -acf boot/rootfs.tar.xz -C $HOME/imx8m/rootfs .
   $ sudo mv boot/rootfs.tar.xz boot/ubuntu.img
   ```

5. Pack boot folder to create ubuntu.tar.xz

   ```shell
   $ sudo tar -acf ubuntu.tar.xz boot
   ```

6. Finally, create Ubuntu installer

   ```shell
   $ ./build.sh ubuntu.tar.xz
   ```

Now installer is ready and can be used to prepare SD card.

## Step 4: Flashing Installer to SD card

The installer generated in previous section supports flashing to SD and eMMC.
To prepare SD card for SD based booting, execute the command below.

   ```shell
   $ sudo ./install.sh -sd /dev/sdX
   ```

To prepare SD card for eMMC based booting, execute the command below.

   ```shell
   $ sudo ./install.sh -emmc /dev/sdX
   ```

The prepared SD card can now be ejected and used for booting i.MX8M.

Please note that on first boot after card preparation, board takes around 10 minutes to setup Ubuntu rootfs in the target.

You will see Ubuntu terminal alone available on first boot and this is normal.
Please change the boot switch setting from SD to eMMC,  if eMMC mode is selected during SD card preparation. Do not reset or power cycle the board during this initial setup time. 

Once setup is complete, board will automatically reboot and launch Ubuntu desktop.
User can now login to i.MX8M using credentials set in section "7. Add user name"
