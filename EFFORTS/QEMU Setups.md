# Telenet Method

```bash
qemu-system-riscv64 \
  -machine virt -cpu rv64 -m 4G -smp 2 \
  -kernel ~/riscv/linux-orangepi/arch/riscv/boot/Image \
  -append "root=/dev/vda rw console=hvc0" \
  -drive file=/home/prabinkumarsabat/riscv/rootfs-partition.img,format=raw,id=hd0,if=none \
  -device virtio-blk-device,drive=hd0 \
  -device virtio-serial-device \
  -device virtconsole,chardev=console \
  -chardev stdio,id=console
VNC server running on 127.0.0.1:5900
```

```bash
telnet 127.0.0.1 55555
```

# GDB Method

```bash
qemu-system-riscv64 \
  -machine virt -cpu rv64 -m 4G -smp 2 \
  -kernel ~/riscv/linux-orangepi/arch/riscv/boot/Image \
  -append "root=/dev/vda rw console=hvc0" \
  -drive file=/home/prabinkumarsabat/riscv/rootfs-partition.img,format=raw,id=hd0,if=none \
  -device virtio-blk-device,drive=hd0 \
  -device virtio-serial-device \
  -device virtconsole,chardev=console \
  -chardev stdio,id=console \
  -monitor telnet:127.0.0.1:55555,server,nowait \
  -gdb tcp::1234 -S
```

```bash
gdb-multiarch ~/riscv/linux-orangepi/vmlinux #to start gdb.
target remote localhost:1234 #connect to the qemu.
```

```bash
# Command to check how many files will get compiled inside  
```