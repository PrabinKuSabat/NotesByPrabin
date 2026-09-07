This section defines a **repeatable kernel build function** and uses it to produce two kernel profiles:

- `baseline` = vendor `x1_defconfig` + qualification/debug requirements
- `m0` = vendor `x1_defconfig` + minimal-pruning fragment + qualification/debug requirements

That distinction is important because the baseline acts as a control. If baseline fails, the problem is probably not your pruning. If baseline passes and `m0` fails, the regression is likely caused by the minimal configuration delta.

The first four variables make the kernel build more reproducible:

```bash
export SOURCE_DATE_EPOCH=0
export KBUILD_BUILD_TIMESTAMP="1970-01-01 00:00:00 UTC"
export KBUILD_BUILD_USER=rv2
export KBUILD_BUILD_HOST=qualification
```

`SOURCE_DATE_EPOCH=0` tells build tooling to use a fixed reference time instead of the current clock where supported. `KBUILD_BUILD_TIMESTAMP` fixes the timestamp embedded into the kernel build metadata. `KBUILD_BUILD_USER` and `KBUILD_BUILD_HOST` stop the kernel from embedding your actual username and machine hostname. Without these settings, two otherwise identical builds could differ simply because they were compiled on different machines or at different times.

So instead of kernel metadata varying like:

```text
Linux version … prabin@laptop … Sat Aug 8 10:30:00 2026
```

you get a stable build identity based on:

```text
rv2@qualification
1970-01-01 00:00:00 UTC
```

The function starts here:

```bash
build_profile()
{
    profile="$1"
    shift
    output="$RV2_WORK/out/$profile"
```

`profile="$1"` takes the first argument passed to the function and uses it as the profile name.

For:

```bash
build_profile baseline \
    "$RV2_WORK/configs/qualification.fragment"
```

this means:

```text
profile = baseline
```

Then:

```bash
shift
```

removes that first argument from the argument list. After `shift`, `$@` contains only the configuration fragments that should be merged.

So in the baseline call:

```text
before shift:
$1 = baseline
$2 = qualification.fragment

after shift:
$@ = qualification.fragment
```

For `m0`:

```bash
build_profile m0 \
    "$RV2_WORK/configs/m0.fragment" \
    "$RV2_WORK/configs/qualification.fragment"
```

after `shift`:

```text
$@ =
    m0.fragment
    qualification.fragment
```

This line:

```bash
output="$RV2_WORK/out/$profile"
```

creates a separate output directory for each build:

```text
$RV2_WORK/out/baseline/
$RV2_WORK/out/m0/
```

That is a good kernel-development practice because the source tree remains clean and the two configurations cannot overwrite one another.

Next:

```bash
mkdir -p "$output"
```

creates the appropriate output directory if it does not already exist.

The most important part is:

```bash
"$KERNEL_SRC/scripts/kconfig/merge_config.sh" \
    -m -O "$output" \
    "$KERNEL_SRC/arch/riscv/configs/x1_defconfig" \
    "$@"
```

`merge_config.sh` is a Linux kernel helper for combining multiple Kconfig fragments.

The base is always:

```bash
arch/riscv/configs/x1_defconfig
```

which is the Orange Pi vendor configuration. Then additional fragments are applied on top.

Conceptually:

```text
x1_defconfig
      +
qualification.fragment
      =
baseline configuration
```

and:

```text
x1_defconfig
      +
m0.fragment
      +
qualification.fragment
      =
m0 configuration
```

The order matters. Later fragments can override earlier values.

For example, if `x1_defconfig` contains:

```text
CONFIG_NET=y
CONFIG_USB=y
```

and `m0.fragment` says:

```text
# CONFIG_NET is not set
# CONFIG_USB is not set
```

then the merged `m0` configuration requests those features disabled.

The options:

```bash
-m
```

tell `merge_config.sh` to merge the fragments but not itself run the full configuration-generation step.

```bash
-O "$output"
```

tells it to place the generated configuration into the out-of-tree build directory rather than modifying the kernel source directory.

So you get:

```text
out/baseline/.config
out/m0/.config
```

rather than one shared `.config`.

Next:

```bash
KCONFIG_WARN_UNKNOWN_SYMBOLS=1 KCONFIG_WERROR=1 \
    make -C "$KERNEL_SRC" O="$output" olddefconfig
```

This resolves the merged configuration through the kernel's actual dependency system.

This step is necessary because simply writing:

```text
CONFIG_SOMETHING=y
```

into a fragment does **not guarantee** that Kconfig will allow it.

For example, a driver could require:

```text
depends on SERIAL
depends on SOC_KY
```

If one dependency is missing, Kconfig may turn the requested option back off.

`olddefconfig` takes the merged `.config`, resolves dependencies, and assigns defaults to any newly encountered symbols without opening an interactive menu.

This is therefore the step that converts:

```text
requested configuration
```

into:

```text
actual valid configuration
```

The two environment variables make this stricter:

```bash
KCONFIG_WARN_UNKNOWN_SYMBOLS=1
```

asks Kconfig to warn if a configuration mentions symbols that the kernel tree does not recognize.

For example, a typo such as:

```text
CONFIG_SERIAL_PXAA=y
```

should not pass unnoticed.

And:

```bash
KCONFIG_WERROR=1
```

turns relevant Kconfig warnings into errors instead of allowing the build to continue.

That supports your project's philosophy that the pre-hardware qualification process should **fail early rather than silently accept suspicious configuration**.

Then:

```bash
make -C "$KERNEL_SRC" O="$output" \
    -j"$(nproc)" Image
```

builds the RISC-V Linux kernel image.

`-C "$KERNEL_SRC"` means:

```text
change into the kernel source directory before running make
```

without requiring your shell to physically `cd` there.

`O="$output"` means all generated files go into the profile-specific output directory.

So the final kernel images become approximately:

```text
out/baseline/arch/riscv/boot/Image
out/m0/arch/riscv/boot/Image
```

`-j"$(nproc)"` tells `make` to use as many parallel jobs as there are logical CPUs on the build host.

Finally, the function closes:

```bash
}
```

and is invoked twice.

The first invocation:

```bash
build_profile baseline \
    "$RV2_WORK/configs/qualification.fragment"
```

builds:

```text
vendor x1_defconfig
        +
qualification/debug/QEMU support
```

The purpose is not minimality. Its purpose is to answer:

> Can the vendor-derived kernel plus our qualification infrastructure boot correctly?

Then:

```bash
build_profile m0 \
    "$RV2_WORK/configs/m0.fragment" \
    "$RV2_WORK/configs/qualification.fragment"
```

builds:

```text
vendor x1_defconfig
        +
minimal pruning
        +
qualification/debug support
```

This is the actual first minimal profile.

So experimentally:

```text
baseline PASS
m0 PASS
    → pruning has not broken tested functionality

baseline PASS
m0 FAIL
    → investigate m0.fragment

baseline FAIL
m0 FAIL
    → likely common configuration/QEMU/kernel problem
      rather than minimal pruning
```

That control-vs-treatment structure is one of the strongest parts of the procedure.

The last command:

```bash
make -C "$KERNEL_SRC" O="$RV2_WORK/out/m0" \
    -j"$(nproc)" dtbs
```

builds the Device Tree Blobs for the `m0` configuration.

For your board, the important artifact is:

```text
out/m0/arch/riscv/boot/dts/ky/x1_orangepi-rv2.dtb
```

This DTB describes the **real Orange Pi RV2 hardware**: CPUs, memory layout, UART, clock controller, reset controller, interrupt controller, DMA devices, and so on.

It is intentionally built separately because there are two distinct test environments:

```text
QEMU:
    Image
    +
    QEMU-generated virt DTB

Real RV2:
    Image
    +
    x1_orangepi-rv2.dtb
```

You should **not** pass the Orange Pi RV2 DTB to QEMU `virt`, because QEMU does not emulate the actual RV2 SoC peripherals described by that DTB. Instead, the RV2 DTB is compiled and audited separately to verify that the real-board hardware description is compatible with the drivers retained in the minimal kernel.

In compact form, this entire section does:

```text
                           x1_defconfig
                              │
              ┌───────────────┴───────────────┐
              │                               │
     qualification.fragment           m0.fragment
              │                               │
              │                     qualification.fragment
              │                               │
              ▼                               ▼
         baseline .config                 m0 .config
              │                               │
          olddefconfig                    olddefconfig
              │                               │
              ▼                               ▼
       baseline/Image                    m0/Image
                                              │
                                              └── dtbs
                                                   │
                                                   ▼
                                      x1_orangepi-rv2.dtb
```

One small syntax point: when you actually enter this in Bash, use names like `SOURCE_DATE_EPOCH`, `build_profile`, `RV2_WORK`, and `KERNEL_SRC`. The backslashes before underscores in your message are Markdown escaping and should **not** be present in the shell.
