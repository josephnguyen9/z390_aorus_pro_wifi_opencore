## Build Boot loader for MACOS for PC using OpenCore
Based on configurations from: https://github.com/blacklizard/gigabyte-z390-aorus-pro-wifi-hackintosh-opencore

## PC builds
- CPU:Intel Core I7 9700
- Mainboard: Z390 AORUS PRO WIFI
- GPU: Radeon RX 5600 XT 
- WIFI: BCM943602CS
- Bluetooth: ASUS USB-BT400
- SSD: Samsung 970 Pro 1TB
- RAM: using 3 different 8GB suppliers, total 32GB
- Monitor 1: Samsung 4K 28" UE590
- Monitor 2: Samsung 4K 28" UE590
- Apple Magic Mouse
- Apple Magic Keyboard

![](Images/monterey%2012.3.1.png)

## Not working
- Sleep
- Microphone of Apple EarPods 3.5mm Headphone Plug


# Download old MacOS versions
You can follow this thread to download a specific version of MacOS: https://github.com/munki/macadmin-scripts

# Setup BIOS
### 1. Upgrade to F11 or F12c
### 2. Disable FCG Lock on Z390 pro otherwise the booloader cannot work (hang at apple logo)
### 3. Setup BIOS with BIOS.md

# Create a Bootloader USB disk
### 1. create a bootable USB disk with MACOS
Follow: https://support.apple.com/en-vn/HT201372 to create a boot usb disk based on your current os, for examples:
#### Ventura
```
sudo /Applications/Install\ macOS\ Ventura.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```
#### Monterey
```
sudo /Applications/Install\ macOS\ Monterey.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```

#### Big Sur
```
sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```

### 2. Create EFI folder for this USB disk
Tools: https://github.com/corpnewt/MountEFI

### Run `MountEFI.command` file 
and choose the USB disk to mount the EFI:
![](images/mount%20EFI.png)

### The EFI will show in Finder > Go > Computer
![](images/EFI.png)

### 3: Drag the current EFI folder from this repo to the above EFI folder or build your old EFI folder base on following #3.1
The result should be:
![](Images/EFI%202.png)

### 3.1 Build your own EFI based on newer version of OpenCore and Kexts 
#### 3.1.1 Download the OpenCore from
https://github.com/acidanthera/OpenCorePkg/releases
#### 3.1.2 Unzip and
Drag following EFI folder from OpenCore to USB disk EFI folder:
#### 3.1.3 Go to USB Disk EFI => EFI => OC
Copy  sample.plist to (from Unzipped OpenCore/Docs )
Rename sample.plist to config.plist
#### 3.1.4 Go to USB Disk EFI => EFI => OC => Tools
Delete all
#### 3.1.4 Go to USB Disk EFI => EFI => OC => Drivers
Delete all except OpenRuntime.efi, ApfsDriverLoader.efi, VBoxHfs.efi
#### 3.1.5 Copy the necessary files to Disk EFI => EFI => OC => Kexts folders (.kexts are drivers)
The kext files to downloads
- `AppleACL.kext`: https://github.com/acidanthera/AppleALC/releases
- `BlueToolFixup.kext` (Required for macOS 12 or newer, as in macOS 12 Apple has changed parts of the Bluetooth stack from kernel-space to user-space) (Requires Lilu 1.5.4+) https://github.com/acidanthera/BrcmPatchRAM/releases (unzipped -> the same folder with Brcm)
- `BrcmFirmwareData.kext` -> same folder as above Brcm https://github.com/acidanthera/BrcmPatchRAM/releases
- `BrcmPatchRAM3.kext` -> same folder as above Brcm https://github.com/acidanthera/BrcmPatchRAM/releases
- `IntelMausi.kext` https://github.com/acidanthera/IntelMausi/releases
- `Lilu.kext` (Core) https://github.com/acidanthera/Lilu/releases
- `SMCProcessor.kext` https://github.com/acidanthera/VirtualSMC/releases
- `SMCSuperIO.kext` (Used for monitoring fan speed) -> from this repo
- `USBInjectAll.kext` (patch issue with usb) -> https://github.com/CaseySJ/OS-X-USB-Inject-All/releases
- `USBPorts.kext` (map usb ports because our mainboard has more usb port than normal Macbooks, can be built using: Hackintool: https://github.com/benbaker76/Hackintool or download the zip file: https://github.com/headkaze/Hackintool/releases/latest/download/Hackintool.zip)
- `VirtualSMC.kext` https://github.com/acidanthera/VirtualSMC/releases
- `WhateverGreen.kext` https://github.com/acidanthera/WhateverGreen/releases

The basic of OC folder before copying/downloading kext files
![](images/OC-basic.png)
### 3.1.6 Add to Drivers folder
- modGRUBShell.efi https://github.com/datasone/grub-mod-setup_var/releases\
This is a tool to disable CFG-Lock on Z390 Pro Wifi mainboard
### 3.1.7 Build config.plist
Tools to build:
- ProperTree https://github.com/corpnewt/ProperTree
- GenSMBIOS https://github.com/corpnewt/GenSMBIOS
Use the config.plist from this repo or
```
```
Follow this document from OpenCore if you are using different devices
https://dortania.github.io/OpenCore-Install-Guide/config.plist/#creating-your-config-plist

```
```
#### `Summary`
#### 1 Adding your SSDTs, Kexts and Firmware Drivers
##### 1.1
```
ProperTree.command
```
##### 1.2
```
Cmd/Ctrl + O
```
selecting the `config.plist` file on your USB
##### 1.3
```
Cmd/Ctrl + Shift + R
```
point it at your EFI/OC folder to perform a "Clean Snapshot"\
- `(This will remove all the entries from the config.plist and then adds all your SSDTs, Kexts and Firmware drivers to the config)`
- `Cmd/Ctrl + R is another option that will add all your files as well but will leave entries disabled if they were set like that before, useful for when you're troubleshooting but for us not needed right now`
![](images/plist1.png)
#### 2 Selecting your platform
The Intel Core i7 9700 processor is based on the `Coffee Lake` microarchitecture https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html

# Tools
- Mount EFI https://github.com/corpnewt/MountEFI
- OpenCore configurator:
https://mackie100projects.altervista.org/download-opencore-configurator/
- Plist edit pro: 
https://www.fatcatsoftware.com/plisteditpro/

# Notes:
### 1. From Big Sur 11.3 onwards the OpenCore XhciPortLimit Quirk stopped working. It hasn't been fixed yet. So generally we recommend using Catalina to perform your first USB configuration before upgrading your system.

### 2. The USBPorts.kext you create will work for all subsequent macOS versions.

### 3. If you change your System-Definition at any time the USBPorts.kext will stop working.

### 4. Be aware that the original USBInjectAll.kext version 0.7.1 was written in 2018 and since then new system-defintions have appeared. I recommend using the updated Kext by our Moderator @CaseySJ here :