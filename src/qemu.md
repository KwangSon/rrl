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

### rootfs
```
# rootfs
# untar archrootfs
mkdir archrootfs

https://elinux.org/QEMU

dd if=/dev/zero of=rootfs.img bs=1M count=1024
mke2fs -j rootfs.img
sudo mkdir /mnt/rootfs
sudo mount -o loop rootfs.img /mnt/rootfs
sudo rsync -a archrootfs/ /mnt/rootfs
sudo chown -R root:root /mnt/rootfs/
sudo sync
sudo umount /mnt/rootfs/
sudo rmdir /mnt/rootfs
```

### test
```
# initramfs
qemu-system-riscv64 -nographic -machine virt \
-kernel linux/build_qemu/arch/riscv/boot/Image \
-initrd initramfs.cpio.gz \
-append "console=ttyS0"
```

```
qemu-system-riscv64 -nographic -machine virt \
-kernel linux/build_qemu/arch/riscv/boot/Image \
-drive file=rootfs.img,format=raw,if=none,id=hd0 \
-device virtio-blk-device,drive=hd0 \
-append "root=/dev/vda rw" \
-net nic,model=virtio -net user
```


## target patch
https://lore.kernel.org/linux-riscv/20230405-itinerary-handgrip-a5ffba368148@spud/T/#u