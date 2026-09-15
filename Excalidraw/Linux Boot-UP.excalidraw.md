---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠== You can decompress Drawing data with the command palette: 'Decompress current Excalidraw file'. For more info check in plugin settings under 'Saving'

# Excalidraw Data

## Text Elements
Major steps in Linux Boot Process ^5UAmpyuD

config ^xmgdwUeA

The actual truth after compilation is .config ^je9Th2Vq

contains defconfig + Kconfig defaults ^SuU2jKgl

select and imply details ^bp5uJ2hv

dependencies ^oMD3Zk2o

We have 3 different config files ^1sJ7yk66

configs/x1_defconfig ^vTjHKSsk

board/ky/x1/Kconfig ^UxNHuVeL

arch/riscv/Kconfig ^HJ5PQhWd

Generic Risc-V configuration choices ^HHYE9OyZ

Gives a understanding of the privilege-mode options ^qnvdOPgj

proper ^ZLA9qOkd

usb-boot ^3ljVObKG

linux-hanoff ^EHM2wyb6

spl-entry ^pZjicNyY

spl-board ^GKGFUNiy

spl-load ^AfA3fQP2

opensbi ^fKX5ap8b

Board ROM ^vgcfNNpL

spl entry ^0DX06Ia3

SPL is the small first-stage U-Boot program. Its job is to make enough hardware usable—especially DDR and UART—to load the next firmware image. It is not U-Boot proper, and it does not use U-Boot proper’s relocated stack or malloc area. ^ZJDnFfzG

relevant configuration ^EkzFZXaP

compiled spl artifact ^1yjOM4s9

boot-trace/src/u-boot-orangepi/spl/u-boot-spl
boot-trace/src/u-boot-orangepi/spl/u-boot-spl.bin ^QYcN2QVE

CONFIG_SPL=y CONFIG_SPL_RISCV_MMODE=y 
CONFIG_SPL_TEXT_BASE=0xC0801000 
CONFIG_SPL_STACK=0xC0840000 
CONFIG_SPL_BSS_START_ADDR=0xC0837000 
CONFIG_SPL_SEPARATE_BSS=y 
CONFIG_SPL_LOAD_FIT=y 
CONFIG_SPL_LOAD_FIT_ADDRESS=0x11000000 
CONFIG_SPL_OPENSBI_LOAD_ADDR=0x0 
CONFIG_SPL_SYS_MALLOC_F_LEN=0x4000 ^03eUIDvF

The ELF is useful for symbols and sections; the .bin is the raw executable payload used by the storage image. ^J7RiYAZA

cd /home/prabin/MinimalKernelBuilding/rv2/boot-trace/src/u-boot-orangepi 
make O=/chosen/output riscv_defconfig 
make O=/chosen/output CROSS_COMPILE=riscv64-linux-gnu- -j ^hg1S4bTz

commands ^plQpXsnh

The first executed symbol is _start in arch/riscv/cpu/start.S line 45. ^zbVF3lQ7

.section .text
.globl _start
_start:
#if CONFIG_IS_ENABLED(RISCV_MMODE)
#ifdef CONFIG_RISCV_ISA_DOUBLE_FLOAT
        csrr        a0, CSR_MSTATUS
        li                t0, 3<<13
        xor                a0, a0, t0
        csrw        CSR_MSTATUS, a0
#endif
        csrr        a0, CSR_MHARTID
#endif ^6CUpdiPo

Because CONFIG_SPL_RISCV_MMODE=y, the SPL reads mhartid into a0. The ROM or earlier firmware is expected to have supplied the hart ID and possibly a firmware DTB pointer in a1. ^HThwsMDw

/*
         * Save hart id and dtb pointer. The thread pointer register is not
         * modified by C code. It is used by secondary_hart_loop.
         */
        mv        tp, a0
        mv        s1, a1

        /*
         * Set the global data pointer to a known value in case we get a very
         * early trap. The global data pointer will be set its actual value only
         * after it has been initialized.
         */
        mv        gp, zero

        /*
         * Set the trap handler. This must happen after initializing gp because
         * the handler may use it.
         */
        la        t0, trap_entry
        csrw        MODE_PREFIX(tvec), t0

        /*
         * Mask all interrupts. Interrupts are disabled globally (in m/sstatus)
         * for U-Boot, but we will need to read m/sip to determine if we get an
         * IPI
         */
        csrw        MODE_PREFIX(ie), zero ^Et5jLF6I

The SPL preserves those values:
- tp receives the hart ID.
- s1 receives the incoming a1 value.
- gp is cleared because C code must not use an uninitialized global pointer.
- tvec receives an early trap entry.
- Interrupt-enable bits are cleared. ^zaTm8Nnc

```text
/*
 * Set stackpointer in internal/ex RAM to call board_init_f
 */
call_board_init_f:
        li        t0, -16
#if defined(CONFIG_SPL_BUILD) && defined(CONFIG_SPL_STACK)
        li        t1, CONFIG_SPL_STACK
#else
        li        t1, SYS_INIT_SP_ADDR
#endif
        and        sp, t1, t0                /* force 16 byte alignment */

``` ^rYhCpOcc

The and aligns the stack down to a 16-byte boundary. Since the value is already aligned, the initial stack pointer remains 0xC0840000. ^WUibCcub

With this build: CONFIG_SPL_STACK = 0xC0840000 ^KLxUhPw6

Lines 86-106 are skipped because SMP is not set in defconfig ^OsjDmiDI

call_board_init_f_0:
        mv        a0, sp
        jal        board_init_f_alloc_reserve

        /*
         * Save global data pointer for later. We don't set it here because it
         * is not initialized yet.
         */
        mv        s0, a0

        /* setup stack */
#if CONFIG_IS_ENABLED(SMP)
        /* tp: hart id */
        slli        t0, tp, CONFIG_STACK_SIZE_SHIFT
        sub        sp, a0, t0
#else
        mv        sp, a0
#endif

call_harts_early_init:
        jal        harts_early_init ^To0y28yx

board_init_f_alloc_reserve() reserves space for early global data and the early malloc area. Its return value becomes the usable stack/global-data base. This is why the actual C stack can be below the initial top-of-stack value. ^dHaKQpFI

This is an important distinction:
- CONFIG_SPL_STACK is the initial stack ceiling.
- board_init_f_alloc_reserve() adjusts the stack and reserves early data.
- The later SPL_BSS_START_ADDR region is separate BSS storage.
- None of these is U-Boot proper’s stack. ^p0WoSaM1

void board_init_f(ulong dummy)
{
        int ret;

        // fix boot mode after boot rom
        fix_boot_mode();

        // setup pinctrl
        board_pinctrl_setup();

        ret = spl_early_init();
        if (ret)
                panic("spl_early_init() failed: %d\n", ret);

        riscv_cpu_setup(NULL, NULL);

        preloader_console_init();
        pr_debug("boot_mode: %x\n", get_boot_mode());

        ret = spl_board_init_f();
        if (ret)
                panic("spl_board_init_f() failed: %d\n", ret);
} ^zkiFMKNY

fix_boot_mode() interprets the boot mode selected by the ROM or board straps. board_pinctrl_setup() prepares pin multiplexing. spl_early_init() initializes the common SPL framework. riscv_cpu_setup() configures CPU-specific state. Only after this does preloader_console_init() make SPL UART output possible. ^pyN2EPsp

int spl_board_init_f(void)
{
        int ret;
        struct udevice *dev;
        bool flag;
        // uint64_t chipid = 0, mac_addr = 0;

#if CONFIG_IS_ENABLED(SYS_I2C_LEGACY)
        /* init i2c */
        i2c_init_board();
#endif

#if CONFIG_IS_ENABLED(KY_POWER)
        board_pmic_init();
#endif

        raise_cpu_frequency();
#if CONFIG_IS_ENABLED(KY_X1_EFUSE)
        // load_chipid_from_efuse(&chipid);
#endif
        // get_mac_address(&mac_addr);

        update_ddr_info();

        // restore prevous saved ddr training info data
        // flag = restore_ddr_training_info(chipid, mac_addr);
        flag = true;
        if (!flag) {
                // flush data and stack
                flush_dcache_range(CONFIG_SPL_BSS_START_ADDR, CONFIG_SPL_STACK);
                flush_dcache_range(round_down((size_t)__data_start, CONFIG_RISCV_CBOM_BLOCK_SIZE),
                         round_up((size_t)__data_end, CONFIG_RISCV_CBOM_BLOCK_SIZE));
                icache_disable();
                dcache_disable();
                invalidate_dcache_range(CONFIG_SPL_BSS_START_ADDR, CONFIG_SPL_STACK);
        }

        /* DDR init */
        ret = uclass_get_device(UCLASS_RAM, 0, &dev);
        if (ret) {
                pr_err("DRAM init failed: %d\n", ret);
                return ret;
        }

        if (!flag) {
                icache_enable();
                dcache_enable();
        }

        // update_ddr_training_info(chipid, mac_addr);
        update_ddr_config_info(ddr_cs_num);
        timer_init();

        return 0;
} ^aRVDRSV3

The most important operation is: uclass_get_device(UCLASS_RAM, 0, &dev); ^8c5aAa1t

That triggers the configured RAM driver and DDR initialization. If it fails, SPL prints DRAM init failed and stops. Successful later execution from DDR therefore proves this operation reached a usable result, although it does not reveal every DDR-training register write. ^L7FVL4st

```text
#ifdef CONFIG_SPL_BUILD
spl_clear_bss:
        la        t0, __bss_start
        la        t1, __bss_end
        beq        t0, t1, spl_stack_gd_setup

spl_clear_bss_loop:
        SREG        zero, 0(t0)
        addi        t0, t0, REGBYTES
        blt        t0, t1, spl_clear_bss_loop

spl_stack_gd_setup:
        jal        spl_relocate_stack_gd

        /* skip setup if we did not relocate */
        beqz        a0, spl_call_board_init_r
        mv        s0, a0

        /* setup stack on main hart */

/*Some #if CONFIG_IS_ENABLED(SMP)

        /* set new global data pointer on main hart */
1:        mv        gp, s0

spl_call_board_init_r:
        mv        a0, zero
        mv        a1, zero
        jal        board_init_r
#endif

``` ^z2gtsxYh

#if CONFIG_IS_ENABLED(SMP)
        /* tp: hart id */
        slli        t0, tp, CONFIG_STACK_SIZE_SHIFT
        sub        sp, s0, t0
#else
        mv        sp, s0
#endif

#if CONFIG_IS_ENABLED(SMP)
        /* set new stack and global data pointer on secondary harts */
spl_secondary_hart_stack_gd_setup:
        la        a0, secondary_hart_relocate
        mv        a1, s0
        mv        a2, s0
        mv        a3, zero
        jal        smp_call_function

        /* hang if relocation of secondary harts has failed */
        beqz        a0, 1f
        mv        a1, a0
        la        a0, secondary_harts_relocation_error
        jal        printf
        jal        hang
#endif  ^OFGzlvx7

%%
## Drawing
```compressed-json
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

M62qjOAKHXM6AcA6fp5BzIP6lEDYUQosnhAgAA==
```
%%