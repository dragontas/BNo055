# Hardare:
#1 Raspberry Pi zero with wifi; 12€ +5Eur DHL
#2 Adafruit bno055, max 30€, google it
#3 ublox neo7 receiver or compatible (with uart connector), max 10€, ebay or amazon
#4 AZDelivery 3 x 0,96 Zoll OLED Display, 10€ @amazon

# Howto

make sure to activate i2c a priori in the Rpi config
g++ Example_Data_Raw.cpp RPi_BNO055.cpp -lpigpio

# Reading GPS-sensor
sudo cat /dev/ttyS0 9600

# RT-Linux RPi
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install git rsync cmake libc6-i386 lib32z1 lib32stdc++6
sudo apt-get install libncurses-dev
sudo apt-get install libssl-de
sudo apt-get install git ncurses-dev make gcc-arm-linux-gnueabi

export KERNEL=kernel

git clone https://github.com/raspberrypi/linux.git
mv ./patch-5.10.16-rt30.patch.xz ./rtKernel/
mv linux/ ./rtKernel/
mv tools/ ./rtKernel/
export ARCH=arm
export CROSS_COMPILE=~/rtKernel/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-
export INSTALL_DTBS_PATH=~/rtKernel/linux/
export KERNEL=kernel
cd linux
make bcmrpi_defconfig

make menuconfig   --> here select the RT path ... and maybe some optimization

make -j4 zImage 

wget http://cdn.kernel.org/pub/linux/kernel/projects/rt/5.10/older/patch-5.10.16-rt30.patch.xz
git checkout -b rpi-5.10.16-rt 3d48a0dbdcf562eb5bcddc9caf092627544b5d4f
xzcat ../patch-5.10.16-rt30.patch.xz | patch -p
git add -A
git commit -m "apply PREEMPT_RT patches patch-5.10.16-rt30.patch.xz"
git clone https://github.com/raspberrypi/tools.git
cd /home/dragontas/rtKernel/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/arm-linux-gnueabihf/bin/
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs
sudo env PATH=$PATH make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=mnt/ext4 modules_instal
sudo cp mnt/fat32/$KERNEL.img mnt/fat32/$KERNEL-backup.img
sudo cp arch/arm/boot/zImage mnt/fat32/$KERNEL.img
sudo cp arch/arm/boot/dts/*.dtb mnt/fat32/
sudo cp arch/arm/boot/dts/overlays/*.dtb* mnt/fat32/overlays/
unmount and start the raspberry



# RPi_BNO055
BNO055 IMU Library for Raspberry Pi based on the Adafruit libraries for Arduino.
