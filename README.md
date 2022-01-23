# Gigabyte Z490 Vision G Hackintosh OpenCore

**Repo status**: Slowly maintained. Tested only with Catalina

[![Board](https://img.shields.io/badge/Gigabyte-Z490_Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.7-important.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-green.svg)](https://www.apple.com/li/macos/catalina/)

Original repo:
[![macOS Big Sur](https://img.shields.io/badge/macOS-11.6.2-white.svg)](https://www.apple.com/macos/big-sur/)
[![macOS Monterey](https://img.shields.io/badge/macOS-12.2-white.svg)](https://www.apple.com/macos/monterey-preview/)

<img src="./Additional%20Files/sysinfo.png" width=600px/>

## Introduction

This repo based on [5T33Z0 Gigabyte-Z490-Vision-G](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore). I made some changes especially for my system drived by Catalina, so i recommend you to check original repo.

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly.

## Build Info

<details>
<summary><strong>Hardware Components</strong></summary>

### System Specs

| Component           | Details                                     |
| :------------------ | :------------------------------------------ |
| Mainboard           | Gigabyte Z490 Vision G                      |
| BIOS		   			    | F20. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock`|
| CPU                 | Intel® Core i7 10700 (Codename Comet Lake) 	|
| RAM                 | 32 GB DDR4 2400 Crucial Basllistix Sport LT |
| iGPU		   			    | Intel® UHD 630 Headless                     |
| dGPU                | AMD Radeon R9 270                           |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)       |
| Ethernet            | Intel® 2.5GbE LAN chip.                     |
</details>
<details>
<summary><strong>BIOS Settings</strong></summary>

### BIOS Settings

* **Tweaker [TAB]**
  * Extreme Memory Profile (XMP): Enabled (if supported by RAM)
	* Advanced CPU Settings
		* VT-d: Enabled (disabled in config.plist anyway, so only relevant to Windows)
		* Intel Speed Shit: Enabled
* **Setings [TAB]**
	* Platform Power
		* ErP: Enabled (so USB Power turns off, after PC is shut down)
	* IO Ports
		* Internal Graphics: enabled.
		* Audio Controller: Enabled (Required for DP/HDMI sound)
		* Above 4G Decoding: Enabled
		* Re-Size BAR Support: Disabled
		* IOAPIC 24-119 Entries: Enabled
		* Super IO Configuration
			* Serial Port: Disabled
		* USB Configuration
			* Legacy USB Support: Disabled
			* XHCI Hand-off: Enabled
		* Network Stack Configuration
			* Network Stack: Disabled
* **Boot [TAB]**
	* CFGLock: Disabled (Option only available on newer BIOS versions)
	* Windows 10 Features: Windows 10
	* CSM: Disabled


	![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/raw/main/Pics/Gigabye-VisionG-BIOS-Hackintosh.jpg)

</details>
<details>
<summary><strong>OpenCore and Config Details</strong></summary>

### OpenCore Details

* **Version**: 0.7.7 (details see `config.plist`)
* **Compatible macOS**: 10.15.7 (Catalina)
* **System Definition:** `iMac20,1` Using a divergent SMBIOS rather than `iMac20,2` may require remapping of USB Ports, since the `info.plist` inside the `USBPorts.kext` refers to `iMac20,1` as `model`.
* **ACPI Patches:** `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-DMAC`, `SSDT-PPMC`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: [tekteq](https://github.com/tekteq/opencanopy-minimal-theme)
* **Chime**: `no`
* **FileVault**: `yes`
* **SecureBootModel**: `j185`
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB_Ports_List.pdf)
* **csr-active-config:** Catalina: SIP Enabled

### Note about Kexts
The following Kexts are enabled by default:

- `IntelBluetoothFirmware.kext`, `IntelBluetoothInjector.kext`, `itlwm.kext`
	- Disable they if you don't have an Intel WiFi/Bluetooth module.

- `CPUFriend.kext` and `CPUFriendDataProvider.kext`.
	- If you use a different CPU model, create your own DataProviderKext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) and replace original kext. See [Post-install Tweaks
](#Post-install-tweaks)</details>

## Installation
<details>
<summary><strong>How to install macOS</strong></summary>

### Installing macOS
If you already have macOS installed but want to perform a clean install, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/). It's a hassle-free app than can download Catalina and Big Sur. It also can create a USB Installer for you. And if you create multiple HFS partitions in the correct sizes, you can use it to create a multi macOS Installer USB Stick as well.

If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer)
</details>
<details>
<summary><strong>EFI Install Instructions</strong></summary>

### EFI Install Guide for OpenCore

1. Download latest Release and unpack it
2. Select the config of your choice and rename it to `config.plist`
3. To disable SIP, use `csr-active-config` `FF070000` for Catalina/Mojave
4. Graphics:
	- AMD GPUs may require additional `boot-args`. Check WhateverGreen repo to find out which you need.
	- The `config.plist` uses iGPU for Display(s) by default. If you want to use dGPU and iGPU in headless mode, open `config.plist` with a plist editor (or text editor) and comment-out the dictionary `PciRoot(0x0)/Pci(0x2,0x0)` with "#" first, to disable the existing entry. Then uncomment headless dict.		- **NOTE:** To choose preffered GPU, you need enable `CSM Support` in `Boot tab`, then go back to `IO Ports`, choose preffered GPU and finally disable `CSM Support`
5. Create SMBIOS infos for `iMac20,1`to the config.plist and save it.
6. Copy the EFI Folder to a FAT32 formated USB Stick
7. Reboot from USB Stick
8. Perform an NVRAM Reset
9. Boot macOS
10. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
11. Continue with Post-Install!

</details>
<details>
<summary><strong>Post-Install Tweaks</strong></summary>

### Strengthen Security
Once you got macOS running, change the following settings to make your system more secure:

- Change UEFI > APFS: `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for macOS High Sierra up to Big Sur can be found [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).</br>

	**BACKGROUND**: OpenCore 0.7.2 introduced a new security feature which prevents the APFS driver from loading if it does not match OS-specific Date (`MinDate`) and Version (`MinVersion`). If left at their default value `0` (as set in the `sample.plist`), the macOS partition will not show up in the Boot Picker unless Big Sur or newer is installed. For ease of use (and since I don't know which macOS you will be using) I've deactivated this feature. If you plan to setup a multiboot system running various iterations of macOS you probably should leave it at `-1`. Otherwise you won't be able to boot older macOSes.

- Change `SecureBootModel`from `j185` (for iMac20,1) to `j185f` (for iMac20,2). **NOTE**: Only applicable to macOS Catalina and newer. You should test these settings first using a USB flash drive since it can prevent the system from booting. Disable it for installing macOS Monterey if you have issues.


### Optimizing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management of your CPU for a more efficent overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/CPU_Power_Management_EN.pdf) to create your own.
When you're done, reboot. Have a look at the CPU behavior using Intel Power Gadget. You can see, that the CPU idle frequency should be lower now:

![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/CPU_PM.png)

### Enabling Apple Graphics Power Management (AGPM) for dedicated GPUs (NVIDIA and AMD)

- Generate an `AGPMInjector.kext` for your GPU using [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector)
- Copy it to `EFI\OC\Kexts`
- Enable the entry in the `config.plist`
- Save and reboot.
- Open [IORegistryExplorer](https://github.com/utopia-team/IORegistryExplorer/releases) and search for`PR00`. CPU Power Management and AGPM are working correctly if it looks like this: </br>
	![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/AGPMEnabler.png)

### Calculating Scan Policy (optional)
The items displayed in the Boot Picker Menu are based on a combination of bits representing supported devices (SATA, NVME, USB, etc.) and file systems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under Misc > Security in the `config.plist.` The default value is `0` (everything). Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy`, you can make use of this online calculator: https://oc-scanpolicy.vercel.app/. I am using `2687747` for example which hides EFI Folders and NTFS Drives. If I need windows I just boot it from the BIOS Boot Menu (F12).

**IMPORTANT**: Calculating a wrong `ScanPolicy` can lead to the Boot Picker being empty, so you can't boot into macOS. So make sure to test the value first by booting from FAT32 formatted USB Stick containing your EFI Folder with the new value for "Scan Policy".

### Changing Themes
- Add theme to `Resources/Image/[Theme name]`
- Open `config.plist`
- Go to Misc > Boot and change `PickerVariant` to: `[Theme name]`.
- Save and reboot

**NOTE**: For more Post-Install tweaks and tips, check out my small collection of [Config Tweaks](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/OpenCore_Config_Tweaks_EN.md)
</details>

## Credits and Thank yous
- Acidanthera and Team for the [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
- Dortantia for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- rierdorf for [OpenCore Sanity Checker](https://opencore.slowgeek.com/)
- [SchmockLord](https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D) for FakePCIID.kext to get the I225 2.5 Gigabit Ethernet Controller working before a Kernel Patch existed
- [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
- daliansky for [OC Little ACPI Hotpatch Collection](https://github.com/5T33Z0/OC-Little-Translated)
- [Pavo-IM](https://github.com/Pavo-IM/) for APGM Injector
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
- [chris1111](https://github.com/chris1111) for config validator apps for OpenCore and Clover
- [5T33Z0](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore) for original repo
- [tekteq](https://github.com/tekteq/opencanopy-minimal-theme) for awesome OpenCanopy theme
- Everybody.
