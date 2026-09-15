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
```
%%