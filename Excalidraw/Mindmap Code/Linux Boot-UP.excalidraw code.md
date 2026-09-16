# Linux Boot-UP.excalidraw code


## asm code

```asm
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
#endif 
```
^mindmap-code-q0gLhVXM


## asm code

```asm
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

```
^mindmap-code-fnxqBgm5


## asm code

```asm
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
        jal        harts_early_init
```
^mindmap-code-rk39gjkl
