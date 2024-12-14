
# Born2beroot

## Index:

1. [What’s an Operating System](#whats-an-operating-system)
2. [What’s the Linux Kernel](#whats-the-linux-kernel)
3. [What’s a Virtual Machine](#whats-a-virtual-machine)
4. [What’s a Hypervisor](#whats-a-hypervisor)
5. [What’s LVM](#whats-lvm)
6. [What’s a File System](#whats-a-file-system)
7. [The Block Devices](#the-block-devices)
   1. [Explanation of `lsblk` Output](#explanation-of-lsblk-output)
   2. [Understanding `sda2`](#understanding-sda2)
   3. [Why does `sda2` exist](#why-does-sda2-exist)
8. [Difference Between `apt`, `apt-get`, and `aptitude](#difference-between-apt-apt-get-and-aptitude)
   1. [`apt`](#apt)
   2. [`apt-get`](#apt-get)
   3. [`aptitude`](#aptitude)
   4. [Comparison Between `apt`, `apt-get`, and `aptitude`](#comparison-between-apt-apt-get-and-aptitude)
9. [What’s a AppArmor & SELinux](#whats-a-apparmor-selinux)


---

## What’s an Operating System
**Operating System (OS)** is system software that manages a computer’s hardware and software resources. It provides a user-friendly interface and acts as a bridge between the user and the hardware, enabling applications to run efficiently.

- **Is Linux an Operating System**

   **Yes**, Linux is an Operating System (OS), but more accurately, it refers to the **kernel**, which is the core part of the OS

---

## What’s the Linux Kernel
**The Linux kernel** manages hardware resources and acts as the foundation for Linux distributions.

---

## What’s a Virtual Machine
**Virtual Machine (VM)** is a software capable of installing an Operating System (**OS**) within itself, making the OS think that it is hosted on a real computer. With virtual machines we can create virtual devices that will behave in the same way as physical devices, using their own CPU, memory, network interface and storage. This is possible because the virtual machine is hosted on a physical device, which is the one that provides the hardware resources to the VM.

---

## What’s a Hypervisor
**Hypervisor** is software that creates and manages virtual machines (VMs) on a physical device. It enables multiple operating systems to run simultaneously by allocating separate hardware resources to each VM.
- **Hosted Hypervisor**: Runs on top of an existing OS (e.g., Windows or Linux). Typically used for personal computers.
- **Bare-Metal Hypervisor**: Runs directly on the physical hardware without requiring an underlying OS. Common in servers and data centers.

---

## What’s LVM
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

---

## What’s a File System
After creating an LV in LVM, you need to format it with a file system (e.g., ext4, xfs, btrfs) so that you can store files and manage data on it.

- **How LVM Works with File System**
<div align="center">
  <img width="523" alt="Screen Shot 2024-12-08 at 5 13 30 PM" src="https://github.com/user-attachments/assets/681da266-7b5b-432a-9c5f-18f13d0ecd3d">
</div>

---

## The Block Devices

<div align="center">
  <img width="491" alt="Screen Shot 2024-12-08 at 5 41 49 PM" src="https://github.com/user-attachments/assets/75e871dc-a40c-4348-8095-d8c51f1e05ce">
</div>

### *Explanation of `lsblk` Output*

1. **NAME**:
   This shows the names of the devices and partitions.
   - **sda**: The primary disk.
   - **sda1, sda2, sda5, sda5_crypt**: The partitions and the encrypted partition on `sda`.
   - **LVMGroup-***: Logical volumes managed by LVM inside the encrypted partition (`sda5_crypt`).
   - **sr0**: A read-only device, typically a CD/DVD drive.

2. **MAJ:MIN**:
   These are the major and minor numbers that uniquely identify devices in the system. They are used by the kernel to refer to the devices:
   - **8:0** refers to the main `sda` disk.
   - **8:1** refers to the `sda1` partition.
   - **254:0** refers to the encrypted partition `sda5_crypt`.
   - **254:1, 254:2, etc.** refer to the logical volumes inside the encrypted partition.

3. **RM**: (Removable)
   This indicates whether the device is removable (USB drives, CD-ROM).
   - **0**: Not removable (in the case of `sda` and its partitions).
   - **1**: Removable (in the case of `sr0`, likely a CD/DVD drive).

5. **SIZE**:
   This column shows the size of the device or partition.
   - **sda** has a size of 30.86 GB.
   - **sda1** is 500 MB.
   - **sda5** is 30.36 GB, and `sda5_crypt` shows the same size because it’s the encrypted partition containing the other logical volumes.

7. **RO**: (Read-Only)
   This column indicates whether the device is in read-only mode.
   - **0**: Writable (most devices).
   - **1**: Read-only (for example, a CD/DVD drive, like `sr0`).

8. **TYPE**:
   The type of the device:
   - **disk**: A physical storage device (like `sda`).
   - **part**: A partition on a disk (like `sda1`, `sda2`).
   - **crypt**: A cryptographic (LUKS) volume, referring to the encrypted partition (`sda5_crypt`).
   - **lvm**: A logical volume managed by LVM (like `LVMGroup-root`, `LVMGroup-swap`).
   - **rom**: A read-only memory device (like `sr0` for a CD/DVD drive).

9. **MOUNTPOINT**:
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

**`sda2`** ?

- **Extended Partition**:
   - **`sda2`** is not a regular data partition but an **Extended Partition**, a special type in the **MBR (Master Boot Record)** partitioning scheme.
   - Its sole purpose is to act as a **container** for additional partitions (called **Logical Partitions**) when the limit of **4 primary partitions** is reached.
   
- **Logical Partitions Inside**:
   - Inside **`sda2`**, **Logical Partitions** are created to store data.
   - In your case, **`sda5`** resides inside **`sda2`**.
   - Logical Partitions always start numbering from **5**, even if there are fewer than 4 primary partitions.

### Why does `sda2` exist

**Primary Partition Limit**:
   - MBR disks support only **4 primary partitions**. To create more, an Extended Partition is used to hold additional Logical Partitions.

**In MY Setup**:
   - **`sda1`** is a primary partition (used for `/boot`).
   - **`sda2`**: An Extended Partition (container for Logical Partitions).
   - **`sda5`** is a Logical Partition inside the Extended Partition **`sda2`** (used for LVM in your setup).

<div align="center">
   <img width="486" alt="Screen Shot 2024-12-08 at 9 09 24 PM" src="https://github.com/user-attachments/assets/8cd3f415-e5e9-48e8-a74a-52bb73565f6d">
</div>

---

## Difference Between `apt`, `apt-get`, and `aptitude`

These tools are used to manage packages but they differ in their purpose, interface, and features. Here’s a detailed comparison:

### `apt`

   **Description**:  
   - A modern and simplified package management interface. It is designed to be easier to use than `apt-get`.
   
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

### `apt-get`

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

### `aptitude`

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

---

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

---

## Setting Up and Using Sudo

1. **Install Sudo**
   switch user to root:
   ```bach
   su -
   ```

   This installs the `sudo` package:

   ```bash
   apt update
   apt install sudo -y
   ```

2. **Add a User to the Sudo Group**

   To allow a user to execute commands with `sudo`:
      - Add the user to the `sudo` group:
   
         ```bash
         usermod -aG sudo username
         ```
         - `-a`: apand user whitout removed from athers group.
         - `-G`: whitch group.
      
         - Replace `username` with the actual user’s name.
      - reboot.
   
      ```bash
      reboot
      ```
   Now you can use `sudo`.

3. **Verify the user’s group membership**:
   ```bash
   groups username
   ```
   The output should include `sudo`.
   or
   ```bash
   getent group sudo
   ```
   The output should include your `username`.

4. **Verify Sudo Access**

   Log in as the user and test `sudo` privileges by running:
   ```bash
   sudo whoami
   ```

5. **Configure Sudo Privileges**

   Modify `sudo` permissions by editing the sudoers file. Use the `visudo` command to ensure syntax validation:
   ```bash
   sudo visudo
   ```

   1. Find the line:
   ```plaintext
   Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin"
   ```
   - Add this:
     -`:/sbin:/bin`.
   - `secure_path`: The secure path it uses when running it is specified using `sudo`.
      ```plaintext
      Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
      ```

   2. Add this lines:

   - `requiretty`: `sudo` requires the command to be run from an interactive terminal session (TTY).
      - **TTY** is a terminal session that allows real-time communication with the system.
      ```plaintext
      Defaults        requiretty
      ```
      
   - `badpass_message`: Dplays a custom message when using a wrong password with `sudo`.
      ```plaintext
      Defaults        badpass_message="Wrong password. Please try again!"
      ```

   - `logfile`: Will store logs in of commands in `/var/log/sudo/sudo.log` that were run with `sudo`.
      ```plaintext
      Defaults        logfile="/var/log/sudo/sudo.log"
      ```
      
   - `log_input`, `log_output`: Logs input & output.
   - `iolog_dir`: Sets the directory to save additional output and input logs.
      ```plaintext
      Defaults        log_input
      Defaults        log_output
      Defaults        iolog_dir=/var/log/sudo
      ```

   - `passwd_tries`: Limits connection attempts using sudo.
      ```plaintext
      Defaults        passwd_tries=3
   
   - Add specific user permissions: (Opestional)
     ```plaintext
     username ALL=(ALL:ALL) ALL
     ```
   
     - The first `ALL` means the user can run commands on any host.
     - `(ALL:ALL)` means the user can run commands as any user `-u`and any group `-G`.
     - The last `ALL` allows the user to run any command.
     Replace `username` with the actual user’s name.
   
      - To allow passwordless sudo (not recommended for security reasons):
        ```plaintext
        username ALL=(ALL:ALL) NOPASSWD:ALL
        ```
      
      - Restrict a user to specific commands:
        ```plaintext
        username ALL=(ALL:ALL) /path/to/command1, /path/to/command2
        ```
---

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

### Set Up the SSH Host (Server) and UFW Setup

1. **Install OpenSSH Server**:
   Update the package list and install OpenSSH server:
   ```bash
   sudo apt update
   sudo apt install openssh-server -y
   ```
   - OpenSSH is required to enable SSH access to your machine.

2. **Start and Enable SSH Service**:
   Ensure the SSH service is running:
    ```bash
    sudo systemctl start ssh
   ```
      - you can use `sudo service ssh start`, but `systemctl` is the recommended way in modern systems using **systemd**.

   To enable SSH to start automatically at boot: (Optional)
   ```bash
   sudo systemctl enable ssh
   ```
   Check if SSH is running:
   ```bash
   sudo systemctl status ssh
   ```
3. **Change the Default SSH Port**: Edit the SSH configuration file:
   Edit the SSH configuration file to change the default port:
   ```bash
   sudo vim /etc/ssh/sshd_config
   ```
      - If you don’t have `vim`, you can use `vi`, `nano`, or install `vim` with:
      ```bash
      sudo apt install vim -y
      ```
   Locate the line:
   ```plaintext
   #Port 22
   ```
   Uncomment and change the port number (`4242`):
   ```plaintext
   Port 4242
   ```
   Restart SSH to apply changes:
   ```bash
   sudo systemctl restart ssh
   ```

4. **Configure Root Login** (Optional):
   Edit the SSH configuration file:
   ```bash
   sudo vim /etc/ssh/sshd_config
   ```
   - Find the line:
   ```yml
   #PermitRootLogin
   ```
   - Change it to one of the following options:

      Modify the `PermitRootLogin` option:
      - **Allow root login**:
        ```plaintext
        PermitRootLogin yes
        ```
      - **Disable root login (recommended)**:
        ```plaintext
        PermitRootLogin no
        ```
      - **Allow root login only with public key authentication**:
        ```plaintext
        PermitRootLogin prohibit-password
        ```
   To enable public key authentication:
      1. Generate an SSH key: (in Client)
         ```bash
         ssh-keygen -t rsa -b 4096
         ```
            - `-t rsa`: Specifies the RSA algorithm for key generation.
            - `-b 4096`: Sets the key length to 4096 bits for higher security.
   
      2. Then, copy the public key:
         ```bash
         cat ~/.ssh/id_rsa.pub
         ```
         - Paste it into:
         ```bash
         /root/.ssh/authorized_keys
         ```
         - If the `authorized_keys` file doesn’t exist, create it.
   
   Restart SSH to apply changes:
   ```bash
   sudo systemctl restart ssh
   ```

5. **Find the Host's IP Address**:
   Get the machine’s IP address:
   ```bash
   ip a
   ```
   Look for the `inet` address under the relevant network interface.
   <div align="center">
      <img width="810" alt="Screen Shot 2024-12-09 at 11 56 20 AM" src="https://github.com/user-attachments/assets/bc98309a-7de7-4382-a063-d09f20eb59c3">
   </div>

6. **Configure UFW (Uncomplicated Firewall) to Allow SSH**:
   Install and enable UFW (if it's not already enabled):
   ```bash
   sudo apt update
   sudo apt install ufw -y
   sudo ufw enable
   ```
   Allow SSH traffic on the configured port:
   - Default port:
     ```bash
     sudo ufw allow ssh
     ```
   - Blocks all incoming requests.
   ```
   ufw default deny incoming
   ```
   - Allows all outgoing requests.
   ```
   ufw default allow outgoing
   ```
   - Custom port (4242):
     ```bash
     sudo ufw allow 4242
     ```
     - This allows inbound traffic on port 4242, the default protocol for SSH.
   Check the UFW status:
   ```bash
   sudo ufw status
   ```
   or
   ```bash
   udo ufw status numbered
   ```
   Reload UFW (to apply all rules):
   ```bash
   sudo ufw reload
   ```

7. **Configure Port Forwarding**: (For Virtual Machines)

   1. Open the virtual machine settings.
       <div align="center">
          <img width="500" alt="Screen Shot 2024-12-12 at 1 48 21 PM" src="https://github.com/user-attachments/assets/56b2aef6-5f6e-439a-8d70-275a363468c2" />
       </div>
   2. Navigate to **Network > Adapter 1 > Advanced > Port Forwarding**.
       <div align="center">
          <img width="500" alt="Screen Shot 2024-12-12 at 1 48 33 PM" src="https://github.com/user-attachments/assets/14ed324b-4666-4f78-a312-4c5452e1ba47" />
       </div>
   3. Add a rule to forward the host port to the guest port (`4242`).
       <div align="center">
          <img width="500" alt="Screen Shot 2024-12-12 at 1 48 50 PM" src="https://github.com/user-attachments/assets/2411b482-5766-44a3-a012-f1e3e913f13c" />
       </div>
      
8. **Connect to the SSH Server**:

   Connect to the server via SSH:
   ```bash
   ssh your_username@127.0.0.1 -p 4242
   ```
   or
   ```bash
   ssh your_username@localhost -p 4242
   ```
   Replace `your_username` with your actual username.

   To exit the SSH session, type:
   ```bash
   exit
   ```

---

## Creating a Group
   to create a group
   ```bach
   sudo groupadd user42
   ```
   ```bach
   getent group
   ```
   add user to the group:
   ```bach
   sudo usermod -aG user42 username
   ```

---

## Setting Password Policy

   Update the package list and install libpam-pwquality:
   ```bash
   sudo apt update
   udo apt-get install libpam-pwquality -y
   ```
   Edit the common-password configuration file:
   ```bash
   sudo vim /etc/pam.d/common-password
   ```
   1. Find the line:
   ```plaintext
   password        requisite                       pam_pwquality.so retry=3
   ```
   - Add this:
      - `minlen=10`: The password must have a minimum length of 10 characters.
      - `ucredit=-1`: The password must include at least *(-1)* one uppercase letter (A-Z). 
      - `lcredit=-1`: The password must include at least *(-1)* one lowercase letter (a-z).
      - `dcredit=-1`: The password must contain at least *(-1)* one digit (0-9).
      - `maxrepeat=3`: Prevents using the same character more than 3 times consecutively (aaa or 111).
      - `reject_username`: Disallows passwords that include the username as a whole or part of it.
      - `difok=7`: Requires at least 7 characters to be different between the new password and the old one.
      - `enforce_for_root`: Enforces the same password quality rules for the root user. By default.
   
   ```plaintext
   password        requisite                       pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
   ```
---

## Abuot bach script

### start whit file name
   - You can named `script_shell.sh` or `script_shell` whitout `.sh`.
   but is bater to use `.sh`.

### Shebang
   - in the top of file write:
      ```bash
      #!/bin/bash
      ```
   1. With `#!/bin/bash`:
      - `./script_shell.sh`: The script will use Bash as defined in the **Shebang** (#!/bin/bash) line to run the script.
      - `bash script_shell.sh`: This will always use Bash to execute the script, regardless of the Shebang line.
   2. Without `#!/bin/bash`:
      - `./script_shell.sh`: The script will run using the default shell in your system (/bin/bash, /bin/sh, etc.).
      - `bash script_shell.sh`: The script will always be executed using Bash, regardless of the absence of the **Shebang** line.
   to make the script can be run :
   ```bash
      chmod +x script_shell.sh
   ```

### Variables
   - **creat a var**
      ```bash
      VAR_NAME=VALUE
      ```
      
   - **Print all tmp vars**
      ```bash
      set
      ```

   - **Print all environment vars**
      ```bash
      env
      ```

  

   

