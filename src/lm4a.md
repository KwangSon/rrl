
## boot process
vendor U-boot - upstream U-boot - upstream kernel with busy box.
When running in M-mode U-Boot, it will load the payload image (e.g. fw_payload) which contains the firmware and the S-mode Operating System; in this case, you can use mkimage to package the payload image into an uImage format, and boot it using the bootm command.

make PLATFORM=generic FW_PAYLOAD_PATH=<linux_build_directory>/arch/riscv/boot/Image

1. U-boot with [patch](https://lore.kernel.org/u-boot/20230526124107.894-1-dlan@gentoo.org/T/#t)
```
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- th1520_lpi4a_defconfig O=./output
check legacy uboot image support
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- -j$(nproc) O=./output


$ mkimage -A <arch> -O linux -T kernel -C none -a <load-address> -e <entry-point> -n "Linux kernel" -d arch/arm/boot/zImage uImage
./output/tools/mkimage -A riscv -T kernel -C none -n "Linux With SBI" -d payload.bin linux-with-payload.img
```
2. kernel with [patch](https://lore.kernel.org/linux-riscv/20230518184541.2627-1-jszhang@kernel.org/T/#m4e07e9f1c1ba156318d3a4646cd0ea0d4f11059f)
```
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- defconfig  O=./output
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- uImage O=./output
```
3. busybox
```
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- defconfig
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- menuconfig
Settings -> Build option to No shared.
$ make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- install
https://hackernoon.com/how-to-set-up-the-environment-for-riscv-64-linux-kernel-development-in-ubuntu-2004-si5p35kv
$ ./output/tools/mkimage -A riscv -T ramdisk -d busybox-1.32.1/initramfs.cpio.gz initrd.img
```
4. put it all to sd card
```
ext4load mmc 1:1 0x1c00000 u-boot-dtb.bin; go 0x1c00000

setenv bootargs earlycon loglevel=7; setenv kernel_comp_addr_r 0x80200000; setenv kernel_comp_size 0x800000

kernel_addr_r=0x20000000; fdt_addr_r=0x1d000000; ramdisk_addr_r=0x30000000

load mmc 0:1 $ramdisk_addr_r initrd.img; ext4load mmc 0:1 ${kernel_addr_r} image-withspl.img; ext4load mmc 0:1 ${fdt_addr_r} th1520-lichee-pi-4a.dtb; bootm ${kernel_addr_r} ${ramdisk_addr_r} ${fdt_addr_r}
```

## links
Xuantie-910 CPU - TH1520 SoC - Lichee Module 4A - Lichee Pi 4A  
[news info](https://www.cnx-software.com/2023/05/06/lichee-pi-4a-risc-v-sbc-raspberry-pi-4-th1520-processor/)  
[official image](https://gitee.com/thead-yocto/light_deploy_images) code name light-lpi4a  
[pre-built image](https://wiki.sipeed.com/hardware/zh/lichee/th1520/lpi4a/3_images.html)  
[sipeed wiki](https://wiki.sipeed.com/hardware/en/lichee/th1520/lp4a.html)  
[t-head git](https://gitee.com/organizations/thead-yocto/projects)  
[t-head u-boot](https://gitee.com/thead-yocto/u-boot), this has light_lpi4a in configs dir  
u-boot patch detail in thead-yocto/xuantie-yocto/meta-light/recipes-bsp/u-boot/files/0001-riscv64-910.patch  
[mainline patch](https://lore.kernel.org/linux-riscv/20230507182304.2934-1-jszhang@kernel.org/T/#t)  

boot, build, fastboot, uart0 check
*jtag*
emmc start address?

## steps
1. datasheet
    1. Xuantie-910 [overview](https://ftp.libre-soc.org/466100a052.pdf)
    2. official [pdf](https://www.t-head.cn/product/c910?lang=en)
2. boot process
    1. tools [bootlin gcc](https://toolchains.bootlin.com/)
    2. [opensbi](https://github.com/riscv-software-src/opensbi)
    u-boot will use it as spl, we may consider this if sipeed use own bootloader
    ```
    # If there is no FW_PAYLOAD_PATH, a small test program is run.
    $ CROSS_COMPILE=riscv64-linux-gnu- PLATFORM=generic FW_PAYLOAD_PATH=../u-boot/u-boot.bin make -j$(nproc) O=output
    ```
    3. u-boot
    ```
    $ make light_lpi4a_defconfig
    $ ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make -j$(nproc)
    # u-boot-with-spl.bin
    ```
    4. linux
    ```
    $ ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make defconfig
    ```
    5. busybox initramfs
    [article](https://risc-v-machines.readthedocs.io/en/latest/linux/simple/)
    `make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- defconfig`
3. sd card boot
