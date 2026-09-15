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