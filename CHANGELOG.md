**13.10.2024 v2**
* Updated `BlueToolFixup.kext`
* Fixed FireVault keyboard layout

**13.10.2024**

* Updated OpenCore to version 1.0.2 (Nightly)
* Updated Drivers and kexts
* Added custom compiled AdvancedMap.kext based on PR from unitedastronomer. Enables 3D Globe in Maps app in macOS Monterey to Sequoia
* Reverted to `SSDT-PORTS.aml` instead of `USBPorts.kext` for USB mapping
* Tested with macOS 15.0

**07.09.2024**
> [!IMPORTANT]
> 
> - Don't update AppleALC – it's a custom build only containing code required for Realtek ALC1220 using layout-id 17!
> - AdvancedMap does work in Sequoia but it doesn't work in Sonoma.
- Fetched upstream:
  - ACPI:
    - Enabled DMAR table
    - Disabled `SSDT-PLUG`. No longer required.
    - Disabled `SSDT-MCHC` &rarr; AppleSMBusPCI driver loads fine without it.
    - Removed `SSDT-XSPI` &rarr; Cosmetic only.
    - Added `SSDT-PORTS.aml` &rarr; Switched to SSDT USB mapping.
    - Deleted cosmetic tables: `SSDT-DMAC`, `SSDT-FWHD` and `SSDT-PMC`.
    - Dropped `HPET` Table &rarr; Superfluous `HPET` since it is a legacy device which is disabled by `SSDT-AWAC-ARTC.aml` anyway.
    - Enabled Quirk `FadtEnableReset` &rarr; Fixes  Crashes on shutdown/reboot. There were crash notifications after rebooting/shutting down macOS 15 (24A5264n).
    - Disabled NormalizeHeaders &rarr; No longer required.
    - Enabled `ResetLogoStatus` Quirk since value for "Displayed" in `BGRT.aml` is not `0`.
  - Booter
    - Populated `MmmioWhitelist` after analyzing bootlog (See [guide](https://github.com/5T33Z0/OC-Little-Translated/tree/main/12_MMIO_Whitelist) for more details)
    - Disabled **Booter/Patch** for macOS 13 compatibility:
		- `Skip Board ID check`
		- `Reroute HW_BID to OC_BID`
		- Enabled `AvoidRuntimeDefrag` quirk.
  - DeviceProperties
    - Removed `Radeon R9 270` patch
    - Updated `Intel UHD Graphics 630` patch
  - Kexts
    - Added [**AppleIGC.kext**](https://github.com/SongXiaoXi/AppleIGC) &rarr; Kext for Intel I225-V 2.5 Gbit Ethernet. No more pesky fixes forgetting Ethernet to work are required!
    - Added [AdvancedMap.kext](https://github.com/narcyzzo/AdvancedMap) &rarr; Enables 3D Globe in Maps.
    - Added `AMFIPass`
  - Kernel
    - Patch
      -  Disabled for macOS 13 compatibility:
      - `Force IOGetVMMPresent`
  		- `Reroute kern.hv_vmm_present patch (1)`
  		- `Reroute kern.hv_vmm_present patch (2)`
  		- `Disable Library Validation Enforcement`
		- Quirks
  		- Enabled `CustomSMBIOSGuid` ???
  		- Enabled `DisableIoMapperMapping`
		- Scheme
  		- Disabled `Kernel/Scheme/FuzzyMatch` &rarr; Only required for legacy OSX ≤ 10.6
  - Misc
    - Boot
      - `HibernateMode` set to `NVRAM` 
    - NVRAM
      - Added `revpatch:asset` &rarr; Allows Content Caching when `sysctl kern.hv_vmm_present` returns `1` on macOS 11.3 or newer
  		- Added `revblock:pci` to prevent PCI and RAM configuration notifications when using `MacPro7,1` SMBIOS

**27.06.2022**
- Restored working `IntelBluetoothFirmware` kext


**21.06.2022**
- Fetched upstream
- Updated config to Monterey
- Removed some ACPI tables
- Adopted to my new GPU - RX 6600 XT
- Removed I225 fixes. See [5T33Z0/I225-V_FIX.md](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md)
- Itlwm blueooth injector replaced with BlueToolFixup (needs for Monterey)


**23.01.2022**
- Fetched upstream:
  - Updated OpenCore to 0.7.7 Release.
  - **ACPI**
  	- Replaced `SSDT-AWAC-DISABLE.aml` with a more elegant [variant](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features/System_Clock_(SSDT-AWAC)#method-1-using-ssct-awac_n_rtc_y-recommended)
  	- Updated `SSDT-EC-USBX` → disables native `H_EC` and adds a fake `EC` for macOS instead.
    - Added `DMAR.aml` → Modified `DMAR` table with removed memory regions. [More info](https://github.com/5T33Z0/OC-Little-Translated/blob/edd7bfc5afb9f4a3a01435edfebdb417d299a0a8/00_About_ACPI/ACPI_Dropping_Tables/README.md#example-1-dropping-the-dmar-table)
    - Added `SSDT-DMAC.aml` → Adds `DMAC` device for macOS. [More info](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features/DMA_Controller_(SSDT-DMAC))
    - Added `SSDT-PMC.aml` → Adds `PMCR` device for macOS. [More info](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features/PMC_Support_(SSDT-PMC))
    - Added `SSDT-PORTS.aml` with new Port Mapping → Replaces OEMTableID `xh_cmsd4` (SSDT-7-xh_cmsd4.aml), so `USBPorts.kext` is no longer required! [More info](https://github.com/5T33Z0/OC-Little-Translated/tree/main/03_USB_Fixes/ACPI_Mapping_USB_Ports)
    - Deleted `SSDT-PPMC.aml` → Now for 100/200 Series Boards only.
  - **Config**
    - Added UEFI > AppleInput > `GraphicsInputMirroring` option → new OpenCore option.
    - Added UEFI > Audio > `DisconnectHda` option → new OpenCore option.
    - Added UEFI > Drivers > `Comment` option → new OpenCore option.
    - Added UEFI > Output > `ReconnectGraphicsOnConnect` option → new OpenCore option.
    - Added UEFI > Output > `UIScale` option → new OpenCore option.
    - Added UEFI > Quirks > `EnableVmx` option → new OpenCore option.
    - Added UEFI > Quirks > `ForceOcWriteFlash` option → new OpenCore option.
    - Added UEFI > Quirks > `ResizeGpuBars` option → new OpenCore option.
    - Disabled `DisableIoMapper`. Refered to [DMAR.aml](https://github.com/5T33Z0/OC-Little-Translated/blob/edd7bfc5afb9f4a3a01435edfebdb417d299a0a8/00_About_ACPI/ACPI_Dropping_Tables/README.md#example-1-dropping-the-dmar-table)
    - Disabled `ExternalDiskIcons` Quirk → no longer necessary.
    - Kernel > Quirks: enabled `ThirdPartyDrives` → Enable trim for SATA SSDs.
    - Updated `ResizeAppleGPBars` to `-1`. Refered to [Docs](https://dortania.github.io/docs/release/Configuration.html#x1-260005) if you want to configure it.
    - Updated UEFI > APFS > `MinDate` to `-1`.
    - Updated UEFI > APFS > `MinVersion` to `-1`.
  -  **Kexts**
    - Removed `USBPorts.kext` → no longer needs. Refered to [SSDT-PORTS.aml](https://github.com/5T33Z0/OC-Little-Translated/tree/main/03_USB_Fixes/ACPI_Mapping_USB_Ports)
- Reordered some config sections to match upstream.
- Updated kexts
- Updated CPUFriendDataProvider with more agressive powersave settings.
- **Config**
  -  Removed NVRAM > Add `UIScale`. Refered to [Docs](https://dortania.github.io/docs/release/Configuration.html#x1-7900014)
  - Removed dublicated `csr-active-config` option.
  - Removed UEFI -> Audio > `MinimumVolume` → removed OpenCore option.
  - Updated UEFI -> AppleInput > `PointerPollMask` → new OpenCore option.
  - Updated UEFI -> AppleInput > `PointerPollMax` → new OpenCore option.
  - Updated UEFI -> AppleInput > `PointerPollMin` → new OpenCore option.
  - Updated UEFI -> ProtocolOverrides > `FirmwareVolume`. Refered to [Docs](https://dortania.github.io/OpenCore-Post-Install/universal/security/filevault.html)


**22.09.2021**
- Reordered and renamed some keys to match upstream
- Itlwm replaced with AirportItlwm
- Removed RealtekRTL8111 (I225-V now works)
- Filed APFS MinDate and MinVersion with values for Catalina
- Fetched upstream:
  - Updated OpenCore to 0.7.3 Release
  - Updated Drivers, Kexts and Resources
  - **Config**:
    - Updated config.plist to reflect latest OpenCore feature-set
    - Added SecureBootModel entries for iMac20.1 (j185)
    - Added ScanPolicy values for hiding EFI Folders and NTFS Drives from Boot Picker.
    - Fixed Intel I225-V kernel patch
    - Removed SSDT-DMAC.aml for now → unneccessary
    - Changed UHD630 Framebuffer device-id and stolenmem
