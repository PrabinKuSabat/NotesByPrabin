Mr. Prabin, use a two-profile qualification workflow:

1. `baseline`: vendor `x1_defconfig` plus QEMU and strict-debug support.
2. `m0`: the same baseline, pruned to the “8 harts + BusyBox shell in initramfs” contract.

Do not put either image on the board until the configuration audit, RV2 DTB audit, strict QEMU test, and an intentional negative test all behave correctly.

A key repository finding changes our earlier console plan: the Ky PXA driver requires `!SERIAL_8250 || COMPILE_TEST`. Therefore, enabling QEMU’s 8250 console while disabling `COMPILE_TEST` can silently remove the hardware console driver. Use `hvc0` through VirtIO in QEMU and keep the PXA `ttyS0` driver for hardware. See the exact vendor [serial Kconfig](https://github.com/orangepi-xunlong/linux-orangepi/blob/ae9e974d3e19f460b6397bfe8f0f1417a073ce05/drivers/tty/serial/Kconfig).

# 1. Install the host tools

These package names apply to Ubuntu 24.04:

```bash
sudo apt update
sudo apt install -y \
    git curl build-essential bc bison flex \
    libssl-dev libelf-dev libncurses-dev dwarves \
    cpio rsync file device-tree-compiler sparse \
    qemu-system-riscv \
    gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu \
    libc6-dev-riscv64-cross
```

Then install Device Tree schema support:

```python3
pip3 install --user dtschema
```

Verify them:

```bash
command -v riscv64-linux-gnu-gcc
command -v qemu-system-riscv64
command -v fdtget
riscv64-linux-gnu-gcc --version
qemu-system-riscv64 --version
```

Ubuntu packages `qemu-system-misc` and `dt-schema` are documented in the [Ubuntu QEMU package](https://packages.ubuntu.com/en/source/noble-updates/qemu) and [DT-schema package](https://packages.ubuntu.com/noble/dt-schema).

# 2. Download the pinned vendor kernel

Use the known Linux 6.6.63 RV2 commit, not the moving branch head:

```bash
export RV2_WORK="$PWD"
export RV2_PIN="ae9e974d3e19f460b6397bfe8f0f1417a073ce05"
export KERNEL_SRC="$RV2_WORK/src/linux-orangepi"
export ARCH=riscv
export CROSS_COMPILE=riscv64-linux-gnu-

mkdir -p "$RV2_WORK"/{src,configs,out,rootfs,artifacts,logs,scripts}

git clone \
    --filter=blob:none \
    --single-branch \
    --branch orange-pi-6.6-ky \
    https://github.com/orangepi-xunlong/linux-orangepi.git \
    "$KERNEL_SRC"

git -C "$KERNEL_SRC" switch --detach "$RV2_PIN"

test "$(git -C "$KERNEL_SRC" rev-parse HEAD)" = "$RV2_PIN"
git -C "$KERNEL_SRC" show -s --format='%H %s'
grep -E '^(VERSION|PATCHLEVEL|SUBLEVEL) =' "$KERNEL_SRC/Makefile"
```

Expected commit description: `Support Orange Pi RV2`. Relevant inputs are the vendor [`x1_defconfig`](https://github.com/orangepi-xunlong/linux-orangepi/blob/ae9e974d3e19f460b6397bfe8f0f1417a073ce05/arch/riscv/configs/x1_defconfig) and [`x1_orangepi-rv2.dts`](https://github.com/orangepi-xunlong/linux-orangepi/blob/ae9e974d3e19f460b6397bfe8f0f1417a073ce05/arch/riscv/boot/dts/ky/x1_orangepi-rv2.dts).

Do not start from `tinyconfig`. Start from the vendor seed and prune it, because Kconfig dependencies can silently reject requested settings. Linux documents this miniconfig behaviour in its [Kconfig documentation](https://docs.kernel.org/6.6/kbuild/kconfig.html).

# 3. Build a static BusyBox initramfs

BusyBox 1.38.0 is available from the [official BusyBox download site](https://busybox.net/downloads/).

```bash
set -e

cd "$RV2_WORK/src"

# Clone the maintainer's GitHub mirror
git clone https://github.com/vda-linux/busybox_mirror.git busybox

cd busybox

# Make absolutely sure the source is pinned to BusyBox 1.38.0
git checkout --detach fc71374dfccd46448c62947269a35f1420d7ee28

# Confirm pinned source revision
test "$(git rev-parse HEAD)" = \
"fc71374dfccd46448c62947269a35f1420d7ee28"

# Clean previous build products
make distclean

make ARCH=riscv \
     CROSS_COMPILE="$CROSS_COMPILE" \
     defconfig

sed -i 's/^# CONFIG_STATIC is not set$/CONFIG_STATIC=y/' .config
sed -i 's/^CONFIG_TC=y$/# CONFIG_TC is not set/' .config

mkdir -p "$RV2_WORK/configs"

cp .config \
   "$RV2_WORK/configs/busybox-1.38.0-riscv64.config"

sha256sum \
   "$RV2_WORK/configs/busybox-1.38.0-riscv64.config"
   

git rev-parse HEAD \
> "$RV2_WORK/configs/busybox-1.38.0.commit"

# Restore OUR locked BusyBox configuration
cp "$RV2_WORK/configs/busybox-1.38.0-riscv64.config" .config

# Verify static linking is enabled
grep -qx 'CONFIG_STATIC=y' .config

# Build for RISC-V
make ARCH=riscv \
     CROSS_COMPILE="$CROSS_COMPILE" \
     -j"$(nproc)"

# Install into the root filesystem
rm -rf "$RV2_WORK/rootfs"

make ARCH=riscv \
     CROSS_COMPILE="$CROSS_COMPILE" \
     CONFIG_PREFIX="$RV2_WORK/rootfs" \
     install
     
file "$RV2_WORK/rootfs/bin/busybox"
ls -l "$RV2_WORK/rootfs/bin/sh"
```

Create the qualification `/init`:

```bash
mkdir -p "$RV2_WORK/rootfs"/{dev,proc,sys,tmp}

tee "$RV2_WORK/rootfs/init" >/dev/null <<'EOF'
#!/bin/sh

PATH=/sbin:/bin:/usr/sbin:/usr/bin
export PATH

fail()
{
    echo "QEMU_GATE_FAIL: $*"
    sync
    poweroff -f
    while :; do sleep 1; done
}

mount -t proc proc /proc || fail "cannot mount proc"
mount -t sysfs sysfs /sys || fail "cannot mount sysfs"

grep -q " /dev devtmpfs " /proc/mounts ||
    mount -t devtmpfs devtmpfs /dev ||
    fail "cannot mount devtmpfs"

exec </dev/console >/dev/console 2>&1

mount -t tmpfs -o size=512M tmpfs /tmp ||
    fail "cannot mount tmpfs"

echo "QEMU_GATE_START"

[ "$$" -eq 1 ] || fail "/init is not PID 1"
[ "$(uname -m)" = "riscv64" ] || fail "wrong architecture"

model="$(tr -d '\000' </sys/firmware/devicetree/base/model)"
case "$model" in
    *riscv-virtio*) ;;
    *) fail "unexpected QEMU model: $model" ;;
esac

cpu_count="$(grep -c '^processor' /proc/cpuinfo)"
[ "$cpu_count" -eq 8 ] || fail "expected 8 CPUs, found $cpu_count"
[ "$(cat /sys/devices/system/cpu/online)" = "0-7" ] ||
    fail "not all CPUs online"

cpu=0
while [ "$cpu" -lt 8 ]; do
    taskset -c "$cpu" \
        awk -v expected="$cpu" \
        '{ exit ($39 == expected ? 0 : 1) }' \
        /proc/self/stat ||
        fail "CPU-affinity execution failed on CPU $cpu"
    cpu=$((cpu + 1))
done

time_before="$(cut -d. -f1 /proc/uptime)"
sleep 2
time_after="$(cut -d. -f1 /proc/uptime)"
[ $((time_after - time_before)) -ge 1 ] ||
    fail "timer did not advance"

dmesg | grep -Eq \
    'mapped [0-9]+ interrupts with [0-9]+ handlers' ||
    fail "PLIC initialization not found"

[ -c /dev/hvc0 ] || fail "hvc0 missing"
[ -d /sys/bus/virtio/drivers/virtio_console ] ||
    fail "VirtIO console driver not bound"

mem_kb="$(awk '/^MemTotal:/ {print $2}' /proc/meminfo)"
[ "$mem_kb" -ge 3800000 ] ||
    fail "less than expected 4-GiB memory: ${mem_kb} KiB"

(sh -c 'exit 0') &
child="$!"
wait "$child" || fail "fork/exec/wait test failed"

dd if=/dev/zero of=/tmp/memory.test \
    bs=1M count=256 2>/dev/null ||
    fail "memory allocation/write test failed"
rm -f /tmp/memory.test

dmesg >/tmp/dmesg
if grep -E \
    'BUG:|WARNING:|Oops:|Kernel panic|Unable to handle kernel|soft lockup|hard LOCKUP|rcu:.*stall|possible circular locking dependency' \
    /tmp/dmesg >/tmp/fatal; then
    cat /tmp/fatal
    fail "fatal kernel diagnostic found"
fi

echo "QEMU_GATE_PASS: model=$model cpus=$cpu_count mem_kb=$mem_kb"
sync
poweroff -f
fail "poweroff returned unexpectedly"
EOF

chmod 0755 "$RV2_WORK/rootfs/init"
```

Verify that BusyBox is RISC-V and static:

```bash
riscv64-linux-gnu-readelf -h \
    "$RV2_WORK/rootfs/bin/busybox" |
    grep -q 'Machine:.*RISC-V'

if riscv64-linux-gnu-readelf -l \
    "$RV2_WORK/rootfs/bin/busybox" |
    grep -q 'INTERP'; then
    echo "ERROR: BusyBox is dynamically linked"
    exit 1
fi

for app in sh mount awk grep find wc dd sleep taskset poweroff; do
    find "$RV2_WORK/rootfs" -name "$app" -print -quit |
        grep -q . || {
            echo "Missing BusyBox applet: $app"
            exit 1
        }
done
```

Package it reproducibly :

```bash
find "$RV2_WORK/rootfs" \
    -exec touch -h -d '@0' {} +

(
    cd "$RV2_WORK/rootfs"
    find . -print0 |
        LC_ALL=C sort -z |
        cpio --null -o --format=newc --owner=0:0 2>/dev/null
) |
    gzip -9n >"$RV2_WORK/artifacts/rootfs.cpio.gz"

gzip -t "$RV2_WORK/artifacts/rootfs.cpio.gz"
```

Read more : [[Creating a reproducible initramfs]].

# 4. Create the qualification configuration

Create `qualification.fragment`:

```bash
tee "$RV2_WORK/configs/qualification.fragment" >/dev/null <<'EOF'
# CONFIG_TOUCHSCREEN_FTS is not set

CONFIG_PRINTK=y
CONFIG_TTY=y
CONFIG_BLK_DEV_INITRD=y
CONFIG_RD_GZIP=y
CONFIG_BINFMT_ELF=y
CONFIG_BINFMT_SCRIPT=y
CONFIG_DEVTMPFS=y
CONFIG_DEVTMPFS_MOUNT=y
CONFIG_PROC_FS=y
CONFIG_SYSFS=y
CONFIG_TMPFS=y
CONFIG_IKCONFIG=y
CONFIG_IKCONFIG_PROC=y

CONFIG_SERIAL_EARLYCON_RISCV_SBI=y
CONFIG_VIRTIO_MENU=y
CONFIG_VIRTIO_MMIO=y
CONFIG_VIRTIO_CONSOLE=y

CONFIG_RISCV_ISA_V=y
CONFIG_RISCV_ISA_V_DEFAULT_ENABLE=y

CONFIG_DEBUG_KERNEL=y
CONFIG_DEBUG_LIST=y
CONFIG_DEBUG_VM=y
CONFIG_SCHED_STACK_END_CHECK=y
CONFIG_PANIC_ON_OOPS=y
CONFIG_PANIC_TIMEOUT=-1
CONFIG_DETECT_HUNG_TASK=y
CONFIG_DEFAULT_HUNG_TASK_TIMEOUT=30
CONFIG_BOOTPARAM_HUNG_TASK_PANIC=y
CONFIG_WQ_WATCHDOG=y
CONFIG_PROVE_LOCKING=y
CONFIG_DEBUG_ATOMIC_SLEEP=y
CONFIG_DEBUG_WX=y
CONFIG_MAGIC_SYSRQ=y
CONFIG_SLUB_DEBUG=y
EOF
```

Create the M0 pruning and hardware fragment:

```bash
tee "$RV2_WORK/configs/m0.fragment" >/dev/null <<'EOF'
# CONFIG_COMPILE_TEST is not set
# CONFIG_MODULES is not set
# CONFIG_BLOCK is not set
# CONFIG_NET is not set
# CONFIG_PCI is not set
# CONFIG_MTD is not set
# CONFIG_INPUT is not set
# CONFIG_MEDIA_SUPPORT is not set
# CONFIG_DRM is not set
# CONFIG_FB is not set
# CONFIG_SOUND is not set
# CONFIG_USB is not set
# CONFIG_MMC is not set
# CONFIG_I2C is not set
# CONFIG_SPI is not set
# CONFIG_REGULATOR is not set
# CONFIG_THERMAL is not set
# CONFIG_WATCHDOG is not set
# CONFIG_RTC_CLASS is not set
# CONFIG_NEW_LEDS is not set
# CONFIG_REMOTEPROC is not set
# CONFIG_RPMSG_VIRTIO is not set
# CONFIG_IIO is not set
# CONFIG_PWM is not set
# CONFIG_CPU_FREQ is not set
# CONFIG_CPU_IDLE is not set
# CONFIG_NAMESPACES is not set
# CONFIG_CGROUPS is not set
# CONFIG_SECURITY is not set
# CONFIG_PROFILING is not set
# CONFIG_BPF_JIT is not set
# CONFIG_BPF_JIT_ALWAYS_ON is not set

CONFIG_SOC_KY=y
CONFIG_SOC_KY_X1=y
CONFIG_SMP=y
CONFIG_NR_CPUS=8
CONFIG_RISCV_SBI=y

# CONFIG_SERIAL_8250 is not set
CONFIG_SERIAL_PXA=y
# CONFIG_SERIAL_PXA_NON8250 is not set
CONFIG_SERIAL_PXA_KY_X1=y
CONFIG_SERIAL_PXA_CONSOLE=y

CONFIG_KY_X1_CCU=y
CONFIG_RESET_CONTROLLER=y
CONFIG_RESET_X1_KY=y
CONFIG_PM=y
CONFIG_SUSPEND=y
CONFIG_KY_PM_DOMAINS=y
CONFIG_PINCTRL=y
CONFIG_PINCTRL_SINGLE=y
CONFIG_GPIOLIB=y
CONFIG_GPIO_X1=y
CONFIG_DMADEVICES=y
CONFIG_MMP_PDMA_DRIVER=y
CONFIG_MMP_PDMA_KY_X1=y
CONFIG_KY_X1_DMA_RANGE=y
CONFIG_CMA=y
CONFIG_DMA_CMA=y
EOF
```

# 5. Build the control and M0 profiles

```bash
export SOURCE_DATE_EPOCH=0
export KBUILD_BUILD_TIMESTAMP="1970-01-01 00:00:00 UTC"
export KBUILD_BUILD_USER=rv2
export KBUILD_BUILD_HOST=qualification

build_profile()
{
    profile="$1"
    shift
    output="$RV2_WORK/out/$profile"

    mkdir -p "$output"

    "$KERNEL_SRC/scripts/kconfig/merge_config.sh" \
        -m -O "$output" \
        "$KERNEL_SRC/arch/riscv/configs/x1_defconfig" \
        "$@"

    KCONFIG_WARN_UNKNOWN_SYMBOLS=1 KCONFIG_WERROR=1 \
        make -C "$KERNEL_SRC" O="$output" olddefconfig

    make -C "$KERNEL_SRC" O="$output" \
        -j"$(nproc)" Image
}

build_profile baseline \
    "$RV2_WORK/configs/qualification.fragment"

build_profile m0 \
    "$RV2_WORK/configs/m0.fragment" \
    "$RV2_WORK/configs/qualification.fragment"

make -C "$KERNEL_SRC" O="$RV2_WORK/out/m0" \
    -j"$(nproc)" dtbs
```

The baseline is a control experiment. If it fails QEMU, do not start pruning investigations. If baseline passes but M0 fails, the fault is almost certainly in the pruning/configuration delta.

Understand more about this section here : [[repeatable kernel build function]] .

# 6. Make configuration closure a hard gate

```bash
CFG="$RV2_WORK/out/m0/.config"
config_failed=0

required_y=(
    SOC_KY SOC_KY_X1 SMP RISCV_SBI
    RISCV_INTC SIFIVE_PLIC RISCV_TIMER
    RISCV_ISA_V RISCV_ISA_V_DEFAULT_ENABLE
    PRINTK TTY
    BLK_DEV_INITRD RD_GZIP
    BINFMT_ELF BINFMT_SCRIPT
    DEVTMPFS DEVTMPFS_MOUNT PROC_FS SYSFS TMPFS
    SERIAL_EARLYCON_RISCV_SBI
    SERIAL_PXA SERIAL_PXA_KY_X1 SERIAL_PXA_CONSOLE
    VIRTIO_MENU VIRTIO_MMIO VIRTIO_CONSOLE HVC_DRIVER
    KY_X1_CCU RESET_CONTROLLER RESET_X1_KY
    PM PM_SLEEP SUSPEND KY_PM_DOMAINS
    PINCTRL PINCTRL_SINGLE
    DMADEVICES MMP_PDMA_DRIVER MMP_PDMA_KY_X1
    KY_X1_DMA_RANGE CMA DMA_CMA
    DEBUG_KERNEL DEBUG_LIST DEBUG_VM
    SCHED_STACK_END_CHECK PANIC_ON_OOPS
    DETECT_HUNG_TASK BOOTPARAM_HUNG_TASK_PANIC
    WQ_WATCHDOG PROVE_LOCKING DEBUG_ATOMIC_SLEEP
    DEBUG_WX MAGIC_SYSRQ SLUB_DEBUG
)

for symbol in "${required_y[@]}"; do
    grep -qx "CONFIG_${symbol}=y" "$CFG" || {
        echo "CONFIG GATE FAIL: CONFIG_${symbol} is not built-in"
        config_failed=1
    }
done

required_n=(COMPILE_TEST MODULES BLOCK NET PCI MTD SERIAL_8250)

for symbol in "${required_n[@]}"; do
    grep -qx "# CONFIG_${symbol} is not set" "$CFG" || {
        echo "CONFIG GATE FAIL: CONFIG_${symbol} is not disabled"
        config_failed=1
    }
done

grep -qx 'CONFIG_NR_CPUS=8' "$CFG" || config_failed=1

if grep -q '=m$' "$CFG"; then
    echo "CONFIG GATE FAIL: modules remain"
    config_failed=1
fi

test "$config_failed" -eq 0 || exit 1

echo "built-ins: $(grep -c '=y$' "$CFG")"
stat -c 'Image bytes: %s' \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image"
```

This specifically catches the dangerous case where Kconfig accepts the file but silently drops the Ky UART, clock, reset, power-domain or DMA driver.

# 7. Audit the actual RV2 DTB

```bash
DTB="$RV2_WORK/out/m0/arch/riscv/boot/dts/ky/x1_orangepi-rv2.dtb"

test -s "$DTB"

test "$(fdtget -t s "$DTB" / model)" \
    = "ky x1 orangepi-rv2 board"

test "$(fdtget -t s "$DTB" / compatible)" \
    = "ky,orangepi-rv2 ky,x1"

test "$(fdtget -t x "$DTB" /memory@0 reg)" \
    = "0 0 0 80000000"

test "$(fdtget -t x "$DTB" /memory@100000000 reg)" \
    = "1 0 0 80000000"

test "$(fdtget -t s "$DTB" /chosen stdout-path)" \
    = "serial0:115200n8"

test "$(fdtget -t s "$DTB" /soc/serial@d4017000 compatible)" \
    = "ky,pxa-uart"

test "$(fdtget -t s "$DTB" /soc/clock-controller@d4050000 compatible)" \
    = "ky,x1-clock"

test "$(fdtget -t s "$DTB" /soc/reset-controller@d4050000 compatible)" \
    = "ky,x1-reset"

test "$(fdtget -t s "$DTB" /soc/interrupt-controller@e0000000 compatible)" \
    = "riscv,plic0"

test "$(fdtget -t s "$DTB" /soc/clint@e4000000 compatible)" \
    = "riscv,clint0"

test "$(fdtget -t s "$DTB" /cpus/cpu@0 riscv,isa)" \
    = "rv64imafdcv"

cpu_nodes="$(
    fdtget -l "$DTB" /cpus |
        grep -c '^cpu@[0-9]'
)"
test "$cpu_nodes" -eq 8

echo "RV2 DTB GATE PASS"
```

The critical closure being checked is:

|RV2 DT compatible|Required built-in|
|---|---|
|`riscv,plic0`|`SIFIVE_PLIC`|
|`ky,x1-clock`|`KY_X1_CCU`|
|`ky,x1-reset`|`RESET_X1_KY`|
|`ky,power-controller`|`KY_PM_DOMAINS`|
|`pinconf-single-aib`|`PINCTRL_SINGLE`|
|`ky,pdma-1.0`|`MMP_PDMA_KY_X1`|
|`ky-dram-bus`|`KY_X1_DMA_RANGE`|
|`ky,pxa-uart`|`SERIAL_PXA_KY_X1`|

Also run:

```bash
make -C "$KERNEL_SRC" O="$RV2_WORK/out/m0" \
    W=1 dt_binding_check dtbs_check \
    2>&1 | tee "$RV2_WORK/logs/dt-schema.log"
```

The vendor tree may have pre-existing schema diagnostics. Errors involving the critical nodes above should block hardware testing. Record unrelated vendor warnings as the baseline and reject any new warnings after your own DTS changes. Linux explains why both checks are needed in its [DT schema documentation](https://docs.kernel.org/devicetree/bindings/writing-schema.html).

# 8. Run the strict QEMU gate

Create the runner:

```bash
tee "$RV2_WORK/scripts/run-qemu-gate.sh" >/dev/null <<'EOF'
#!/usr/bin/env bash
set -euo pipefail

kernel="${1:?kernel Image required}"
initramfs="${2:?initramfs required}"
label="${3:-qemu}"

smp="${RV2_QEMU_SMP:-8}"
memory="${RV2_QEMU_MEM:-4G}"
cpu="${RV2_QEMU_CPU:-rv64}"
log_dir="${RV2_LOG_DIR:-$PWD/logs}"

mkdir -p "$log_dir"

early_log="$log_dir/${label}-early.log"
runtime_log="$log_dir/${label}-runtime.log"
all_log="$log_dir/${label}-all.log"

: >"$early_log"
: >"$runtime_log"

set +e
timeout --foreground --signal=TERM --kill-after=5s 240s \
    qemu-system-riscv64 \
        -machine virt \
        -accel tcg,thread=multi \
        -cpu "$cpu" \
        -smp "$smp" \
        -m "$memory" \
        -bios default \
        -kernel "$kernel" \
        -initrd "$initramfs" \
        -append "earlycon=sbi console=hvc0 rdinit=/init loglevel=8 ignore_loglevel initcall_debug panic_on_warn=1 oops=panic panic=-1 hung_task_panic=1 workqueue.watchdog_thresh=30 slub_debug=FZPU" \
        -nodefaults \
        -no-user-config \
        -display none \
        -monitor none \
        -nic none \
        -serial "file:$early_log" \
        -chardev stdio,id=hvc,signal=off \
        -device virtio-serial-device \
        -device virtconsole,chardev=hvc \
        -no-reboot \
    2>&1 | tee "$runtime_log"

qemu_status="${PIPESTATUS[0]}"
set -e

cat "$early_log" "$runtime_log" >"$all_log"

fatal='QEMU_GATE_FAIL|Kernel panic|BUG:|WARNING:|Oops:|Unable to handle kernel|soft lockup|hard LOCKUP|rcu:.*stall|possible circular locking dependency'

if [ "$qemu_status" -ne 0 ]; then
    echo "QEMU GATE FAIL: QEMU status $qemu_status"
    tail -n 100 "$all_log"
    exit 1
fi

if grep -Eq "$fatal" "$all_log"; then
    echo "QEMU GATE FAIL: fatal diagnostic found"
    grep -En "$fatal" "$all_log"
    exit 1
fi

grep -q '^QEMU_GATE_PASS:' "$runtime_log" || {
    echo "QEMU GATE FAIL: success marker absent"
    tail -n 100 "$all_log"
    exit 1
}

echo "QEMU GATE PASS: $label"
EOF

chmod 0755 "$RV2_WORK/scripts/run-qemu-gate.sh"
```

QEMU `virt` supplies a generated DTB, generic harts, PLIC, CLINT, UART and VirtIO-MMIO devices, and supports direct `-kernel` boot through its default OpenSBI firmware. [QEMU `virt` documentation](https://www.qemu.org/docs/master/system/riscv/virt.html) The VirtIO console syntax is documented in the [QEMU invocation manual](https://www.qemu.org/docs/master/system/qemu-manpage.html).

Run the control first:

```bash
export RV2_LOG_DIR="$RV2_WORK/logs"

"$RV2_WORK/scripts/run-qemu-gate.sh" \
    "$RV2_WORK/out/baseline/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/rootfs.cpio.gz" \
    baseline
```

Then M0:

```bash
"$RV2_WORK/scripts/run-qemu-gate.sh" \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/rootfs.cpio.gz" \
    m0
```

Optionally exercise kernel vector initialization too:

```bash
RV2_QEMU_CPU='rv64,v=true' \
    "$RV2_WORK/scripts/run-qemu-gate.sh" \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/rootfs.cpio.gz" \
    m0-vector
```

The strict boot parameters intentionally turn warnings and oopses into failures; `panic_on_warn=1` is explicitly intended for this behaviour. [Linux panic-on-warning documentation](https://docs.kernel.org/6.13/admin-guide/sysctl/kernel.html#panic-on-warn)

# 9. Prove that the gate really breaks

This negative control must fail because the guest insists on eight online CPUs:

```bash
if RV2_QEMU_SMP=4 \
    "$RV2_WORK/scripts/run-qemu-gate.sh" \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/rootfs.cpio.gz" \
    negative-4cpu; then

    echo "ERROR: gate passed with only four CPUs"
    exit 1
else
    echo "NEGATIVE CONTROL PASS: four-CPU boot was rejected"
fi
```

Also test the memory check:

```bash
if RV2_QEMU_MEM=1G \
    "$RV2_WORK/scripts/run-qemu-gate.sh" \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/rootfs.cpio.gz" \
    negative-1g; then

    echo "ERROR: gate passed with only 1 GiB"
    exit 1
else
    echo "NEGATIVE CONTROL PASS: 1-GiB boot was rejected"
fi
```

If these unexpectedly pass, the harness is not trustworthy.

# 10. Freeze the board candidate

```bash
make -C "$KERNEL_SRC" O="$RV2_WORK/out/m0" savedefconfig

install -m 0644 \
    "$RV2_WORK/out/m0/arch/riscv/boot/Image" \
    "$RV2_WORK/artifacts/Image"

install -m 0644 \
    "$RV2_WORK/out/m0/arch/riscv/boot/dts/ky/x1_orangepi-rv2.dtb" \
    "$RV2_WORK/artifacts/x1_orangepi-rv2.dtb"

install -m 0644 \
    "$RV2_WORK/out/m0/.config" \
    "$RV2_WORK/artifacts/rv2-m0.config"

install -m 0644 \
    "$RV2_WORK/out/m0/defconfig" \
    "$RV2_WORK/artifacts/rv2-m0.defconfig"

git -C "$KERNEL_SRC" rev-parse HEAD \
    >"$RV2_WORK/artifacts/SOURCE_COMMIT"

(
    cd "$RV2_WORK/artifacts"
    sha256sum \
        Image \
        x1_orangepi-rv2.dtb \
        rootfs.cpio.gz \
        rv2-m0.config \
        >SHA256SUMS
)
```

Only these exact hashed artifacts should proceed to the first board test. QEMU must use its generated DTB; the hardware must use `x1_orangepi-rv2.dtb`.

The board command line should change to:

```text
earlycon=sbi console=ttyS0,115200n8 rdinit=/init loglevel=8
```

# What this gate now catches

It should reject:

- A missing or dynamic `/init`.
- Wrong architecture or malformed kernel image.
- Failure before or during MMU, scheduler or userspace startup.
- Fewer than eight online harts.
- Broken CPU-affinity/syscall execution.
- Timer failure.
- Failure to initialize QEMU’s PLIC.
- Missing VirtIO-MMIO or `hvc0`.
- Insufficient 4-GiB memory handling.
- Kernel warnings, oopses, lockdep failures and selected VM/SLUB errors.
- Missing built-in RV2 UART, clock, reset, power-domain or DMA support.
- An incorrect board model, CPU count, console path or split-memory description.

It still cannot execute the RV2’s real CCU, reset, PXA UART, PLIC/CLINT addresses, split physical DRAM, pinmux or firmware handoff. Those depend on the real board and its boot firmware; Linux’s required firmware contract includes `a0=hartid`, `a1=DTB`, `satp=0`, memory reservations and kernel alignment. [Linux RISC-V boot requirements](https://docs.kernel.org/arch/riscv/boot.html)

Finally, this is a qualification configuration, not a performance-measurement configuration. Lockdep, VM debugging, SLUB poisoning and `initcall_debug` must be removed before measuring syscall latency, privilege transitions, IPC or memory footprint.
