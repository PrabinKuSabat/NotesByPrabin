> [!info] **rdcycle is privileged**  
> From Linux 6.6 kernel rdcycle is privileged and cannot be used directly in user space.  
> **PS:** For now there exists a sysctl to re-enable it. But it may eventually disappear.
>
> _Temporary:_
> ```
> sudo sysctl -w abi.riscv_user_access=1
> ```
> _Permanent:_
> ```
> sudo nano /etc/sysctl.d/99-riscv-rdcycle.conf
> abi.riscv_user_access=1
> sudo sysctl --system
> ```

> [!attention] Go through this from Linux dev
> [Implementing perf event mmap support in the SBI backend](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=cc4c07c89aada16229084eeb93895c95b7eabaa3)and [this](https://www.kernel.org/doc/html/v5.7/riscv/pmu.html
> )


> 


