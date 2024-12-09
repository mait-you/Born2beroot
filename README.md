
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

### What’s a LVM
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

## The Block Devices
<div align="center">
  <img width="491" alt="Screen Shot 2024-12-08 at 5 41 49 PM" src="https://github.com/user-attachments/assets/75e871dc-a40c-4348-8095-d8c51f1e05ce">
</div>

### Explanation of `lsblk` Output

### 1.NAME
This shows the names of the devices and partitions.
- **sda**: The primary disk.
- **sda1, sda2, sda5, sda5_crypt**: The partitions and the encrypted partition on `sda`.
- **LVMGroup-***: Logical volumes managed by LVM inside the encrypted partition (`sda5_crypt`).
- **sr0**: A read-only device, typically a CD/DVD drive.

### 2.MAJ:MIN
These are the major and minor numbers that uniquely identify devices in the system. They are used by the kernel to refer to the devices:
- **8:0** refers to the main `sda` disk.
- **8:1** refers to the `sda1` partition.
- **254:0** refers to the encrypted partition `sda5_crypt`.
- **254:1, 254:2, etc.** refer to the logical volumes inside the encrypted partition.

### 3.RM (Removable)
This indicates whether the device is removable (USB drives, CD-ROM).
- **0**: Not removable (in the case of `sda` and its partitions).
- **1**: Removable (in the case of `sr0`, likely a CD/DVD drive).

### 4.SIZE
This column shows the size of the device or partition.
- **sda** has a size of 30.86 GB.
- **sda1** is 500 MB.
- **sda5** is 30.36 GB, and `sda5_crypt` shows the same size because it’s the encrypted partition containing the other logical volumes.

### 5.RO (Read-Only)
This column indicates whether the device is in read-only mode.
- **0**: Writable (most devices).
- **1**: Read-only (for example, a CD/DVD drive, like `sr0`).

### 6.TYPE
The type of the device:
- **disk**: A physical storage device (like `sda`).
- **part**: A partition on a disk (like `sda1`, `sda2`).
- **crypt**: A cryptographic (LUKS) volume, referring to the encrypted partition (`sda5_crypt`).
- **lvm**: A logical volume managed by LVM (like `LVMGroup-root`, `LVMGroup-swap`).
- **rom**: A read-only memory device (like `sr0` for a CD/DVD drive).

### 7.MOUNTPOINT
This column shows where the device or partition is mounted in the filesystem.
- **sda1**: Mounted at `/boot`, which stores the bootloader and kernel.
- **sda5_crypt**: It’s the encrypted container, but the logical volumes inside it are mounted.
  - **LVMGroup-root**: Mounted as the root filesystem `/`.
  - **LVMGroup-swap**: Mounted as swap space.
  - **LVMGroup-home**: Mounted as `/home`, where user data is stored.
  - **LVMGroup-var**: Mounted as `/var`, used for variable data like logs.
  - **LVMGroup-srv**: Mounted as `/srv`, used for services like web servers.
  - **LVMGroup-tmp**: Mounted as `/tmp`, for temporary files.
  - **LVMGroup-var--log**: Mounted as `/var/log`, for log files.
- **sr0**: This device is a CD/DVD drive, and it’s typically not mounted unless there’s a disc in the drive.

### Understanding `sda2`

When running the command `sudo fdisk -l /dev/sda`, the output shows an **`sda2`** partition labeled as an **Extended Partition**. Here's what it represents and why it exists:

#### `sda2`?

#### Extended Partition:
- **`sda2`** is not a regular data partition but an **Extended Partition**, a special type in the **MBR (Master Boot Record)** partitioning scheme.
- Its sole purpose is to act as a **container** for additional partitions (called **Logical Partitions**) when the limit of **4 primary partitions** is reached.

#### Logical Partitions Inside:
- Inside **`sda2`**, **Logical Partitions** are created to store data.
- In your case, **`sda5`** resides inside **`sda2`**.
- Logical Partitions always start numbering from **5**, even if there are fewer than 4 primary partitions.

### Why does `sda2` exist?

#### Primary Partition Limit:
- MBR disks support only **4 primary partitions**. To create more, an Extended Partition is used to hold additional Logical Partitions.

#### In MY Setup:
- **`sda1`** is a primary partition (used for `/boot`).
- **`sda2`**: An Extended Partition (container for Logical Partitions).
- **`sda5`** is a Logical Partition inside the Extended Partition **`sda2`** (used for LVM in your setup).

<div align="center">
   <img width="486" alt="Screen Shot 2024-12-08 at 9 09 24 PM" src="https://github.com/user-attachments/assets/8cd3f415-e5e9-48e8-a74a-52bb73565f6d">
</div>

## Difference Between `apt`, `apt-get`, and `aptitude`

These tools are used to manage packages but they differ in their purpose, interface, and features. Here’s a detailed comparison:

### 1. `apt`

**Description**:  
A modern and simplified package management interface. It is designed to be easier to use than `apt-get`.

**Features**:
- Easy to use with a unified command syntax.
- Clear user messages.
- Suitable for end users.

**Drawbacks**:
- Limited in handling complex scenarios.

**Common Usage**:
- `apt install package`: To install packages.
- `apt update`: To update package lists.
- `apt upgrade`: To upgrade installed packages.

### 2. `apt-get`

**Description**:  
A traditional, powerful package management tool, often used for complex tasks.

**Features**:
- Supports a wide range of options.
- Flexible in handling dependencies.
- Reliable for professional and administrative use.

**Drawbacks**:
- Less user-friendly and harder for casual users.

**Common Usage**:
- `apt-get install package`: To install packages.
- `apt-get update`: To update package lists.
- `apt-get dist-upgrade`: To upgrade the system.

### 3. `aptitude`

**Description**:  
An advanced package management tool that provides a text-based interactive interface for resolving issues.

**Features**:
- Interactive interface for displaying packages.
- Automatic dependency resolution.
- Supports all functions of `apt-get` with added features.

**Drawbacks**:
- Requires manual installation and is not as commonly used by casual users.

**Common Usage**:
- `aptitude install package`: To install packages.
- `aptitude update`: To update package lists.
- `aptitude safe-upgrade`: To safely upgrade packages.

### Comparison Between `apt`, `apt-get`, and `aptitude`

| Feature                  | `apt`               | `apt-get`              | `aptitude`               |
|--------------------------|---------------------|------------------------|--------------------------|
| **Interface**            | Simple and unified  | Traditional and complex| Text-based interactive   |
| **Ease of Use**          | For regular users   | For professionals      | For professionals        |
| **Dependency Resolution**| Manual              | Manual                 | Automatic                |
| **User Messages**        | Clear and improved  | Traditional            | Interactive              |
| **Default Installation** | Installed by default| Installed by default   | Needs manual installation|
| **Complex Functionality**| Limited             | Powerful               | Powerful                 |

## What’s a AppArmor & SELinux
**AppArmor** and **SELinux** are both security frameworks used to implement mandatory access control (MAC) protecting the system from harmful or unexpected behaviors ,It defines what actions programs are allowed to perform, but they have key differences in how they operate and are configured.
### Summary Table:

| Feature              | **AppArmor**                                 | **SELinux**                              |
|----------------------|----------------------------------------------|------------------------------------------|
| **Policy Type**      | Path-based                                   | Label-based                              |
| **Complexity**       | Easier to use and configure                  | More complex, but offers more granularity|
| **Flexibility**      | Less flexible, but sufficient for many users | Highly flexible and customizable         |
| **Default Distro**   | Ubuntu, Debian, SUSE                         | Red Hat, Fedora, CentOS, RHEL            |

### Difference Between AppArmor and SELinux:
- **Flexibility**: SELinux is more powerful and complex but requires more in-depth setup, while AppArmor is easier to use.
- **Implementation Method**: AppArmor is path-based, meaning it defines security policies based on file paths, whereas SELinux is label-based, meaning it uses labels for objects and enforces policies based on those labels.

## SSH (Secure Shell)
**SSH (Secure Shell)** is a cryptographic network protocol used to provide secure communication between two devices over an unsecured network (such as the internet). SSH is primarily used for securely accessing and managing remote servers. It offers a secure way to interact with computer systems via the command line and also allows secure file transfer.

### Secure Shell Host

A **Secure Shell (SSH) Host** refers to a server or system that runs an **SSH server** to allow secure remote access and communication over a network. The term "SSH Host" is used to describe the machine that accepts incoming SSH connections from clients.

#### Key Points about SSH Host:

1. **SSH Protocol**: It is a cryptographic network protocol used to provide secure communication between a client and a server over an insecure network (such as the internet).
    
2. **Role of the Host**: The SSH Host is the machine that listens for incoming SSH connections. It requires an **SSH server software** (such as OpenSSH) to allow remote clients to connect securely.
    
3. **Authentication**: To access the SSH Host, users must authenticate, typically through a password or an SSH key pair (private and public keys).
    
4. **Secure Access**: Once connected to the SSH Host, the user can remotely execute commands, transfer files, or perform other administrative tasks in a secure environment, as SSH encrypts all data exchanged between the client and the host.
    
5. **Use Cases**:
    - **Remote server management**: Administrators use SSH Hosts to manage servers and perform tasks remotely.
    - **Secure file transfers**: SSH is used to transfer files securely using protocols like **SFTP** (Secure File Transfer Protocol) and **SCP** (Secure Copy).
    - **Tunneling and port forwarding**: SSH Hosts can be used for secure tunneling to access internal networks.

To set up an SSH Host, you typically need to install and configure an SSH server (like OpenSSH) on the host machine.

### Set Up the SSH Host (Server)


### Set Up the SSH Host (Server) with Detailed Explanation and UFW Setup

1. **Install OpenSSH Server**:
    ```bash
    sudo apt update
    sudo apt install openssh-server -y
    ```
    - This command updates the package list and installs the OpenSSH server package on your system. OpenSSH is required to enable SSH access to your machine.

2. **Start the SSH Service**: Ensure that the SSH service is running:
    ```bash
    sudo systemctl start ssh
    ```
    - The command starts the SSH server if it’s not already running, enabling SSH connections. Alternatively, you can use `sudo service ssh start`, but `systemctl` is the recommended way in modern systems using **systemd**.

3. **Enable SSH to Start at Boot** (Optional):
    ```bash
    sudo systemctl enable ssh
    ```
    - This command ensures that the SSH service starts automatically when your machine boots up. It’s useful for ensuring remote access is available even after reboots.

4. **Check SSH Server Status**: You can check if the SSH server is running by using:
    ```bash
    sudo systemctl status ssh
    ```
    - This shows the status of the SSH service. If it is active, you’ll see a message indicating that the service is running. If there’s an issue, you can use this to diagnose.

5. **Change the SSH Port**: Edit the SSH configuration file:
    ```bash
    sudo vim /etc/ssh/sshd_config
    ```
    - If you don’t have `vim`, you can use `vi`, `nano`, or install `vim` with:
    ```bash
    sudo apt install vim -y
    ```
    - Find the line:
    ```yml
    #Port 22
    ```
    - Remove the `#` and change the port number (e.g., `4242`), ensuring that SSH listens on a different port to avoid common scanning attacks:
    ```yml
    Port 4242
    ```
    - After saving the file, restart the SSH service to apply changes:
    ```bash
    sudo systemctl restart ssh
    ```

6. **Enable Root Login** (Optional):
    ```bash
    sudo vim /etc/ssh/sshd_config
    ```
    - Find the line:
    ```yml
    #PermitRootLogin
    ```
    - Change it to one of the following options:

      - **Allow root login via SSH using any authentication method (password or public key)**:
        ```yml
        PermitRootLogin yes
        ```

      - **Disable root login entirely via SSH** (this is the most secure option):
        ```yml
        PermitRootLogin no
        ```

      - **Allow root login only with public key authentication; password-based login is disabled for root** (default on many systems):
        ```yml
        PermitRootLogin prohibit-password
        ```

      - To use public key authentication for root, generate a key:
        ```bash
        ssh-keygen -t rsa -b 4096
        ```
        - `-t rsa`: Specifies the RSA algorithm for key generation.
        - `-b 4096`: Sets the key length to 4096 bits for higher security.

      - Then, copy the public key:
        ```bash
        cat ~/.ssh/id_rsa.pub
        ```
      - Paste it into:
        ```bash
        /root/.ssh/authorized_keys
        ```
        - If the `authorized_keys` file doesn’t exist, create it.

      - **Alternatively**, root login is allowed only with public key authentication:
        ```yml
        PermitRootLogin without-password
        ```

7. **Find the Host's IP Address**: You need the IP address of the machine acting as the SSH host. You can get it by running:
    ```bash
    ip a
    ```
    - This command shows the network interfaces and IP addresses of the machine. The IP address (usually under `inet`) is required to connect to your machine via SSH from a remote location.

8. **Configure UFW (Uncomplicated Firewall) to Allow SSH**:
    UFW is a simple and user-friendly firewall manager in Ubuntu. To set up UFW to allow SSH traffic, follow these steps:
    
    - **Enable UFW** (if it's not already enabled):
      ```bash
      sudo ufw enable
      ```
      - This will activate the firewall and enforce the rules.
    
    - **Allow SSH on the new port (4242 in this example)**:
      ```bash
      sudo ufw allow 4242/tcp
      ```
      - This allows inbound traffic on port 4242 over TCP, the default protocol for SSH.
    
    - **If you’ve changed the default SSH port**, ensure that you allow traffic on the new port:
      ```bash
      sudo ufw allow ssh
      ```
    
    - **Check UFW Status**: To verify the current firewall settings, you can check the status of UFW:
      ```bash
      sudo ufw status
      ```

    - **(Optional) Allow additional services**: If you want to allow other services (e.g., HTTP on port 80), you can run:
      ```bash
      sudo ufw allow 80/tcp
      ```
    
    - **(Optional) Deny all incoming connections except SSH**:
      To block all incoming connections except for SSH (and other services you explicitly allow), you can run:
      ```bash
      sudo ufw default deny incoming
      sudo ufw default allow outgoing
      ```

    - **Restart UFW** (to apply all rules):
      ```bash
      sudo ufw reload
      ```

### Summary:

- **OpenSSH**: Set up OpenSSH to allow remote connections over SSH.
- **Change Port**: Modify the default SSH port for security reasons.
- **Root Login**: Configure root login via SSH, either enabling password or public key authentication.
- **UFW**: Set up UFW to allow SSH on the configured port and optionally block other services.


<div align="center">
   <img width="810" alt="Screen Shot 2024-12-09 at 11 56 20 AM" src="https://github.com/user-attachments/assets/bc98309a-7de7-4382-a063-d09f20eb59c3">
</div>



