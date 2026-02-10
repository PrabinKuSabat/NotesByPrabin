# Why QEMU?

Why not Docker!!  
That's same question I was asked with by one of my juniors. See Docker and QEMU server fundamentally different purposes and are not interchangeable. Docker is a _**containerization platform**_ that shares the host operating system's kernel across all containers. Whereas QEMU by contrast is a _**hardware emulator**_ and virtualizer designed to run operating systems with custom kernels.

> [!info] Hardware Acceleration for QEMU  
> Using Kernel Virtual Machine(KVM), systems achieve near-native performance running 8x to 12x faster than software-based translation using QEMU's TCG(Tiny Code Generator).

# Dependencies

``` bash
sudo apt update
sudo apt install opensbi u-boot-qemu 
```

# Hardware Definitions

## QEMU Machines

_Use `qemu-system-riscv64 -machine help` to see for yourself._

| Machine                    | What it emulates                                      | Use case                                                 |
| -------------------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| `virt`                     | Generic RISC-V virtual machine w/ VirtIO disk/network | **YOUR MAIN CHOICE** - modern, flexible, supports Ubuntu |
| `spike`                    | UC Berkeley Spike simulator                           | Reference simulator, minimal peripherals                 |
| `sifive_u`                 | SiFive U-series HiFive Unleashed board                | Real SiFive hardware clone                               |
| `sifive_e`                 | SiFive E-series HiFive1 board                         | Real SiFive low-end hardware clone                       |
| `shakti_c`                 | Shakti C-class development board                      | IIT Madras research board                                |
| `microchip-icicle-kit`     | Microchip PolarFire Icicle Kit FPGA board             | FPGA dev board w/ RISC-V                                 |
| `xiangshan-kunminghu`      | Xiangshan FPGA prototype (Chinese research)           | High-performance research CPU                            |
| `amd-microblaze-v-generic` | AMD MicroBlaze-V softcore (not RISC-V)                | FPGA softcore CPU                                        |
| `none`                     | Empty machine (no peripherals)                        | Testing bare CPU                                         |  

QEMU can be designed to simulate entire boards/machines like the spike, sifive_u, sifive_e, shakti_c. But for the board which we have, i.e. OrangePi RV2, the in-built `virt` machine mode will work.

> [!note] virt  
> It's a Generic RISC-V platform with modern peripherals(PCle, VirtIO disk/network).

## QEMU CPUs

_Use `qemu-system-riscv64 -cpu help` to see for yourself._

**Generic ISA levels**:

```
rv32, rv32e, rv32i    = 32-bit RISC-V base ISA
rv64, rv64e, rv64i    = 64-bit RISC-V base ISA
rva22s64, rva22u64    = RVA22 (RV64GC + Vector 1.0 Supervisor mode)
rva23s64, rva23u64    = RVA23 (newer RVA22 + more extensions)
x-rv128               = Experimental 128-bit RISC-V
```

We should be focusing on using the rva22s64 for now. As for the hardware we have, **Ky X1** processor, is based on **RV64GCVB** IS(_**RVA22** profile and **RVV1.0** RISC-V Vector extension._).

**Specific Implementation**:

```
sifive-e31/e34/e51    = SiFive E-series embedded cores
sifive-u34/u54        = SiFive U-series application cores  
shakti-c              = IIT Madras Shakti C-class core
thead-c906            = T-Head XuanTie C906 (Chinese Aliyun core)
lowrisc-ibex          = LowRISC Ibex (small embedded core)
max/max32             = Ventana Micro Veyron cores
tt-ascalon/veyron-v1  = T-Head/Transwarp cores
xiangshan-*           = Xiangshan open-source high-perf cores
```

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


# Last Working Command
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
