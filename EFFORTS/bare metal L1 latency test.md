> [!info] **rdcycle is privileged**
> From Linux 6.6 kernel rdcycle is privileged and cannot be used directly in user space.
> **PS:** For now there exists a sysctl to re-enable it. But it may eventually disappear. 
> 
> *Temporary:*
> ```
> sudo sysctl -w abi.riscv_user_access=1
> ```
> *Permanent:*
> ```
> sudo nano /etc/sysctl.d/99-riscv-rdcycle.conf
> abi.riscv_user_access=1
> sudo sysctl --system
> ```

