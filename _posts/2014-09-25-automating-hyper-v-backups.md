---
layout: post
title: Automating Hyper-V Backups
category: Hyper-V
tags: [automation, hyper-v]
---

One of the many benefits of having a machine virtualized is the ease of backing it up. With a virtual machine, you can simply copy the entire contents of the hard drive (in the form of a VHD file or similar) and have the entire system backed up. Hyper-V takes the simple copy-paste process step further and provides an import/export feature that backs up not only the hard drive, but also the additional xml configuration files containing the network, hardware (CPU cores and memory), and other settings and restores it just as easily. With the buildout of Hyper-V [powershell commands](http://technet.microsoft.com/en-us/library/hh848559.aspx), I have been contemplating automating this process. My current backup process is as follows:

- Remember to perform the backup
- (Optional) Clean up existing backups to save space/only keep x number available
- Open Hyper-V GUI and initiate the export
- Point it at the directory where the backups are located
- Follow previous backup naming conventions
- (Optional) Once the export is completed, zip the resulting bits

After going through this manual process for a while, I decided it was time to automate it. I took a look online and was able to find an "old" [script](http://poshcode.org/3927) (circa early 2013); however, the powershell commands have since been changed. I went ahead and tweaked the script to the new updated commands. Additionally, I updated the zipping code to use the built-in .Net 4.5 code as the existing implementation would use the local OS drive to perform the zip, resulting in mysterious failures when the backup drive had plenty of room.

The features of the script are as follows:

- Specify which VMs (on which host!) to backup, allowing you to set different schedules for each VM if you'd like
- (Optional) Shut down the VM host once completed
- Auto cleanup of existing backup(s) if the drive is out of space
- Zip the backup after export

Without further ado, here is the script:

{% gist 9355d776cd5d8f133f44 %}

## Future Tweaks

I have yet to test some of the features from the original script such as automatic shut down, so I'll preface those with a _Use at your own risk_ warning. I am contemplating building out some additional features such as specifying how many backups to keep to make it a little more robust.

The biggest shortfall of this process is lack of ability to see the progress during the export. This is viewable inside the Hyper-V Manager during the export, but the powershell commands do not appear to expose this functionality anywhere.
