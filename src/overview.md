# Overview
Learn RISCV + RUST + linux detail

## why
It is too vast and exhausting to study Linux as a simple hobby. Therefore, I study with a small purpose.

## what
Learn risc-v rust linux through actual target(LM4A) + QEMU.

## Steps
1. know your hardware
2. ROM, u-boot, sbi
3. kernel, rust, riscv

## software env
gcc - gcc-12  
llvm - clang-15 llvm-15  
u-boot - https://github.com/u-boot/u-boot, tar - https://github.com/u-boot/u-boot/archive/refs/tags/v2023.04.tar.gz  
opensbi - https://github.com/riscv-software-src/opensbi, tar - https://github.com/riscv-software-src/opensbi/archive/refs/tags/v1.2.tar.gz  
busybox - git - git://busybox.net/busybox.git, tar - https://busybox.net/downloads/busybox-1.36.1.tar.bz2  
linux - https://github.com/torvalds/linux  
bash - ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu-