---
dg-publish: true
---

# NVIDIA Development Tools Solutions - ERR_NVGPUCTRPERM: Permission issue with Performance Counters

_Short URL: <https://developer.nvidia.com/ERR_NVGPUCTRPERM_>

> [!info] Content taken from 👆

---

## Overview

You may encounter the following error message when using NVIDIA tools:  
   **_ERR_NVGPUCTRPERM The user running <tool_name/application_name> does not have permission to access NVIDIA GPU Performance Counters on the target device._**

If you are receiving this error, then

1. You are using a tool that accesses the GPU Performance Counters. These counters are a developer-specific feature that provides low-level access to GPU hardware, and
2. Your system administrator or a recent NVIDIA driver installation has disabled access to GPU Performance Counters for regular users due to [Security Notice: NVIDIA Response to “Rendered Insecure: GPU Side Channel Attacks are Practical” - November 2018](https://nvidia.custhelp.com/app/answers/detail/a_id/4738). Your tool is affected by this restriction when using driver versions 419.17+ (Windows) or 418.43+ (Linux).

To avoid this error, [run the tool or application with elevated privileges](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters#ElevPrivsTag) or [enable access for all users](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters#AllUsersTag). For problems following these instructions, see the [troubleshooting guide](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters#TrblShootTag).

---

## Run with elevated privileges

Run the tool or application being profiled with administrative privileges on the target device:

- **Windows**: Launch the tool by right clicking on the tool and selecting “Run as administrator” or running the full command from an Administrator command prompt.
- **Mac OS**: Launch the tool with `sudo` or as an administrative user.
- **Linux Desktop**: Launch the tool with sudo or as a user with the CAP_SYS_ADMIN capability set. When [profiling within a container](https://developer.nvidia.com/blog/using-nsight-compute-in-containers/), access must be [enabled on the host](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters#AllUsersTag), or the container must be started with the appropriate permissions by passing `--cap-add=SYS_ADMIN` as an admin user.
- **DRIVE, Tegra and QNX**: Launch the tool with `sudo` or as the root user.
- See also [tool specific](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters#SolnToolsTag) information for further details.

---

### Linux Desktop

GPU Performance Counter control requires Linux display driver 418.43 or later. Also see the "Restricting access to GPU Performance Counters" section of the README.txt in the Linux driver.

#### Enable access permanently

- To allow access for any user, create a file with the .conf extension containing `options nvidia NVreg_RestrictProfilingToAdminUsers=0` in /etc/modprobe.d.
- To restrict access to admin users (CAP_SYS_ADMIN capability set), create a file with the .conf extension containing `options nvidia NVreg_RestrictProfilingToAdminUsers=1` in /etc/modprobe.d.

####       Notes

- A reboot may be required for the change to take effect.
- On some systems (or when using a package manager to install), it may be necessary to rebuild the initrd after writing a configuration file to /etc/modprobe.d.
- For RedHat-based distributions, rebuild the initrd with `dracut -–regenerate-all -f`
- For Debian-based distributions, rebuild the initrd with `update-initramfs -u -k all`
