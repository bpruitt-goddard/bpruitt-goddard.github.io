---
layout: post
title: Encrypting Hyper-V Virtual Machines
---

Since updating to Windows 8, I have thoroughly enjoyed making use of Hyper-V and working inside of a local virtual machine. It makes backing up the system a breeze and can give you greater flexibility than working on the "bare metal." But the abstraction of working in a virtualized environment is quickly forgotten when it comes time to encrypt it.

## Encryption Options
With a non-virtualized setup, you only have the option of encrypting the drive itself. This problem becomes interesting with virtualized disks. You additionally have the option of encrypting the .vhd or .vhdx file on the host. The two options are subtly different and each provide a particular strength.

Encrypting the virtual machine directly provides the benefit of maintaining encryption when the virtual hard disk is backed up or transferred. Whereas the latter option may be a better choice for situations where you want to bundle the encryption for a group of drives in one fell swoop. In this situation, if you have the virtual drives all in one folder/location, you can simply encrypt that folder and be done. Note that in this situation, once the virtual disk is moved, you lose any encryption. In my case, I only have a single virtual machine requiring encryption and would like to not have to worry about encryption when it comes time to back up the files.

## BitLocker - The Failed Attempt
Introduced in Windows Vista, BitLocker is Microsoft's solution to encryption. Once the feature is enabled in Windows, it provides an easy way to encrypt a drive and either boot using a password or startup key. However, there are [conflicting answers](http://social.technet.microsoft.com/Forums/en-US/d4bc991f-191b-4a7f-9988-ff57e8bd9a4b/enabling-bitlocker-on-hyperv-server-after-vms-in-use?forum=winserverhyperv) as to whether BitLocker supports being run in a virtualized environment. Originally, BitLocker required a hardware TPM (Trusted Platform Module), making it impossible to run in such an environment, but that restriction has been made optional. Seeing that change as well as this [official blog post](http://blogs.msdn.com/b/mszcool/archive/2010/02/03/bitlocker-in-a-windows-7-guest-running-on-a-hyper-v-r2-environment-or-anye-nvironment-without-a-tpm.aspx) got my hopes up that support was here.

Enabling BitLocker through the Group Policy Editor was as easy as the previously mentioned guide made it out to be. My first attempt was to use a password for protection. I felt that using an external drive with the key file on it felt less secure when the external media was also virtualized and would be sitting next to the encrypted drive. Once BitLocker was enabled, I simply brought up the context menu on the OS drive, and followed the "Turn BitLocker On" wizard. However this approach proved problematic as the drive was not able to be decrypted on startup. Instead of booting to the password prompt, I was simply greeted with a blank screen. Note that this screen is still encountered even if the recovery disk is inserted. After some quick searching proving unfruitful, I'm chalking this one up to a problem with Hyper-V's non-traditional BIOS and bootup.

My second attempt with BitLocker was to try using the seemingly less secure external drive option to store the encryption key. This is done by saving a file to disk and having BitLocker detect the disk on startup. As there are typically no physical disks associated with virtual machines, a substitute virtualized floppy disk (.vfd file) would have to be used. Tabling the issue of how to protect and "hide" the vfd file for another time, I decided to give the approach a try. After creating and attaching a virtual floppy disk, I followed the aforementioned guide and used the `manage-bde` command line tool to set up the encryption and store the recovery on my vfd drive. On reboot I was greeted with a proper boot to Windows -- progress! However, this was quickly dashed as I was met with an unhelpful error message:

![BitLockerError](/assets/images/bitlocker-encryption-error.png)

Seeing this message and a few pages [describing this error](http://social.technet.microsoft.com/Forums/en-US/71c9f5f0-98b9-4782-b6e1-6641751e7192/mbam-20-testing-on-a-nontpm-windows-8-virtual-machine?forum=mdopmbam) as due to a lack of VM support for BitLocker, I ruled the software a bust and moved on. Perhaps someone else can get BitLocker working, but I had had enough.

## TrueCrypt
Enter TrueCypt, the open source alternative to BitLocker. Boasting the same features as BitLocker (drive encryption, windows boot drive encryption), I gave it a whirl. Encrypting a drive with the default settings of TrueCrypt worked well. Following the GUI, I was able to properly get the encryption up and running and even use a pre-boot password as I ideally hoped I could. TrueCrypt must use a different pre-boot step as Hyper-V was able to pick up the step successfully and allow me to enter a password. TrueCrypt proved much more capable of working with virtualized setups.

![TrueCryptSuccess](/assets/images/truecrypt-boot-password.png)

After having the encryption software running successfully now for a week, I can safely say the virtual machine abstraction has returned, and I can resume working, blissfully unaware of the underlying setup.