# Setup


## Deps
First make sure you have these dependencies installed on your system (taken from [here](https://ocw.cs.pub.ro/courses/si/laboratoare/01)).

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y bison flex gettext texinfo libncurses5-dev libncursesw5-dev gperf automake libtool pkg-config build-essential gperf genromfs libgmp-dev libmpc-dev libmpfr-dev libisl-dev binutils-dev libelf-dev libexpat-dev gcc-multilib g++-multilib picocom u-boot-tools util-linux chrony libusb-dev libusb-1.0.0-dev kconfig-frontends python3-pip
pip3 install esptool pyserial
```

Then run these commands to install the toolchain for working with ESP32S3 (assuming you are using `bash`).

```bash
sudo mkdir -p /opt/xtensa
curl -L "https://github.com/espressif/crosstool-NG/releases/download/esp-12.2.0_20230208/xtensa-esp32s3-elf-12.2.0_20230208-x86_64-linux-gnu.tar.xz" | sudo tar -xJ -C /opt/xtensa
echo "export PATH=\$PATH:/opt/xtensa/xtensa-esp32s3-elf/bin" >> ~/.bashrc
source ~/.bashrc
```

## Cloning the repository

```bash
git clone --recurse-submodules https://github.com/radupascale/hectorwatch-nuttx
```

# Using the board

## Building

The default config for working with the smartwatch is called `usbnsh`. You can use
it as a template for customizing the device. For example, if you configured stuff
with `make menuconfig`, `make savedefconfig` can be used to generate a `defconfig` file
that can be added to a reparate directory in `nuttx/boards/custom-boards/esp32s3-hectorwatch/configs`.

As this device is not an "in-tree" board, the command for selecting the configuration is a
little different, but building is exactly the same

```bash
./tools/configure.sh -l ./boards/custom-boards/esp32s3-hectorwatch/configs/usbnsh
make -j$(nproc)
```

## Flashing

Flashing requires a bit of manual work. Unfortunately, the default config cannot be used to
automatically set the device into download mode (it's complicated...).
So until, we fix this issue you'll have to do the following steps everytime you want to 
**flash** the device:
- Hold **BOOT** -> Press **RESET** -> Release **RESET**. This will boot the device in **download mode**.
- Run:

```bash
make flash ESPTOOL_PORT=/dev/ttyACM0 ESPTOOL_BAUD=115200 ESPTOOL_BINDIR=../esp32s3-bins
```

- Press **RESET** again to reboot with the new firmware.


Afterwards, assuming you followed the above steps and everything went fine, you can access
`nsh` using `picocom`.

```bash
picocom /dev/ttyACM0 -b 115200
```

# Resources
- [Lab01 Embedded Systems ACS](https://ocw.cs.pub.ro/courses/si/laboratoare/01)
- [Nuttx custom boards how-to](https://nuttx.apache.org/docs/latest/guides/customboards.html)
- [ESP32S3 does not reset after upload](https://github.com/espressif/arduino-esp32/issues/6762)