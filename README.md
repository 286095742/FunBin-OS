# FunBin-OS

## Intro
This repository contains all the sources required to build FunBin-OS, the Open-Source firmware at the heart of the [FunBoy 40a retro-gaming device](https://github.com/286095742/FunBin-OS).

As the FunBoy 40a is based on a sophisticated [Allwinner A33 ARM Cortex-A7 CPU](http://www.allwinnertech.com/index.php?c=product&a=index&id=38), an Operating System is mandatory in order to access all the hardware resources without re-inventing the wheel.

FunBin-OS is based on Linux, and is built from scratch using the [buildroot](http://nightly.buildroot.org/) tool that simplifies and automates the process of building a complete Linux system for an embedded system like this.

Technically, FunkBin-OS is a [buildroot (v2) based external tree](https://buildroot.org/downloads/manual/manual.html#outside-br-custom) for building the bootloader, the Linux kernel and user utilities, as well as the optimized retro-game launcher and console emulators.

## Build host requirements
Even if the resulting disk image and firmware update files are relatively small (202 MB and 55MB, respectively), the size of the corresponding sources and the compilation by-products tend to be rather large, such that an available disk space of at least 12GB is required during the build.

And even if the resulting FunBin-OS boots in less than 5s, it still requires a considerable amount of time to compile: please account for 1 1/2 hour on a modern multi-core CPU with SSD drives and a decent Internet bandwidth.

As the target CPU is probably different from the one running on your build host machine, a process known as [_cross-compilation_](https://en.wikipedia.org/wiki/Cross_compiler) is required for the build, and as the target system will eventually be Linux, this is much better handled on hosts running a Linux-based operating system too.

As a matter of fact, the FunBin-OS is meant to be built on a native Ubuntu or Debian Linux host machine (Ubuntu 20.04 LTS in our case, but this should also work with other versions, too). And with only a few changes to the prerequisites, it can certainly be adapted to build on other common Linux distros.

However, if your development machine does not match this setup, there are still several available solutions:
 -  use a lightweight container system such as [Docker](https://www.docker.com/) and run an Ubuntu or Debian Linux container in it
 - use a VM (Virtual Machine) , such as provided by [VirtualBox](https://www.virtualbox.org/) and run an Ubuntu or Debian Linux in it
 - for Windows 10 users, use the [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10) (Windows System for Linux 2) subsystem and run an Ubuntu Linux distro in it

In order to install one of these virtualized environments on your machine, please refer to the corresponding documentation.

## Build on a Physical/Virtual Machine

### Prerequisites
While Buildroot itself will build most host packages it needs for the compilation, some standard Linux utilities are expected to be already installed on the host system. If not already present, you will need to install the following packages beforehand:
 - bash
 - bc
 - binutils
 - build-essential
 - bzip2
 - ca-certificates
 - cpio
 - cvs
 - expect
 - file
 - g++
 - gcc
 - git
 - gzip
 - liblscp-dev
 - libncurses5-dev
 - locales
 - make
 - mercurial
 - openssh-client
 - patch
 - perl
 - procps
 - python
 - python-dev
 - python3
 - python3-dev
 - python3-distutils
 - python3-setuptools
 - rsync
 - rsync
 - sed
 - subversion
 - sudo
 - tar
 - unzip
 - wget
 - which
 - xxd

On Ubuntu/Debian Linux, this is achieved by running the following command:
```bash
$ sudo apt install bash bc binutils build-essential bzip2 ca-certificates cpio cvs expect file g++ gcc git gzip liblscp-dev libncurses5-dev locales make mercurial openssh-client patch perl procps python python-dev python3 python3-dev python3-distutils python3-setuptools rsync rsync sed subversion sudo tar unzip wget which xxd
```

### How to get the sources
When using either physical or virtual Linux machines, you must clone the FunBin OS repository from Github (here we place it into a `FunBin-OS` directory):

```bash
$ git clone https://github.com/286095742/FunBin-OS.git FunBin-OS
```

Then enter into the created directory:

```bash
$ cd FunBin-OS
```

### Build the disk image & firmware update files
You may now build your FunBin with:

```bash
$ make sdk all
```
This may take a while (~1h30), so consider getting yourself a cup, a glass or a bottle of your favorite beverage ;-)

<ins>Note</ins>: you will need to have access to the network, since buildroot will download the package sources.

### Result of the build
After building, you should obtain the SD Card image `FunBin-sdcard-X.Y.Z.img` and the firmware update file `FunBin-rootfs-X.Y.fwu` in the `images` directory.

## How to write to the SD card
You can copy the bootable `images/sdcard.img` onto an SD card using "dd":

```bash
$ sudo dd if=images/FunBin-sdcard-X.Y.Z.img of=/dev/sdX
```
<ins>Warning</ins>: Please make sure that */dev/sdX* device corresponds to your SD Card, otherwise you may wipe out one of your hard drive partitions!

Alternatively, you can use the Balena-Etcher graphical tool to burn the image
to the SD card safely and on any platform:

https://www.balena.io/etcher/

Once the SD card is burnt, insert it into your FunBoy 40a slot, and
power it up. Your new system should come up now and start a console on
the UART0 serial port and display the retro game launcher on the graphical screen.

## How to update the FunBoy 40a firmware
It is possible to update a FunBoy 40a over USB:
 - Connect the FunBoy 40a console to your host machine using the USB cable
 - From the retro-game launcher, press the **ON/OFF** button to access the menu
 - Using the **Up/Down** keys, select the "**MOUNT USB**" screen ad press the "**A**" key twice to mount the FunBoy 40a on your machine as an USB mass storage drive
 - Drag and drop the images/FunBin-rootfs-X.Y.fwu file into it
 - When finished, eject the USB mass storage from your host machine
 - Back on the FunBoy 40a, press the "**A**" key twice to eject the USB mass storage drive
 - The FunBoy 40a will automatically detect the firmware update file and proceed with the update before returning to the retro game launcher screen once finished
