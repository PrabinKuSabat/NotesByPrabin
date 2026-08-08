# 12. FocalTech touchscreen failure

The baseline build exposed:

```
drivers/input/touchscreen/focaltech_touch/…
```

which failed because vendor firmware files were absent:

```
include/firmware/fw_sample.bin
```

and:

```
FT8205P_Pramboot_V1.0_20231226.bin
```

Inspection of its Kconfig showed:

```
config TOUCHSCREEN_FTS
    tristate "Focaltech Touchscreen"
    default m
```

This was an important finding.

The vendor driver defaults to `m`, meaning it can become enabled without being explicitly requested in our fragment.

For our minimal kernel, the correct approach is not to locate unnecessary touchscreen firmware. It is to remove the driver:

```
# CONFIG_TOUCHSCREEN_FTS is not set
```

This also reinforces why the configuration gate should reject unexpected modules.

---

# 13. PowerVR Rogue GPU failure

Another compilation failure came from:

```
drivers/gpu/drm/img-rogue/
```

with errors involving:

```
pci_request_region()
pci_release_region()
```

Inspection showed:

```
CONFIG_POWERVR_ROGUE=y
```

while M0 deliberately disabled PCI.

This created an invalid vendor-driver dependency:

```
PowerVR Rogue
      │
      └── assumes PCI interfaces
                 │
                 X
           CONFIG_PCI=n
```

For M0, GPU support is not part of the boot contract.

Therefore the correct solution was:

```
# CONFIG_POWERVR_ROGUE is not set
```

rather than enabling PCI just to satisfy an unnecessary GPU driver.

This establishes a useful stripping rule:

> When an unnecessary peripheral driver fails because a subsystem has been intentionally removed, remove the peripheral driver rather than restoring the subsystem.

---

# 14. HUSB239 Type-C failure

The next invalid dependency appeared in:

```
drivers/usb/typec/husb239.c
```

with unresolved:

```
ky_headphone_notifier_call_chain
```

Configuration inspection showed:

```
CONFIG_TYPEC=y
CONFIG_TYPEC_HUSB239=y
```

The Kconfig entry is:

```
config TYPEC_HUSB239
    tristate "Hynetek HUSB239 Type-C DRP Port controller driver"
    depends on I2C
```

For the M0 boot contract, USB Type-C controller functionality is unnecessary.

Therefore we removed:

```
# CONFIG_TYPEC is not set
# CONFIG_TYPEC_HUSB239 is not set
```

rather than pulling in audio/headphone infrastructure simply to satisfy the driver.

---

# 15. SGM4154x charger dependency failure

The linker then reported unresolved symbols from:

```
drivers/power/supply/sgm4154x_charger.o
```

including:

```
regulator_unregister
rdev_get_drvdata
devm_regulator_register
```

The driver is controlled by:

```
CONFIG_CHARGER_SGM415XX
```

M0 had removed the regulator framework while the charger remained enabled.

Conceptually:

```
SGM415XX charger
       │
       └── regulator framework
                    │
                    X
             CONFIG_REGULATOR=n
```

Because battery charging is not required to prove kernel boot, the chosen solution is:

```
# CONFIG_CHARGER_SGM415XX is not set
```

rather than:

```
CONFIG_REGULATOR=y
```

This is another instance of the same minimality principle.

---

# 16. Ky X1 power-management dependency

The most interesting architecture-specific failure so far was:

```
undefined reference to `__cpu_suspend_enter'
undefined reference to `__cpu_resume_enter'
```

from:

```
drivers/soc/ky/pm/platform_pm.c
```

The Ky platform PM code requires RISC-V CPU suspend/resume implementation.

The corresponding architecture objects are tied to:

```
CONFIG_CPU_PM
```

However, `CPU_PM` is a hidden Kconfig symbol. It should not simply be forced blindly from the fragment.

For this tree, RISC-V selects CPU PM when the relevant higher-level functionality is enabled.

The appropriate M0 choice is to retain:

```
CONFIG_CPU_IDLE=y
```

rather than enabling hibernation.

The intended PM configuration therefore becomes approximately:

```
CONFIG_PM=y
CONFIG_SUSPEND=y
CONFIG_CPU_IDLE=y
CONFIG_CPU_PM=y
CONFIG_KY_PM_DOMAINS=y
```

with `CPU_PM` expected to emerge in the resolved `.config`.

This must be verified after `olddefconfig`.

---

# 17. Current important M0 removals

Based on the failures encountered so far, the M0 fragment needs explicit exclusions along these lines:

```
# CONFIG_TOUCHSCREEN_FTS is not set

# CONFIG_POWERVR_ROGUE is not set

# CONFIG_TYPEC is not set
# CONFIG_TYPEC_HUSB239 is not set

# CONFIG_REGULATOR is not set
# CONFIG_CHARGER_SGM415XX is not set
```

and the PM path retains:

```
CONFIG_PM=y
CONFIG_SUSPEND=y
CONFIG_CPU_IDLE=y
CONFIG_KY_PM_DOMAINS=y
```

After Kconfig resolution we expect:

```
CONFIG_CPU_PM=y
```

as well.

---

# 18. Current M0 design philosophy

We should **not** attempt to preserve every feature from `x1_defconfig`.

The current profile is a qualification kernel whose first objective is:

> Reach a reliable Linux + initramfs boot on the Orange Pi RV2 while retaining only the infrastructure required for that boot contract.

Therefore hardware such as:

- touchscreen,
- GPU,
- battery charger,
- Type-C functionality,
- unnecessary networking,
- PCI devices,
- MTD,
- optional peripheral stacks

does not need to survive M0 unless evidence shows it is required for boot.

---

# 19. Why these failures are actually useful

The repeated compilation/link failures are revealing weaknesses in the vendor kernel's Kconfig dependency graph.

For example:

```
POWERVR_ROGUE=y
PCI=n
```

was permitted far enough to reach compilation.

Similarly:

```
CHARGER_SGM415XX=y
REGULATOR=n
```

reached the linker.

And the Ky PM code could be compiled without the RISC-V suspend implementation it references.

This means we cannot assume:

> "`olddefconfig` succeeded, therefore the configuration is internally valid."

For this vendor tree, the **actual compile and link are part of configuration validation**.

That is an important project finding in itself.

---

# 20. The qualification philosophy emerging from this work

We are effectively constructing three layers of assurance:

```
             Fragment
                │
                ▼
        ┌───────────────┐
        │ Kconfig gate  │
        └───────┬───────┘
                │
                ▼
       resolved .config
                │
                ▼
        ┌───────────────┐
        │ Compile/link  │
        │     gate      │
        └───────┬───────┘
                │
                ▼
              Image
                │
                ▼
        QEMU smoke test
                │
                ▼
        Hardware boot test
```

Each layer proves something different.

**Kconfig gate:** proves that the intended configuration survived dependency resolution.

**Compile/link:** proves that the selected vendor source subset is internally buildable.

**QEMU:** will prove architecture-level kernel initialization and initramfs/userspace functionality.

**Orange Pi RV2:** will finally prove the real SoC/board-specific boot path, firmware interaction, interrupts, clocks, UART, memory map, device tree and required hardware drivers.

---

# 21. A particularly important lesson for the project

The objective should **not** be:

> Disable as many `CONFIG_*` symbols as possible.

Instead it should be:

> Determine the smallest configuration that satisfies a precisely defined boot and measurement contract.

That distinction matters academically.

For example, `CPU_IDLE` might initially look removable from a minimal kernel. But removing it breaks the Ky platform PM implementation because of the vendor tree's dependency structure.

Therefore:

```
minimal ≠ blindly smallest
```

Instead:

```
Minimal Kernel
      =
smallest validated configuration
that satisfies the defined functional contract
```

That is a much stronger definition for the project.

# 10 Check no symbol is defined twice

```bash
# checks if you have the same symbol defined more than once
grep -E '^CONFIG_|^# CONFIG_.* is not set' \
    "$RV2_WORK/configs/m0.fragment" |
    sed -E 's/^# //; s/(=| is not set).*//' |
    sort |
    uniq -d
```

---

| Finding                     | Cause                                           | M0 decision                  |
| --------------------------- | ----------------------------------------------- | ---------------------------- |
| FocalTech firmware missing  | unnecessary touchscreen enabled                 | disable `TOUCHSCREEN_FTS`    |
| PowerVR compile failure     | GPU enabled while PCI removed                   | disable `POWERVR_ROGUE`      |
| HUSB239 unresolved symbol   | unnecessary Type-C stack                        | disable Type-C/HUSB239       |
| SGM415xx unresolved symbols | charger retained while regulator removed        | disable charger              |
| Ky PM unresolved symbols    | platform PM requires CPU suspend implementation | retain `CPU_IDLE` → `CPU_PM` |
