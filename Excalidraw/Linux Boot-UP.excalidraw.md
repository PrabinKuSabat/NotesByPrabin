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
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
```
%%