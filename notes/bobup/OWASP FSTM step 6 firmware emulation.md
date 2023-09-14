The motivation for firmware emulation of IoT devices during hardware pentesting analysis is based on two principles: 
1. speed up testing and attacks on the device and ← Includes Easy to build (?) 
2. remove the need of the physical device. ← Important

Most popular are QEMU and Unicorn, but there are many others.

important informations that we should know is
1. Architecture of the system
2. Endianness
3. Memory region

At the emulation level, We need to replicate Its architecture and the peripherals of the device.

Firmware emulation ways

1. Emulation in user space for an executable or a service : Easy to emulate executable system with QEMU, Qiling
2. Emulation in user space with simulated files : Sometimes, Executables require access to devices or files with HW-dependent behaviors ⇒ We can replicate these devices by using CUSE or FUSE
3. System emulation without bootloader: Sometimes we need to emulate the execution of a kernel so that the processes or services work properly. In this case some tools can load the kernel in memory and run it directly without the need of a bootloader. This option may require us to develop software to emulate some physical devices that the emulated kernel needs to boot. This can be a relatively simple or very complicated task depending on the device documentation available.
4. Full system emulation


Emulation tools

1. [[QEMU]] :
    
    it provides a complete virtual machine model to run the emulated system.
    
    it can also work from supervisor such as KVM, Xen, Hax, or Hypervisor ⇒ Basically Qemu is running as type1 hyperviser, But as running with KVM, it can run as type1 hyperviser
    
    qemu-arch
    
2. [[Unicorn]] : 
	is an alternative to QEMU. But to perform an emulation, advanced knowledge of the platform to be emulated is required and a large investment of time to write the code that allows us to launch its executions (on numerous occasions, peripherals and other components must be emulated at a low level for the system to work properly).
    
3. [[Renode]] :
    
    <U>Qemu-based</U> tool for emulating firmware.
    
    Its functionalities include … , multiple types of devices such as sensors, displays and other system inputs and outputs.
    
    Unlike QEMU, Renode has been primarily designed for emulation of IoT and embedded devices with real-time operating systems, although it is also capable of emulating more powerful systems, such as Linux.
    
    MMIO is needed. it can be obtained from the manufacturer’s manuals of the IoT device or from DTB(Device Tree Blob) data blcks that are usually present in firmware.
    
    The way to configure Renode for emulating is minimal set of devices and run the system until failures occur, then Investigate the failures and complete the list of devices.
    
4. [[Qiling]] : 
	to emulate any type of executables. supporting multiple architectures, OS, executable formats
    
    framework written in Python
    
    <U>based on Unicorn</U> emulation engine.
    
    For emulating with Qiling, The file system of the extracted firmware with executable to be emulated is required.
    
    AFL++ Fuzzer can be executed with it.
    
5. [[Firmadyne]] :
    
    Automate and simplify the work of emulating a firmware image
    
    Its automatic process includes File system extraction, firmware architecture identification and the creation of an executable virtual machine specific to the analyzed firmware.
    
    However installation of this suite can be more complicated.
    
    In general, emulation with Firmadyne can be much easier than with the other tools.

6. [[EMUX]] :
	is a collection of scripts, executables, kernels, and file systems that together with <U>QEMU</U>
	It tries to emulate ARM, MIPS IoT devices.
	The project tries to be as like an IoT virtual machine as possible.
	
	If EMUX does not have support for the device we are emulating,
	it has a section that explains how to create our own devices and templates to make the implementation work easier.

Firmware emulation example with QEMU : I'll do it myself soon.

[[IoT Firmware Emulating]]