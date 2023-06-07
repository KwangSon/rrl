# QEMU

## know your hardware
apt install qemu-system-misc

### QEMU virt

## ROM, u-boot, sbi
can skip

## kernel(gcc)
```
make O=./build_qemu defconfig
make O=./build_qemu -j$(nproc)
```

### kernel(rust enable)
```
git remove add rust https://github.com/Rust-for-Linux/linux.git
```

### initramfs
```
https://gist.github.com/chrisdone/02e165a0004be33734ac2334f215380e
```

### test
```
qemu-system-riscv64 -nographic -machine virt \
-kernel linux/build_qemu/arch/riscv/boot/Image \
-initrd initramfs.cpio.gz \
-append "console=ttyS0"
```

## target patch
https://lore.kernel.org/linux-riscv/20230405-itinerary-handgrip-a5ffba368148@spud/T/#u