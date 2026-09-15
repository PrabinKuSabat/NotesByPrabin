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

explains why particular values were chosen ^GHUARdG2

Contains X1-specific defaults, such as text addresses ^rVE7GuS3

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

[[x1_defconfig_architecture.excaidraw#^frame=Architecture defconfig|Architecture, board, and privilege]] ^yuh0anpb

[[Excalidraw/x1_defconfig_architecture.excaidraw.md#^2QXLowC0|Memory and Placement]] ^j2JLmRoA

Board ROM ^vgcfNNpL

spl entry ^0DX06Ia3

SPL is the small first-stage U-Boot program. Its job is to make enough hardware usable—especially DDR and UART—to load the next firmware image. It is not U-Boot proper, and it does not use U-Boot proper’s relocated stack or malloc area. ^ZJDnFfzG

relevant configuration ^EkzFZXaP

compiled spl artifact ^1yjOM4s9

boot-trace/src/u-boot-orangepi/spl/u-boot-spl 
boot-trace/src/u-boot-orangepi/spl/u-boot-spl.bin ^QYcN2QVE

CONFIG_SPL=y CONFIG_SPL_RISCV_MMODE=y CONFIG_SPL_TEXT_BASE=0xC0801000 CONFIG_SPL_STACK=0xC0840000 CONFIG_SPL_BSS_START_ADDR=0xC0837000 CONFIG_SPL_SEPARATE_BSS=y CONFIG_SPL_LOAD_FIT=y CONFIG_SPL_LOAD_FIT_ADDRESS=0x11000000 CONFIG_SPL_OPENSBI_LOAD_ADDR=0x0 CONFIG_SPL_SYS_MALLOC_F_LEN=0x4000 ^03eUIDvF

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
 ^rYhCpOcc

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
 ^z2gtsxYh

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
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=
```
%%