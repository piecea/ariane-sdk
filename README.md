# Ariane SDK

This repository houses a set of RISCV tools for the [ariane core](https://github.com/pulp-platform/ariane). It **does not contain openOCD**.

Included tools:
* [OpenSBI](https://github.com/riscv/opensbi), the firmware

## Quickstart

Requirements Ubuntu:
```console
$ sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev libusb-1.0-0-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev device-tree-compiler pkg-config libexpat-dev
```

Requirements Fedora:
```console
$ sudo dnf install autoconf automake @development-tools curl dtc libmpc-devel mpfr-devel gmp-devel libusb-devel gawk gcc-c++ bison flex texinfo gperf libtool patchutils bc zlib-devel expat-devel
```

Then install the tools with

```console
$ git submodule update --init --recursive
```

## Linux
You can also build a compatible linux image with opensbi that boots linux on the ariane fpga mapping:
```bash
$ make Image # make only the linux Image
# outputs an Image file in the top directory
$ make fw_payload.bin # generate the entire bootable image
# outputs fw_payload.bin
```

### Booting from an SD card
The bootloader of ariane requires a GPT partition table so you first have to create one with gdisk.

```bash
$ sudo fdisk -l # search for the corresponding disk label (e.g. /dev/sdb)
$ sudo sgdisk --clear --new=1:2048:67583 --new=2 --typecode=1:3000 --typecode=2:8300 /dev/sdb # create a new gpt partition table and two partitions: 1st partition: 32mb (ONIE boot), second partition: rest (Linux root)
```

Now you have to compile the linux kernel:
```bash
$ make fw_payload.bin # generate the entire bootable image
```

Then the opensbi+linux kernel image can get copied to the sd card with `dd`. __Careful:__  use the same disk label that you found before with `fdisk -l` but with a 1 in the end, e.g. `/dev/sdb` -> `/dev/sdb1`.
```bash
$ sudo dd if=fw_payload.bin of=/dev/sdb1 status=progress oflag=sync bs=1M
```

## OpenOCD - Optional
If you really need and want to debug on an FPGA/ASIC target the installation instructions are [here](https://github.com/riscv/riscv-openocd). 
