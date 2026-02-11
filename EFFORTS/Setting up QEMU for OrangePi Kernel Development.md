---
dg-publish: true
---

# Why QEMU?

Why not Docker!!  
That's same question I was asked with by one of my juniors. See Docker and QEMU server fundamentally different purposes and are not interchangeable. Docker is a _**containerization platform**_ that shares the host operating system's kernel across all containers. Whereas QEMU by contrast is a _**hardware emulator**_ and virtualizer designed to run operating systems with custom kernels.

> [!info] Hardware Acceleration for QEMU  
> Using Kernel Virtual Machine(KVM), systems achieve near-native performance running 8x to 12x faster than software-based translation using QEMU's TCG(Tiny Code Generator).

# Dependencies

``` bash
sudo apt update
sudo apt install -y qemu-system-riscv64 build-essential bc bison flex  libssl-dev gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu opensbi u-boot-qemu qemu-efi-riscv64
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

## Extracting Kernel Configuration from Orange Pi RV2 Image

This method directly accesses the `/boot` directory inside your disk image file without needing to boot the system.  
**Step 1: Find the image and mount it as a loopback device**

```bash
cd ~/Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63

# Verify the image file exists
ls -lh Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img

# Setup loop device with partition scanning
sudo losetup -fP Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img

# Find which loop device was assigned
losetup -a | grep Orangepirv2

# It will show something like: /dev/loop43: []: (/home/…/Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img)
# Note the loop device number (e.g., loop43)

# List the partitions
ls -l /dev/loop43*
# You should see: /dev/loop43p1, /dev/loop43p2, etc.

# Create a mount point
mkdir -p ~/mnt_orange_pi

# Mount the root partition (usually p2)
sudo mount /dev/loop43p2 ~/mnt_orange_pi
```

**Step 2: Verify the mount and explore contents**

```bash
# Check if mount was successful
mount | grep "mnt_orange_pi"

# List boot directory contents
ls -la ~/mnt_orange_pi/boot/
```

Output:

```bash
# You should see:
# config-6.6.63-ky
# vmlinuz-6.6.63-ky
# initrd.img-6.6.63-ky
# dtbs/
```

**Step 3: Copy the kernel config to your working directory**

```bash
# Copy the config file
sudo cp ~/mnt_orange_pi/boot/config-6.6.63-ky ~/linux-orangepi/.config

# Fix permissions
sudo chown $(whoami):$(whoami) ~/linux-orangepi/.config

# Verify it was copied
head -20 ~/linux-orangepi/.config

# Check that it's a valid kernel config 
grep "CONFIG_RISCV=y" ~/riscv/linux-orangepi/.config #should be =y
grep "CONFIG_KY_WATCHDOG" ~/riscv/linux-orangepi/.config #should be =y
grep "Linux/riscv" ~/riscv/linux-orangepi/.config 
#version should be 6.6.63 as the OrangePi kernel is 6.6.63.
# Count enabled options 
echo "Number of enabled config options:" 
grep -c "^CONFIG_.*=y$" ~/riscv/linux-orangepi/.config
```

**Step 4: Unmount when done**

```bash
# Unmount the image
sudo umount ~/mnt_orange_pi

# Detach loop device (use the correct loop number from Step 3) 
sudo losetup -d /dev/loop43

# Verify unmount
mount | grep "mnt_orange_pi"  # Should return nothing


```

## Config generation

```bash
cd ~/linux-orangepi
export ARCH=riscv
export CROSS_COMPILE=riscv64-linux-gnu-
export LOCALVERSION=""
make clean #removes any existing build artifacts
make olddefconfig # Update config for any new kernel options

make menuconfig

#Do the following in the interactive menu
# Device Drivers -> Virtio drivers -> 
# Select "Platform bus driver for memory mapped virtio devices" as <*> (built-in)
# Save and exit

```

- You can disable `CONFIG_KY_WATCHDOG` since QEMU won't emulate this hardware
- Enable `CONFIG_VIRTIO` options (should already be enabled)

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

### Verify Kernel Image

```bash
# Check that kernel image was created successfully
ls -lh arch/riscv/boot/Image

# Expected output:
# -rw-r--r-- 1 user user 18M Jan 27 15:30 arch/riscv/boot/Image

# File size should be 15-25 MB typically
file arch/riscv/boot/Image
```

## Root File System Extraction

**Step 1: Find the sector stating location**

```bash
sudo fdisk -l Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img
```

_Output Example :_

> Disk Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img: 2.21 GiB, 2373976064 bytes, 4636672 sectors  
> Units: sectors of 1 * 512 = 512 bytes  
> Sector size (logical/physical): 512 bytes / 512 bytes  
> I/O size (minimum/optimal): 512 bytes / 512 bytes  
> Disklabel type: dos  
> Disk identifier: 0xbbaac27f
>
> _Device Boot Start End Sectors Size Id Type_ Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img1 _**61440**_ 4636671 4575232 2.2G 83 Linux

**Step 2 : Obtain the entire OS image with the rootFS inside**

```
cd ~/riscv

# Copy the Orange Pi image to use as rootfs
cp /home/prabinkumarsabat/Downloads/Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63/Orangepirv2_1.0.0_ubuntu_noble_server_linux6.6.63.img ./rootfs.img

# Optional: Expand the image for more space
qemu-img resize -f raw rootfs.img +5G

# Check the image
ls -lh rootfs.img
```

**Step 3: Extract just the file-system**

We assume the file-system is starting at sector 61440(value obtained in step 1).

```
cd ~/riscv

# Calculate: 61440 sectors × 512 bytes = 31457280 bytes offset
# Extract the partition to a separate file
dd if=rootfs.img of=rootfs-partition.img bs=512 skip=61440
```

# Working Commands

## Telenet Method

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

## GDB Method

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

# How to use

```
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

**It waits for you to connect using GDB.** Only then the process starts.

```bash
gdb-multiarch ~/riscv/linux-orangepi/vmlinux #to start gdb.
target remote localhost:1234 #connect to the qemu.
```

---
**PS:**
- If there is any-error feel free to discuss : prabinkusabat@gmail.com.
- If you run into error where it says unable to load the console (or anything similar to console), just do a chatgpt asking how to add console support or *how to enable `serial-getty@hvc0.service`.

---
