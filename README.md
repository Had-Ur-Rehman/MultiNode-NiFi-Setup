# NiFi-Setup
This is a guide to setup multi-node NiFi Cluster in CentOS7

## 1. Downloading Required Stuff:

* CentOS7ISO.
* VirtualBox.

## 2. Creating a VM Using VirtualBox:

* Click on the new button.
* Add the name of the VM as per your choice. 1. Type: Linux 2. Version: Red-Hat (64 bit)
* Assign memory according to the current Memory installed on your system. (4GB Recommended)
* Select Create a virtual hard disk now option
* Select VDI (Virtualbox Disk Image)
* Select Dynamically Allocated
* Assign the storage according to the available storage on your system. (Atleast 8GB)
* Creating Adapters 1. Goto File > Host Network Manager 2. There are 3 buttons shown namely: create, remove and properties 3. Create 2 Adapters and name them vboxnet0 and       vboxnet1
* Now go back to the VirtualBox and select the VM that is just created and click on the settings button.
* Goto Adapter tab. 1. Under Adapter2 tab, select the Enable Network Adapter checkbox and assign virtualbox host ethernet adapter. 2. Goto Adapter3 tab, select the Enable Network Adapter checkbox and assign virtualbox host ethernet adapter1 .
* Now goto Storage tab in settings and Select the empty disk. on the right side, add the CentOS7 ISO Image.
* Now run the VM
*
