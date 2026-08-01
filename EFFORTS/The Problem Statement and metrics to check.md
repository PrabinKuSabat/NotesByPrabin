# Problem Statement

For a specific edge workload or analysis a general-purpose Linux kernel supplied for the edge platform comes with many subsystems, drivers, protocols, debugging facilities, filesystems, and services that may not be needed. These increases the static kernel size, runtime memory consumption, initialization work and potential execution noise.

This project aims to research, design, compile, boot and experimentally evaluate a functionally minimal RISC-V Linux Kernel for the Orange Pi RV2 using the `orangepi-xunlong/linux-orangepi` vendor kernel source.

The design phase will involve retaining a defined set of essential capabilities while removing unnecessary configuration options and subsystems.
