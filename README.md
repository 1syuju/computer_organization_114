# computer_organization_114
build: scons EXTRAS=../NVMain build/X86/gem5.opt


## gem5
an open-source simulator used for computer system architecture research and education. It offers a flexible and modular platform for simulating a wide range of systems, from simple microcontrollers to complex multicore architectures. Key features include:

Modularity: Combine different components (CPUs, caches, memory) to simulate various architectures.
Support for Multiple ISAs: ARM, x86, SPARC, MIPS, RISC-V.
Detailed and High-Level Simulation: Cycle-level detailed simulations and faster functional simulations.
Full-System and SoC Simulation: Run entire operating systems and complex workloads.
Extensive Configuration: Customize CPU parameters, memory hierarchies, interconnects, and I/O devices.

## NVMain
a cycle-accurate memory simulator designed to model non-volatile memory (NVM) technologies. It is used for research and development in memory system architecture, particularly focusing on emerging NVM technologies such as phase-change memory (PCM), spin-transfer torque RAM (STT-RAM), and resistive RAM (ReRAM). NVMain can be integrated with other system simulators like GEM5 to provide a detailed simulation of memory behavior and performance in the context of a complete system.
