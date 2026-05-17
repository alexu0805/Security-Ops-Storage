# Security-Ops-Storage

SECURITY OPERATIONS DEPLOYMENT LOG
Cross-Platform Storage Architecture & Hardening Project

Node Identifier: NiteOpsTech@NiteOps-Lab
Date Logged: May 16, 2026
Target Environment: RHEL Enterprise / Windows 11 Dual-Boot
Classification: Internal Lab Documentation

1. Project Objective
The objective of this deployment was to engineer a dual-stage, non-persistent, cross-platform storage infrastructure. The design mandates two specific security profiles for high-value data, ensuring cryptographic isolation, protection against system compromise, and defense against data corruption across dual-boot handoffs.

2. Phase I: Storage Layout & Asset Inventory
Initial storage mapping confirmed the following persistent drive geometry across the node:

Physical ID | Capacity | Primary OS Mapping | Deployment Role / File System
Disk 3 | 476.92 GB | Windows (D:) | Maine Games (NTFS Storage)
Disk 4 | 476.92 GB | Windows (E:) | Steam Games (NTFS Storage)
Disk 5 | 476.92 GB | RHEL/Windows | RHEL Root (LUKS Encrypted Container) / EFI System
Disk 6 (HDD) | 931.51 GB | Unmapped / Shared | Air-Gapped Shared Vault (exFAT - NASCENT_FLSH)
Disk 6 (SSD) | 447.12 GB | Unmapped / Shared | Professional Cryptographic Vault (VeraCrypt exFAT Container)

3. Phase II: Air-Gapped Physical Vault Setup (NASCENT_FLSH)
A 1TB external Seagate enclosure with a physical power boundary switch was deployed to act as an unmapped, on-demand operational target.

3.1 Linux Partition Initialization
The partition table was structured via diskpart, followed by user-space block initialization on RHEL using the exfatprogs engine. Standard utilities strict limit constraints capped the volume label to an 11-character maximum constraint:
NiteOpsTech@NiteOps-Lab:~$ sudo mkfs.exfat -L "NSCENT_FLSH" /dev/sdd1
exfatprogs version 1.2.8
Creating exFAT filesystem(/dev/sdd1, cluster size=131072)
Writing root directory entry: done
exFAT format complete!

3.2 Automation Scripting & Macro Binds
To restrict automation exposure and eliminate persistent mounting vulnerabilities, non-persistent aliases were committed to the shell initialization profile (~/.bashrc):
# Custom Secure Storage Automation
alias tankon="sudo mkdir -p /mnt/nascent_flash && sudo mount /dev/sdd1 /mnt/nascent_flash && echo Nascent Flash Active: Shared Link Established."
alias tankoff="sudo umount /mnt/nascent_flash && echo 'Nascent Flash Cooldown: Drive Safe to Power Off.'"

3.3 Cross-Platform Driver Alignment (Windows Side)
To resolve cross-platform driver alignment anomalies and eliminate a hardware-level Read-Only attribute bit flag enforced during external cluster alignment, the Windows administrative shell was leveraged:
DISKPART> select disk 6
DISKPART> attributes disk clear readonly
Disk attributes cleared successfully.
DISKPART> format fs=exfat quick label=NSCENT_FLSH

4. Phase III: Enterprise-Grade Cryptographic Vault (SEC_HOST)
A secondary, internal 480GB solid-state drive (Disk 6, 447.12 GB) was allocated exclusively to host professional-tier secure authentication credentials and access vectors.

4.1 OS Kernel Hardening Configuration
To prevent kernel caching leaks, multi-boot mount contentions, and stale cryptographic handles remaining resident in non-volatile memory pools, Windows Fast Startup was purged from the OS subsystem state:
System Policy Enforcement Notification: Windows Fast Startup dumps kernel runtime memory states into hiberfil.sys, preventing unmounting sequences and dirtying partition flags between environments. Disabling this enforces clean teardowns and state purification.

4.2 High-Entropy Container Synthesis
Using an open-source, enterprise-vetted architecture, VeraCrypt (v1.26.24) was initialized with core drivers anchored at C:\Program Files\VeraCrypt. A fixed, 440 GB high-entropy virtual cipher container was structured inside the exFAT physical boundary:
• Host Mount Path: Physical raw disk volume initialized under label SEC_HOST
• Cipher Target: Multi-layered 440 GB file-backed block storage container (vault.hc)
• Cryptographic Suite: Symmetric Block Cipher AES-256 coupled with SHA-512 Key Derivation
• Nested File System: exFAT (Default allocation unit blocks to guarantee full cross-environment native IOPS)

4.3 Obfuscation & Isolation Routine
To satisfy the requirement of absolute isolation, the host drive letter mapping (H:) was completely removed from the OS disk management table. The drive remains dark, unreadable, and hidden from unauthorized system processes until a manual cryptographic token challenge is completed.

5. Phase IV: Red Hat Enterprise Integration Script
The operational framework for mounting the high-security cryptographic storage container inside the RHEL secure lab has been codified as follows:
# Professional Secure Vault Operations
alias secureon="sudo mkdir -p /mnt/pro_vault && sudo mount /dev/sdd1 /mnt/usb_host && veracrypt --tno --protect-hidden=no /mnt/usb_host/vault.hc /mnt/pro_vault && echo 'Secure Vault Decrypted and Mounted.'"
alias secureoff="veracrypt -d /mnt/pro_vault && sudo umount /mnt/usb_host && echo 'Secure Vault Locked and Isolated.'"

Summary Status: SUCCESSFUL IMPLEMENTATION
Both cryptographic environments are fully operational, verified cross-platform, and optimized for staging into localized configuration control systems.
