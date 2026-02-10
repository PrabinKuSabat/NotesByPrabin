# Why QEMU?

Why not Docker!!  
That's same question I was asked with by one of my juniors. See Docker and QEMU server fundamentally different purposes and are not interchangeable. Docker is a _**containerization platform**_ that shares the host operating system's kernel across all containers. Whereas QEMU by contrast is a _**hardware emulator**_ and virtualizer designed to run operating systems with custom kernels.

> [!info] Hardware Acceleration for QEMU  
> Using Kernel Virtual Machine(KVM), systems achieve near-native performance running 8x to 12x faster than software-based translation using QEMU's TCG(Tiny Code Generator).

# Dependencies

``` bash
sudo apt update
sudo apt install qemu-system-riscv64 build-essential bc bison flex  libssl-dev gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu opensbi u-boot-qemu qemu-efi-riscv64
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

# Kernel Aquisition

```bash
git clone https://github.com/orangepi-xunlong/linux-orangepi.git
cd linux-orangepi
git checkout origin/orange-pi-6.6-ky
```

# Extracting Kernel Configuration from Orange Pi RV2 Image

## **Method 1: Mount the Image and Extract (Recommended - Fastest)**

This method directly accesses the `/boot` directory inside your disk image file without needing to boot the system.  
**Step 1: Find the image and mount it as a loopback device**

```
cd /home/prabinkumarsabat/Downloads/Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63

# Verify the image file exists
ls -lh Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img

# Create a mount point
mkdir -p ~/mnt_orange_pi

# Mount the image (loopback mount)
sudo mount -o loop Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img ~/mnt_orange_pi
```

**Step 2: Verify the mount and explore contents**

```
# Check if mount was successful
mount | grep "mnt_orange_pi"

# List boot directory contents
ls -la ~/mnt_orange_pi/boot/
```

Output:

```
# Check if mount was successful
mount | grep "mnt_orange_pi"

# List boot directory contents
ls -la ~/mnt_orange_pi/boot/
```

**Step 3: Copy the kernel config to your working directory**

```
# Copy the config file
sudo cp ~/mnt_orange_pi/boot/config-6.6.63-ky ~/linux-orangepi/.config

# Fix permissions
sudo chown $(whoami):$(whoami) ~/linux-orangepi/.config

# Verify it was copied
head -20 ~/linux-orangepi/.config
```

**Step 4: Unmount when done**

```
# Unmount the image
sudo umount ~/mnt_orange_pi

# Verify unmount
mount | grep "mnt_orange_pi"  # Should return nothing
```

## Config generation

```bash
cd ~/linux-orangepi
export ARCH=riscv
export CROSS_COMPILE=riscv64-linux-gnu-
make clean #removes any existing build artifacts
make oldconfig #Creates default .config
```

> [!info] Interactive config modification  
> U can make use of this interactive menu to configure the kernel  
> `make menuconfig`

> [!abstract] Other configs  
> Use `ls -la arch/riscv/configs` to check all the available configs.

## Compilation

```bash
make -j$(nproc) 
# U should use a specific no. of cores ( < totall cores ) if u want to multi-task while the process is running.
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
