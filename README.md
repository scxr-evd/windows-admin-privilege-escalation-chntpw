Task Report: Local Privilege Escalation via Linux Live Environment
Project Overview

The objective of this task was to demonstrate how to gain administrative privileges on a locked Windows system by using an external bootable environment. I successfully promoted a standard user account to an Administrator account in under 20 seconds after the system was booted.
Technical Environment

* Target System: Windows 11 (installed on a 512GB NVMe SSD).
  
 * Tool Used: Debian 13 (Trixie) Live Environment.
   
 * Utility: chntpw (Change NT Password and Registry Editor).
   
 * Hardware Constraint: The USB drive being used was slow, which could have delayed the process.

   
Part 1: Optimization for Speed
To ensure the task was completed as fast as possible, I did not run the operating system directly from the USB drive. Instead, I used a boot configuration to load the entire Debian Live system into the computer's RAM.
By using the "copy to RAM" method during the GRUB boot phase, the operating system no longer relies on the slow read speeds of the USB hardware. This made the terminal and file system operations nearly instantaneous, allowing the entire modification process to be completed in less than 20 seconds once the environment was ready.

Part 2: System Identification and Mounting

Once the Debian environment was active, I needed to locate the Windows installation. I used the lsblk command to list all block devices connected to the machine.

 * Locating the Partition: I identified the main Windows partition as nvme0n1p4 based on its size (approximately 212GB).
   
 * Mounting the Drive: I mounted this partition to a temporary directory located at /mnt/win.
   
 * Verifying the Path: I navigated to the system configuration directory: /mnt/win/Windows/System32/config. This is the specific location where Windows stores its "SAM" (Security Accounts Manager) database, which contains all local user information and privilege levels.
   
Part 3: Modifying the SAM Database
With the drive mounted, I used the chntpw tool in interactive mode to modify the user permissions.

 * Accessing the File: I ran the command sudo chntpw -i SAM. This opened the internal Windows registry files for editing.
   
 * User Selection: I chose the option to edit user data and passwords. The tool listed all available accounts on the machine, including:
   * Administrator (RID 01f4)
   * Guest (RID 01f5)
   * SCXR (RID 03e9)
   * user1 (RID 03ea)
     
 * Promoting the User: I selected the account "user1". I observed that this account was currently a standard user. I then used the specific command within the tool to "Promote user" (make user an administrator).
   
 * Verification: The tool confirmed that the user was successfully added to the Administrators group. I then exited the tool and saved the changes to the hive.
   
Part 4: Conclusion and Analysis
This exercise proves that physical access to a machine often bypasses software security measures. By using a Linux Live environment, we can ignore Windows file permissions and edit the core security files directly.
Key Findings:

 * Efficiency: Loading the OS to RAM is a critical step when working with slow external storage.
   
 * Direct Modification: Using the chntpw utility is faster and more reliable than attempting to use command-line tricks within Windows itself, as it requires no passwords or existing access.
   
 * Security Recommendation: To prevent this type of unauthorized privilege escalation, full-disk encryption (such as BitLocker) should be used. Encryption would have prevented me from mounting the partition and reading the SAM file.
