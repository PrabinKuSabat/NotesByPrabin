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