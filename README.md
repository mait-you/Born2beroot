
# Born2beroot

## Index:

1. [What’s an Operating System](#whats-an-operating-system)
2. [What’s the Different between Rocky Linux and Debian](#whats-the-different-between-rocky-linux-and-debian)
3. [What’s the Linux Kernel](#whats-the-linux-kernel)
4. [What’s a Virtual Machine](#whats-a-virtual-machine)
5. [What’s a Hypervisor](#whats-a-hypervisor)
6. [What’s LVM](#whats-lvm)
7. [What’s a File System](#whats-a-file-system)
8. [What’s a Linux File Systems](#whats-a-linux-file-systems)
10. [The Block Devices](#the-block-devices)
    1. [Explanation of `lsblk` Output](#explanation-of-lsblk-output)
    2. [Understanding `sda2`](#understanding-sda2)
    3. [Why does `sda2` exist](#why-does-sda2-exist)
11. [Difference Between `apt`, `apt-get`, and `aptitude`](#difference-between-apt-apt-get-and-aptitude)
    1. [`apt`](#apt)
    2. [`apt-get`](#apt-get)
    3. [`aptitude`](#aptitude)
12. [What’s a AppArmor and SELinux](#whats-a-apparmor-and-selinux)
13. [SSH (Secure Shell)](#ssh-secure-shell)
    1. [Secure Shell Host](#secure-shell-host)
    2. [SSH Protocol](#ssh-protocol)
    3. [Set Up the SSH Host (Server)](#set-up-the-ssh-host-server)
14. [Configure UFW (Uncomplicated Firewall) to Allow SSH](#configure-ufw-uncomplicated-firewall-to-allow-ssh)
15. [Setting Up and Using Sudo](#setting-up-and-using-sudo)
16. [Creating a User](#creating-a-user)
16. [Creating a Group](#creating-a-group)
17. [Setting Password Policy](#setting-password-policy)

---Creating a User

## What’s an Operating System
**Operating System (OS)** is system software that manages a computer’s hardware and software resources. It provides a user-friendly interface and acts as a bridge between the user and the hardware, enabling applications to run efficiently.

- **Is Linux an Operating System**

   **Yes**, Linux is an Operating System (OS), but more accurately, it refers to the **kernel**, which is the core part of the OS.
   - for defense:
      - `uname -s`: -> `Linux` print the kernel name.
      - `uname -o`: -> `GNU/Linux` print the operating system.

---

## What’s the Different between Rocky Linux and Debian
- **Rocky**:
   - Based on Red Hat and designed as a free alternative to CentOS.
   - Ideal for large businesses and organizations.
   - Uses RPM package management system.
- **Debian**:
   - Uses APT and dpkg for package management.
   - Ideal for both beginners and developers.

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
- **How They Work Together**:
   - **MBR** provides the basic structure for dividing a physical disk.
   - **LVM** is implemented on top of a partition created using MBR.
      - **Example**: Create an LVM physical volume (PV) on a partition like /dev/sda5 (created using MBR), then use LVM to create volume groups and logical volumes dynamically.
- **In Short**:
   - **DOS (MBR)**: A way to divide the disk into partitions.
   - **LVM**: A tool for managing storage across one or more partitions dynamically. It adds flexibility and functionality but relies on a partitioning scheme like MBR or GPT underneath.

---

## What’s a File System
After creating an LV in LVM, you need to format it with a file system (e.g., ext4, xfs, btrfs) so that you can store files and manage data on it.

- **How LVM Works with File System**
<div align="center">
  <img width="523" alt="Screen Shot 2024-12-08 at 5 13 30 PM" src="https://github.com/user-attachments/assets/681da266-7b5b-432a-9c5f-18f13d0ecd3d">
</div>

---

## What’s a Linux File Systems
<div align="center">
   <img width="523" alt="Screen Shot 2024-12-28 at 1 49 41 PM" src="https://github.com/user-attachments/assets/dacd3389-303e-437c-a5f2-b4d9ab599dee" />
</div>

---

## The Block Devices

<div align="center">
  <img width="491" alt="Screen Shot 2024-12-08 at 5 41 49 PM" src="https://github.com/user-attachments/assets/75e871dc-a40c-4348-8095-d8c51f1e05ce">
</div>



### *Explanation of `lsblk` Output*

<details>
    <summary>
        <h4>1. NAME:</h4>
        <p>This shows the names of the devices and partitions.</p>
    </summary>
    <ul>
        <li><strong><code>sda</code></strong>: The primary disk.</li>
        <li><strong><code>sda1, sda2, sda5, sda5_crypt</code></strong>: The partitions and the encrypted partition on <code>sda</code>.</li>
        <li><strong><code>LVMGroup-*</code></strong>: Logical volumes managed by LVM inside the encrypted partition (<code>sda5_crypt</code>).</li>
        <li><strong><code>sr0</code></strong>: A read-only device, typically a CD/DVD drive.</li>
    </ul>
</details>

<details>
    <summary>
        <h4>2. DEVICE NUMBERS:</h4>
        <p>These refer to the device numbers for the partitions and logical volumes.</p>
    </summary>
    <ul>
        <li><strong><code>8:0</code></strong>: Refers to the main <code>sda</code> disk.</li>
        <li><strong><code>8:1</code></strong>: Refers to the <code>sda1</code> partition.</li>
        <li><strong><code>254:0</code></strong>: Refers to the encrypted partition <code>sda5_crypt</code>.</li>
        <li><strong><code>254:1, 254:2, etc.</code></strong>: Refers to the logical volumes inside the encrypted partition.</li>
    </ul>
</details>

<details>
    <summary>
        <h4>3. RM (Removable):</h4>
        <p>This indicates whether the device is removable (e.g., USB drives, CD-ROM).</p>
    </summary>
    <ul>
        <li><strong><code>0</code></strong>: Not removable (e.g., <code>sda</code> and its partitions).</li>
        <li><strong><code>1</code></strong>: Removable (e.g., <code>sr0</code>, typically a CD/DVD drive).</li>
    </ul>
</details>

<details>
    <summary>
        <h4>4. SIZE:</h4>
        <p>This column shows the size of the device or partition.</p>
    </summary>
    <ul>
        <li><strong><code>sda</code></strong>: 30.86 GB.</li>
        <li><strong><code>sda1</code></strong>: 500 MB.</li>
        <li><strong><code>sda5</code></strong>: 30.36 GB (same size for <code>sda5_crypt</code> since it’s an encrypted partition containing logical volumes).</li>
    </ul>
</details>

<details>
    <summary>
        <h4>5. RO (Read-Only):</h4>
        <p>This column indicates whether the device is in read-only mode.</p>
    </summary>
    <ul>
        <li><strong><code>0</code></strong>: Writable (most devices).</li>
        <li><strong><code>1</code></strong>: Read-only (e.g., a CD/DVD drive like <code>sr0</code>).</li>
    </ul>
</details>

<details>
    <summary>
        <h4>8. TYPE:</h4>
        <p>The type of the device.</p>
    </summary>
    <ul>
        <li><strong><code>disk</code></strong>: A physical storage device (e.g., <code>sda</code>).</li>
        <li><strong><code>part</code></strong>: A partition on a disk (e.g., <code>sda1</code>, <code>sda2</code>).</li>
        <li><strong><code>crypt</code></strong>: A cryptographic (LUKS) volume, referring to the encrypted partition (<code>sda5_crypt</code>).</li>
        <li><strong><code>lvm</code></strong>: A logical volume managed by LVM (e.g., <code>LVMGroup-root</code>, <code>LVMGroup-swap</code>).</li>
        <li><strong><code>rom</code></strong>: A read-only memory device (e.g., <code>sr0</code> for a CD/DVD drive).</li>
    </ul>
</details>

<details>
    <summary>
        <h4>9. MOUNTPOINT:</h4>
        <p>This column shows where the device or partition is mounted in the filesystem.</p>
    </summary>
    <ul>
        <li><strong><code>sda1</code></strong>: Mounted at <code>/boot</code>, which stores the bootloader and kernel.</li>
        <li><strong><code>sda5_crypt</code></strong>: It's the encrypted container, but the logical volumes inside it are mounted.</li>
        <ul>
            <li><strong><code>LVMGroup-root</code></strong>: Mounted as the root filesystem <code>/</code>.</li>
            <li><strong><code>LVMGroup-swap</code></strong>: Mounted as swap space.</li>
            <li><strong><code>LVMGroup-home</code></strong>: Mounted as <code>/home</code>, where user data is stored.</li>
            <li><strong><code>LVMGroup-var</code></strong>: Mounted as <code>/var</code>, used for variable data like logs.</li>
            <li><strong><code>LVMGroup-srv</code></strong>: Mounted as <code>/srv</code>, used for services like web servers.</li>
            <li><strong><code>LVMGroup-tmp</code></strong>: Mounted as <code>/tmp</code>, for temporary files.</li>
            <li><strong><code>LVMGroup-var--log</code></strong>: Mounted as <code>/var/log</code>, for log files.</li>
        </ul>
        <li><strong><code>sr0</code></strong>: This device is a CD/DVD drive, and it's typically not mounted unless there's a disc in the drive.</li>
    </ul>
</details>


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

   - for defense: `sudo fdisk -l /dev/sda`
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

---

## What’s a AppArmor and SELinux

**AppArmor** and **SELinux** are both security frameworks used to implement mandatory access control (MAC) protecting the system from harmful or unexpected behaviors ,It defines what actions programs are allowed to perform, but they have key differences in how they operate and are configured.
- **AppArmor**:
   - It is easier to set up and manage than SELinux.
   - It is used in distributions such as **Ubuntu**, **Debian**, and **SUSE**.
- **SELinux**L:
   - It is more complex to set up and manage.
   - It is mainly used in distributions such as **Red Hat**, **CentOS**, and **Fedora**.

---

## SSH (Secure Shell)
**SSH (Secure Shell)** is a cryptographic network protocol used to provide secure communication between two devices over an unsecured network (such as the internet). SSH is primarily used for securely accessing and managing remote servers. It offers a secure way to interact with computer systems via the command line and also allows secure file transfer.

### Secure Shell Host

A **Secure Shell (SSH) Host** refers to a server or system that runs an **SSH server** to allow secure remote access and communication over a network. The term "SSH Host" is used to describe the machine that accepts incoming SSH connections from clients.

### SSH Protocol:

**SSH Protocol** is a cryptographic network protocol used to provide secure communication between a client and a server over an insecure network (such as the internet).

To set up an SSH Host, you typically need to install and configure an SSH server (like OpenSSH) on the host machine.

### Set Up the SSH Host (Server)

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
   > you can use `sudo service ssh start`, but `systemctl` is the recommended way in modern systems using **systemd**.

   To enable SSH to start automatically at boot: *(Optional)*
   ```bash
   sudo systemctl enable ssh
   ```
   Check if SSH is running:
   ```bash
   sudo systemctl status ssh
   ```
3. **Configure SSH**: Edit the SSH configuration file:

   Edit the SSH configuration file to change the default port:
   ```bash
   sudo vim /etc/ssh/sshd_config
   ```
   - If you don’t have `vim`, you can use `vi`, `nano`, or install `vim` with: `sudo apt install vim -y`

   1. **Change the Default SSH Port**:
      
      Locate the line:
      ```yml
      #Port 22
      ```
      Uncomment and change the port number (`4242`):
      ```yml
      Port 4242
      ```
      Restart SSH to apply changes:
      ```bash
      sudo systemctl restart ssh
      ```

   2. **Configure Root Login**:

      Locate the line:
      ```yml
      #PermitRootLogin
      ```

      Change it to one of the following options:

      1. **Disable root login**: *(Recommended)*
      ```yml
      PermitRootLogin no
      ```
      2. **Allow root login**:
      ```yml
      PermitRootLogin yes
      ```
      3. **Allow root login only with public key authentication**:
      ```yml
      PermitRootLogin prohibit-password
      ```
      - To enable public key authentication:
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
   
   3. **Restart SSH to apply changes**:
      ```bash
      sudo systemctl restart ssh
      ```

4. **Find the Host's IP Address**:

   Get the machine’s IP address:
   ```bash
   ip a
   ```
   Look for the `inet` address under the relevant network interface.
   <div align="center">
      <img width="810" alt="Screen Shot 2024-12-09 at 11 56 20 AM" src="https://github.com/user-attachments/assets/bc98309a-7de7-4382-a063-d09f20eb59c3">
   </div>

## Configure UFW (Uncomplicated Firewall) to Allow SSH:

1. Install and enable UFW (if it's not already enabled):

   ```bash
   sudo apt update
   sudo apt install ufw -y
   sudo ufw enable
   ```

   1. Allow SSH traffic on the configured port:

   - Custom port (4242):
      ```bash
      sudo ufw allow 4242
      ```
      - This allows inbound traffic on port 4242, the default protocol for SSH.

   - Blocks all incoming requests.
      ```
      sudo ufw default deny incoming
      ```

   - Allows all outgoing requests.
      ```
      sudo ufw default allow outgoing
      ```

   2. Check the UFW status:

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
   
   3. Make sure to leave only port 4242 open:

      <div align="center">
         <img width="466" alt="Screen Shot 2024-12-18 at 3 15 35 PM" src="https://github.com/user-attachments/assets/1d586bca-e886-4d9a-8841-14cf8f9e3806" />
      </div>
      
      if you want to delete:
      ```bash
      sudo ufw delete <number>
      ```

2. **Configure Port Forwarding**: (For Virtual Machines)

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
      
3. **Connect to the SSH Server**:

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
   1. Add the user to the `sudo` group:

      ```bash
      usermod -aG sudo username
      ```
      - `-a`: apand user whitout removed from athers group.
      - `-G`: whitch group.
   
      - Replace `username` with the actual user’s name.
   2. reboot.

      ```bash
      reboot
      ```
   Now you can use `sudo`.

3. **Verify the user’s group membership**:

   ```bash
   groups username
   ```
   - The output should include `sudo`.

   or
   ```bash
   getent group sudo
   ```
   - The output should include your `username`.

   or `getent group`, `cat /etc/group`.

4. **Verify Sudo Access**

   Log in as the user and test `sudo` privileges by running:
   ```bash
   sudo whoami
   ```

5. **Configure Sudo Privileges**

   Go to the `/etc/login.defs` configuration file and modify the following lines:

   - `PASS_MAX_DAYS 30`: Maximum number of days a password may be used.
   - `PASS_MIN_DAYS 2`: Minimum number of days allowed between password changes.
   - `PASS_WARN_AGE 7`: Number of days warning given before a password expires.

   For ensure the policy changes we've made are applied to our current users:

   - `chage -M 30 user_name`: Sets PASS_MAX_DAYS to 30 days for user.
   - `chage -m 2 user_name`; Sets PASS_MIN_DAYS to 2 days for.
   > do not forget the `root` user !!.

   Modify `sudo` permissions by editing the sudoers file. Use the `visudo` command to ensure syntax validation:

   ```bash
   sudo visudo
   ```

   1. Find the line:

      ```ylm
      Defaults        secure_path="..."
      ```

      - `secure_path`: The secure path it uses when running it is specified using `sudo`.

      ```ylm
      Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
      ```

   2. Add this lines:

   - `requiretty`: `sudo` requires the command to be run from an interactive terminal session (TTY).

      - **TTY**: is a terminal session that allows real-time communication with the system.

      ```ylm
      Defaults        requiretty
      ```
      
   - `badpass_message`: Dplays a custom message when using a wrong password with `sudo`.
   
      ```ylm
      Defaults        badpass_message="Wrong password. Please try again!"
      ```

   - `logfile`: Will store logs in of commands in `/var/log/sudo/sudo.log` that were run with `sudo`.
   
      ```ylm
      Defaults        logfile="/var/log/sudo/sudo.log"
      ```
      
   - `log_input`, `log_output`: Logs input & output.
   - `iolog_dir`: Sets the directory to save additional output and input logs.
   
      ```ylm
      Defaults        log_input
      Defaults        log_output
      Defaults        iolog_dir=/var/log/sudo
      ```

   - `passwd_tries`: Limits connection attempts using sudo.
   
      ```ylm
      Defaults        passwd_tries=3
   
   - Add specific user permissions: (Opestional)
   
     ```ylm
     username ALL=(ALL:ALL) ALL
     ```
   
     - The first `ALL` means the user can run commands on any host.
     - `(ALL:ALL)` means the user can run commands as any user `-u`and any group `-G`.
     - The last `ALL` allows the user to run any command.
     Replace `username` with the actual user’s name.
   
      - To allow passwordless sudo (not recommended for security reasons):
   
        ```ylm
        username ALL=(ALL:ALL) NOPASSWD:ALL
        ```
      
      - Restrict a user to specific commands:
        ```ylm
        username ALL=(ALL:ALL) /path/to/command1, /path/to/command2
        ```

---

## Creating a User

```bash
sudo adduser <user_name>
```
- The registered system will ask you for information such as `password`, full `name`, etc. You can press Enter to skip branches that you do not want to have multiple.

---

## Creating a Group
   to create a group
   ```bach
   sudo groupadd user42
   ```

   To verify the changes
   ```bach
   getent group
   ```
   or
   ```bach
   cat /etc/group
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
   ```ylm
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
   
   ```ylm
   password        requisite                       pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
   ```
---

## About the bach script

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

- ***• The architecture of your operating system and its kernel version***
   - `uname -a`: -a, --all                **print all information, in the following order**
      - `Linux mait-you42 6.1.0-28-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.119-1 (2024-11-22) x86_64 GNU/Linux`.
   - `uname -s`: -s, --kernel-name        **print the kernel name**
      - `Linux`.
   - `uname -n`: -n, --nodename           **print the network node hostname**
      - `mait-you42`.
   - `uname -r`: -r, --kernel-release     **print the kernel release**
      - `6.1.0-28-amd64`.
   - `uname -v`: -v, --kernel-version     **print the kernel version**
      - `#1 SMP PREEMPT_DYNAMIC Debian 6.1.119-1 (2024-11-22)`.
   - `uname -m`: -m, --machine            **print the machine hardware name**
      - `x86_64`.
   - `uname -o`: -o, --operating-system   **print the operating system**
      - `GNU/Linux`.

- ***• The number of physical processors***
   - `Socket(s):`: refers to the number of installing physical CPU sockets on the motherboard.
   - `lscpu | grep "^Socket" | awk '{printf $2}'`

- ***• The number of virtual processors***
   - `nproc`
 
- ***• The current available RAM on your server and its utilization rate as a percentage***
   - ``


## Set up WordPress website

1. Update the System
```bash
sudo apt update && sudo apt upgrade -y
```

2. Install Lighttpd

Install the Lighttpd web server:
```bash
sudo apt install lighttpd -y
```

- Start and enable the service:
```bash
sudo systemctl start lighttpd
```

- Verify the installation by accessing your server's IP address in a browser (port 80).
```bash
sudo systemctl enable lighttpd
```

3. Install MariaDB

Install MariaDB server and client:
```bash
sudo apt install mariadb-server mariadb-client -y
```

- Secure MariaDB:
```bash
sudo mysql_secure_installation
```

- Create a database and user for WordPress:

```bash
sudo mysql -u root -p
```
```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

4. Install PHP

Install PHP and the required extensions:
```bash
sudo apt install lighttpd mariadb-server php php-mysql php-cgi wget unzip -y
```

- Enable FastCGI for Lighttpd:
```bash
sudo apt install lighttpd-mod-fastcgi -y
sudo lighttpd-enable-mod fastcgi
sudo lighttpd-enable-mod fastcgi-php
sudo systemctl reload lighttpd
```

5. Install WordPress

- Navigate to the Lighttpd root directory
```bach
cd /var/www/html/
sudo rm index.lighttpd.html
```

- Download WordPress:
```bash
wget https://wordpress.org/latest.tar.gz
sudo tar -xzf latest.tar.gz
sudo mv wordpress/* .
sudo rmdir wordpress
sudo rm latest.tar.gz
```

- Set correct permissions:
```bash
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
```

6. Configure WordPress

- Visit your server's IP in a browser and follow the WordPress installation wizard.
- Provide the database details created earlier.

7. Add an Additional Service

**Chosen Service: Memcached**

Memcached can improve performance by caching database queries and data.

- Install Memcached:

```bach
sudo apt install memcached php-memcached -y
```

- Ensure Memcached is running:
```bach
sudo systemctl start memcached
sudo systemctl enable memcached
```

- integrate with WordPress
    - Use a plugin such as W3 Total Cache or WP Rocket to enable Memcached.

8. Configure UFW Firewall

- Open required ports:
```bash
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 11211  # Memcached port
sudo ufw enable
```

- Check firewall status:
```bash
sudo ufw status
```

9. Optional: Additional Services (Bonus)
    1. Enable HTTPS with Certbot:
    ```bash
    sudo apt install certbot python3-certbot-lighttpd -y
    sudo certbot --lighttpd
    ```

    2. Fail2Ban for added security.





