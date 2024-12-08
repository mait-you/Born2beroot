
# Born2beroot

## What you need to know :

### What’s a Operating System
**Operating System (OS)** is system software that manages a computer’s hardware and software resources. It provides a user-friendly interface and acts as a bridge between the user and the hardware, enabling applications to run efficiently.

#### is Linux Operating System
**Yes**, Linux is an Operating System (OS), but more accurately, it refers to the **kernel**, which is the core part of the OS

### What’s a The Linux kernel
**The Linux kernel** manages hardware resources and acts as the foundation for Linux distributions.

### What's a virtual machine 
**Virtual Machine (VM)** is a software capable of installing an Operating System (**OS**) within itself, making the OS think that it is hosted on a real computer. With virtual machines we can create virtual devices that will behave in the same way as physical devices, using their own CPU, memory, network interface and storage. This is possible because the virtual machine is hosted on a physical device, which is the one that provides the hardware resources to the VM.

### What’s a Hypervisor
**Hypervisor** is software that creates and manages virtual machines (VMs) on a physical device. It enables multiple operating systems to run simultaneously by allocating separate hardware resources to each VM.
- **Hosted Hypervisor**: Runs on top of an existing OS (e.g., Windows or Linux). Typically used for personal computers.
- **Bare-Metal Hypervisor**: Runs directly on the physical hardware without requiring an underlying OS. Common in servers and data centers.

### What is LVM
**LVM (Logical Volume Manager)** is a flexible disk management system that allows dynamic partitioning and resizing of storage volumes in Linux. LVM provides a more advanced way to manage storage by abstracting physical storage into logical units.

1. **Physical Volumes (PVs)**:
   - Represent the actual physical storage devices.
   - Created using commands like `pvcreate` or `sudo pvs`.

2. **Volume Groups (VGs)**:
   - Combine multiple physical volumes into a single storage.
   - Created using commands like `vgcreate` or `sudo vgs`.

3. **Logical Volumes (LVs)**:
   - Act as partitions within the volume group.
   - These can be resized or extended without worrying about the physical storage.
   - Created using commands like `lvcreate` or `sudo lvs`.

### What is a File System
After creating an LV in LVM, you need to format it with a file system (e.g., ext4, xfs, btrfs) so that you can store files and manage data on it.

#### How LVM Works with File System
<div align="center">
  <img width="523" alt="Screen Shot 2024-12-08 at 5 13 30 PM" src="https://github.com/user-attachments/assets/681da266-7b5b-432a-9c5f-18f13d0ecd3d">
</div>

## the block devices
#### when you run `lsblk` you shoud get
<div align="center">
  <img width="491" alt="Screen Shot 2024-12-08 at 5 41 49 PM" src="https://github.com/user-attachments/assets/75e871dc-a40c-4348-8095-d8c51f1e05ce">
</div>




