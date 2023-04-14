---
title: "Setup for the workshop"
teaching: 10
exercises: 10
questions: 
objectives:
- "To have the Ubuntu VM running in VMWare Player, or"
- "To know which tools to used and where to find the data for local installation"
keypoints:
- "VM can be used after the workshop"
- "Installing required packages on own machine may require root privileges."
apps:
- "[VMWare player](https://www.vmware.com/nl/products/workstation-player.html)"
- "[The Ubuntu image](https://www.dropbox.com)"
---

For the workshop we use Ubuntu Linux as the main Operating System. We have created a virtual machine, including all the necessary tools and data. You are of course welcome to use your own Linux-based system.

## Installing the VMware image

The VMWare workstation 16 player from VMWare runs the virtual machine. It is available through the WUR Software center and is also [freely available](https://www.vmware.com/nl/products/workstation-player.html). 

The ubuntu image can be downloaded from dropbox. Please, download the zip-file and unzip it to a local hard drive. A network location usually does not work, so it is better to store it locally.

The next steps will install and run the Ubuntu image:

1. Start VMWare player
2. Select **Open existing VM** available on the main screen and locate the **Ubuntu Bioinformatics 22.04.vmx** file in the main folder of the VM.
3. Select **Edit virtual machine settings** to make the VM fit your needs. For memory size: keep the setting in the green, but above 1GB. For this workshop we recommend to use 80% of the available memory. Click **ok**
4. Clicking **Play virtual machine** (green arrow) should now start Ubuntu and you will automatically login.
5. It is now recommended to leave the host system alone and work only on the Ubuntu virtual machine.

The image is a regular Ubuntu install, so if you install this on your laptop or desktop you can continue to use it.

The password for the **bioinf** user is **bioinf** and the Ubuntu user has superuser access ('sudo').  

## Installing the data and tools on your own Ubuntu

The list of tools used for workshop:

1. Java 11
2. Assembly-stats
3. Minimap2
4. Flye assembler
5. Hifiasm assembler 
6. Mummer 4
7. Samtools & BCF tools
8. Tablet

## Origin of the data

The [data are available](https://dropbox.com) for download. We are using data from the [Telomere 2 telomere assembly of kiwi](https://academic.oup.com/hr/article/10/2/uhac264/6865344).

For this workshop we have selected read data sets and a particular region of a cultivar of this **kiwifruit**. 

## Rounding up

The data folders also contains, next to the raw data, all intermediate and end results. So if for some reason a step in the exercise fails to produce any results you can still continue.

{% include links.md %}
