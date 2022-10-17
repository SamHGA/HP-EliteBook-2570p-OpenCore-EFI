# OpenCore EFI Folder for HP EliteBook 2570p
Bootable OpenCore EFI Folder and the relevant files used to make it. Made for the HP EliteBook 2570p, with macOS Monterey in mind. Initially based on OpenCore 0.8.5.

**<ins>PLEASE READ ALL THE INSTRUCTIONS BEFORE USING THIS EFI.</ins>**

***I will not be held responsible for any bricks just because you were too lazy to read.***

<ins>Even then, use this EFI at your own risk only if you know what you're doing and can edit the config.plist according to your own hardware and needs.</ins>

## **Considerations**
- You'll need any macOS Monterey version ready to be installed, either with a full installer or through recovery. ***<ins>THIS EFI WAS NOT MADE TO WORK WITH ANY OTHER macOS AND AS SUCH HAS NOT BEEN TESTED, HENCE I WILL NOT SUPPORT ANYONE WANTING TO USE A DIFFERENT macOS UNTIL I HAVE BEEN ABLE TO TEST AND MAKE MULTIPLE DIFFERENT EFIs FOR DIFFERENT VERSIONS.</ins>*** If you want to install a different version, see: **"What if I need > macOS Monterey?"**
- While officially you only need 4GB of RAM, I personally recommend no less than 8GB running in dual channel (E.G. Two sticks of RAM in each slot, for example 2 sticks of 4GB RAM and not just one 8GB stick.) and using a SATA SSD at the very least to get reasonable performance. However, this is optional and completely up to you.
- You ***NEED*** to exact same model or at the very least, have a same generation CPU and iGPU to use this config.plist as is.
- You absolutely ***NEED*** to have emulated NVRAM enabled by setting `LoadEarly` to `True` for both OpenRuntime.efi and OpenVariableRuntimeDxe.efi in the config.plist. (***You cannot boot if this is configured incorrectly. Be careful when snapshotting with ProperTree as it resets the*** `LoadEarly` ***value to false. This has the potential to break booting completely and will warrant a full reinstall.***) If you have questions, see: **"What if I don't know how to enable emulated NVRAM?"**
- You ***NEED*** to generate your own SMBIOS info using</ins> [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) <ins> with `MacBookPro10,1` and place it in your config.plist before being able to use this EFI. If you don't know how to do this, see: **"What if I don't know how to use GenSMBIOS?"**
- ***You cannot enable SIP, Secure Boot or remove the*** `-no_compat_check` ***boot arg or YOU WILL break booting.*** For more information, see: **"What if I want to enable SIP?"**, **"What if I want to enable Secure Boot?"**, and **"What if I want to remove the boot arg?"**

## BIOS Changes
   Set `Boot Options` > `Startup Menu Delay (Sec.)` to `05`. (This will give you a chance to enter the BIOS just in case you need to change anything, as I have found that it sometimes boots very quickly when using a SATA SSD.)
   
   Set `Boot Options` > `User Mode` to `HP Factory Keys`. (If you already had this set to `Customer Keys`, you can leave this setting as is.)
   
   Set `Boot Options` > `Boot Mode` to `UEFI Hybrid (With CSM)`. (This is only needed for the inital install as macOS Monterey has no native graphics support for Intel HD Graphics 4000. For more info, see: **"What if my screen shows garbled diagonal lines on boot?"**)
   
   Set `Device Configurations` > `Max SATA Speed` to `6.0 Gbps`. (The higher the number, the faster your hard drive can perform.)
   
   Set `Device Configurations` > `SATA Device mode` to `AHCI`. (`IDE` is obsolete and `RAID` should only be used if you are using more than one drive.)
   
   Set `Device Configurations` > `Express Card Link Speed` to `Generation 2`. (Same reasons as `Max SATA Speed`.)
   
   Set `Device Configurations` > `Smart Card Reader Power Setting (if present)` to `Powered on if card is present`. (Helps reduce overall power consumption.)
   
   Set `Device Configurations` > `Deep sleep` to `Auto`. (In my research, setting this to either `On` or `Off` breaks Sleep in macOS.)
   
   Set `Built-In Device Options` > `Wake on LAN` to `Disable`. (In my research, setting this to `Boot to Network` or `Follow Boot Order` also breaks sleep in macOS.)
   
   ### You will also need to
   
   | Enable | Disable |
   | ------ | ------- |
   | `Boot Options` > `Audio alerts during boot` | `Boot Options` > `Display Diagnostic URL` |
   | `Boot Options` > `Custom Logo` (See: **"What if I want to use a custom boot logo?"**) | `Boot Options` > `Custom Help and URL message` |
   | `Boot Options` > `Require acknowledgement of battery errors` | `Boot Options` > `Fast Boot` |
   | `Boot Options` > `CD-ROM boot` | `Boot Options` > `Floppy Boot` |
   | `Boot Options` > `SD card boot` | `Boot Options` > `HP Application` |
   | `Boot Options` > `PXE Internal NIC, IPV4 NIC, IPV6 NIC Boot` | `Boot Options` > `SecureBoot Configuration` > `SecureBoot` |
   | `Boot Options` > `USB device boot` | `Boot Options` > `SecureBoot Configuration` > `Clear SecureBoot Keys` |
   | `Boot Options` > `eSATA boot` | `Device Configurations` > `USB legacy support` |
   | `Boot Options` > `Customized Boot` | `Device Configurations` > `Fan Always on while on AC Power` |
   | `Device Configurations` > `Data Execution Prevention` | `Device Configurations` > `Wake on USB` |
   | `Device Configurations` > `USB Charging port` | `Device Configurations` > `Trusted Execution Technology (TXT)` |
   | `Device Configurations` > `Virtualization Technology (VTx)` | `Device Configurations` > `HP Hypervisor` |
   | `Device Configurations` > `Virtualization Technology for Directed I/O` | `Device Configurations` > `Power on unit when AC is detected` |
   | `Built-in Device Options` > `Wireless Button State` | `Built-in Device Options` > `Modem Device` |
   | `Built-in Device Options` > `Embedded WLAN Device` | `Built-in Device Options` > `Power on unit when lid is opened` |
   | `Built-in Device Options` > `Embedded LAN Controller` |
   | `Built-in Device Options` > `LAN/WLAN Switching` |
   | `Built-in Device Options` > `Notebook Upgrade Bay` |
   | `Built-in Device Options` > `Integrated Camera` |
   | `Built-in Device Options` > `Power Monitor Circuit` |
   | `Built-in Device Options` > `Audio Device` |
   | `Built-in Device Options` > `Microphone` |
   | `Built-in Device Options` > `Speakers and Headphones` |
   | `Built-in Device Options` > `Wake unit from sleep when lid is opened` |
   | `Built-in Device Options` > `Boost Converter` |
   | `Port Options` > `Flash media reader` |
   | `Port Options` > `USB Port` |
   | `Port Options` > `Express Card Slot` |
   | `Port Options` > `eSATA Port`|
   
   But now, with that out of the way:

## **Hardware**

If you have **ANY** different hardware than what's listed in the hardware table below, make sure to change Kexts, SSDTs, Drivers, DeviceProperties and config.plist patches accordingly, see: **"What if I'm not familiar with the files in the EFI?"**

If you don't know what hardware you have, you probably shouldn't even be here, but see: **"What if I don't know what hardware I have?"**

| Hardware | Model |
| --- | -------------------|
| CPU | Intel Core i7-3520 |
| Microarchitecture | Ivy Bridge |
| iGPU | Intel HD Graphics 4000 |
| Chipset | Intel 7 Series - Mobile Intel QM77 Express with vPro Support |
| Ethernet | Intel 82579LM |
| WiFi | Intel Centrino Advanced-N 6205 AGN 2x2 HMC |
| Audio Codec | IDT 92HD81B1X |
| Webcam | Integrated HP Webcam [Fixed] |
| SD Card Reader | JMicron SD/MMC Reader |
| Trackpad/Trackpoint | PS/2 Connected |
| Keyboard | PS/2 Connected |
| BIOS Version | F.73 Revision A |

## What's Working

| Fully Working | Not Tested |
| ------------- | ---------- |
| iGPU Acceleration (Patched) | SIM Card Slot |
| Brightness controls | Express Card Slot (It is detected, but I have nothing to test it with. However, I did map the ports for future use.) |
| CPU Power Management with XPCM & correct P & C-States | Smart Card Reader (Also detected, but I have nothing to test it with. However, it does show in macOS settings when activated in the BIOS.) |
| Emulated NVRAM | RJ-11 (Telephone line) port. (I'm guessing it would be the same situation as the modem devices though.) |
| Battery Percentage & Status (Charging, not charging, etc.) | DisplayPort (I don't currently have a way of testing it, but it should work.) |
| Brightness, Volume & Sleep shortcut keys (`fn+f3`, `fn+f6`, `fn+f7`, `fn+f9`, `fn+f10`) | WWAN/GPS (I tried using a card I have that came with the laptop and the BIOS told me it wasn't supported, so I'm not sure it'll work on other cards.) |
| WiFi, Power, Charger & Hard Drive activity LEDs |
| Sleep |
| Lid Switch |
| Trackpad |
| Webcam |
| Audio out (Speakers & 3.5mm Headphone/Microphone Combo Jack) |
| Audio in (Integrated Mic) |
| USB Ports |
| CD-ROM |
| WiFi, both 2.4GHz and 5GHz |
| Ethernet |
| Boot Chime |
| AirPlay to Mac |
| Content Caching |
| iMessage, FaceTime, App Store, iCloud, etc. |
| Webcam eye tracking |
| Software Updates |
| Recovery |

## Known Issues
- WiFi and Ethernet are sometimes spotty in recovery, I recommend using the full installer which you can grab from a real Mac by using [MIST](https://github.com/ninxsoft/Mist/releases) (Requires ≤ macOS Monterey), or using Munki's InstallInstallMacOS utility, see [Making the installer in macOS](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#downloading-macos-modern-os).  
- USB transfers can sometimes be slow. It seems to happen on other hackintoshes too, though.
- Neither pressing keyboard keys or using the trackpad wakes the laptop from sleep, but pressing the power button once does.
- The macOS loading bar is wonky for the first few seconds of booting when not in `UEFI Hybrid (With CSM)` mode.
- Boot chime volume is rather low, but I have not been able to figure out how the new UEFI Audio settings work.
- Webcam image is pretty dark.
- Some keyboard keys have weird behavior, such as `fn+Right arrow key` & `fn+Left arrow key` which also change brightness in addition to `fn+f9` & `fn+f10`. Seems to be related to VoodooPS2Controller.
 - You can use the power button to power on the laptop initally, but once it's turned on, pressing the button once quickly does not make it go to sleep. If you hold it down for about 1.5-2 seconds and then let go, it does go to sleep, and will wake up from a quick press. If you hold it down for 5 seconds, it will still shut down the laptop as normal.
- The supplemental battery information, like cycle count, temperature, etc., hasn't been patched in yet.
- The eSATA Port has been tested as a normal USB 3.0 port and it works fine, but haven't been able to test it as an actual eSATA port.
- The WiFi Off/On physical shortcut button does work for disabling WiFi, but messes with it in a weird way which makes you have to turn WiFi off and on again manually in macOS to fix it and be able to reconnect, which kind of defeats the purpose.
- The browser physical shortut button does nothing.
- The Mute/Unmute Audio physical shortcut button works fine, but the light stays orange no matter what state audio is in, and turns white when sleep is activated.
- The WiFi card should be replaced with a WiFi + Bluetooth card for Airdrop, Continuity, etc. support.
- No DRM support for iGPU in macOS Monterey + a dGPU is needed, see [Fixing DRM Support and iGPU Performance](https://dortania.github.io/OpenCore-Post-Install/universal/drm.html).
- No VGA support in macOS Monterey.
- No Airdrop, Handoff, Sidecar, etc. because of no Bluetooth.
- Track Point isn't working. This seems to be caused by VoodooPS2Controller. In my testing, Keyboard and Trackpad work decently when using RehabMan's 2018 version of VoodooPS2, but not the Track Point. If I switch over to the newer VoodooPS2Controller which uses VoodooInput, the Track Point works, but nothing else.
- Modem devices are detected, but have no support in macOS.
- The SD Card Reader is detected, but not working.
- The disable trackpad shortcut (Double tapping the top left corner of the touchpad) does nothing, also seems to be related to VoodooPS2Controller.
- Fingerprint scanner does not work as there is currently no way to emulate Touch ID, see [Hardware Limitations](https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html#miscellaneous).

## **Instructions**

1. Test, test, test. I ***DO NOT*** recommend placing this EFI directly on your main hard drive EFI partiton without swapping from RELEASE to DEBUG and test booting from USB first. If you need guidance on how to test boot from USB, see: **"What if I don't know how to test an EFI folder?"**
2. Once tested and confirmed working, you will need to download [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases) to patch the Intel HD Graphics 4000 iGPU to get graphics acceleration working. If you need guidance on how to do this, see: **"What if I don't know how to use OpenCore Legacy Patcher?"**
3. Once fully patched, you can swap back from `DEBUG` to `RELEASE`.

## "What if...?"

| "What if...?" | Answer |
| ------------- | ------ |
| "What if I need > macOS Monterey?" | You can either use macOS Monterey for now, and wait until I make an EFI for other versions, (Which I do currently plan on doing, but things like this take a long time to complete and troubleshoot, and also take into consideration that I have to document everything.) or make your own EFI from scratch. |
| "What if I don't know how to enable emulated NVRAM?" | Starting with OpenCore 0.8.3, emulated NVRAM is now under it's own separate driver, OpenVariableRuntimeDxe.efi, which depends on a couple of things to actually work: OpenRuntime.efi has to be specified after OpenVariableRuntimeDxe.efi in the `Drivers` list, OpenVariableRuntimeDxe.efi needs to have `LoadEarly` set to `True`, and OpenRuntime.efi also has to have `LoadEarly` set to `True` for the correct operation of `RequestBootVarRouting` when using both OpenRuntime.efi and OpenVariableRuntimeDxe.efi. So, yeah, a couple of things, but usually only setting `LoadEarly` to `True` for both OpenRuntime.efi and OpenVariableRuntimeDxe.efi does the trick, at least for me.
| "What if I don't know how to use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)?" | You first will want to download [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) and extract it, then run `GenSMBIOS.py`. Choose option `1` to install macserial, then option `3` to generate your SMBIOS info. Type `MacBookPro10,1` then press enter, and you should get all the info you need to place in your config.plist. For more info, see: [PlatformInfo](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/ivy-bridge.html#platforminfo).
| "What if I want to enable SIP?" | Due to the use of [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases), which requires SIP to be off and stay off, we cannot re-enable it without completely breaking booting. I know, because I tried. This also ties into breaking booting, as SIP is disabled in NVRAM, and so if NVRAM were to stop working, SIP would turn on and destroy everything. |
| "What if I want to enable Secure Boot?" | You can't really enable Secure Boot with the `MacBookPro10,1` SMBIOS we are using, as Secure Boot was not a feature of that SMBIOS.|
| "What if I want to remove the boot arg?" | We use the boot arg `-no_compat_check` so that macOS doesn't check for board compatibility with our SMBIOS. This also ties into breaking booting, because of the boot arg being NVRAM dependent. If NVRAM were to stop working or the boot arg were to be removed, we would be either unable to boot or greeted with a forbidden symbol when booting. We use the `MacBookPro10,1` SMBIOS because it was written for Ivy Bridge, which helps use less energy and produce less heat than if we were using the `MacBookPro11,1` SMBIOS, which was written for Haswell. This also has the added benefit of giving us the correct P and C-States for our processor. While you *can* use a different SMBIOS, `MacBookPro10,1` is the best one for this laptop.|
| "What if my screen shows diagonal garbled lines on boot?" | You didn't set `Boot Options` > `Boot Mode` to `UEFI Hybrid (With CSM)`. Don't worry about the lower resolution, this is only to fix the inital boot and installation process. CSM support is needed for VESA graphics mode (No iGPU acceleration) in macOS Monterey or you will get garbled graphics when booting either into recovery or macOS, as it does not have native support for Intel HD Graphics 4000 without using patches. After patching the iGPU with [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases) you can set this to `UEFI Native (Without CSM)`. |
| "What if I want to use a custom boot logo?" | All you need to do is rename your EFI partition to `HP_TOOLS` and place the Hewlett-Packard folder into that same partition. You can change the provided Apple logo in the Hewlett-Packard folder if you want to use a different one. |
| "What if I'm not familiar with the files in the EFI?" | Check the *EFI Files Explained* section below. |
| "What if I don't know what hardware I have?" | Well, honestly you shouldn't even try to use this EFI, but see: [Finding your Hardware](https://dortania.github.io/OpenCore-Install-Guide/find-hardware.html). |
| "What if I don't know how to test an EFI folder?" | The first thing you should do is download OpenCore 0.8.5 `DEBUG` and replace the files in the EFI with the ones in the OpenCore download. The main ones to replace are listed in the *Debugging File Swaps* table below. You can then enable debugging settings, see: [OpenCore Debugging](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/debug.html#config-changes) and [System Debugging](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/kernel-debugging.html#config-plist-setup). Now all you have to do is put that new debugging-enabled EFI on a USB and boot! You can check what could be causing problems or panics when trying to boot, and the logs should save to your USB drive. |
| "What if I don't know how to use [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases)?" | After installing macOS Monterey, you'll notice that you have no iGPU acceleration and everything chugs along *very* slowly. To fix this, we download [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases), run it, re-launch as root, click on `Post Install Root Patch`, click on `Apply Patches`, and wait for the prompt to reboot. After rebooting, iGPU acceleration should now be fully working. If you update through Software Update this patch will be removed and you will have repeat this process. Note that [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases) automatically detects when the patches have been removed and will prompt you to patch again. You will also have to switch back to `UEFI Hybrid (With CSM)` so you can see what's on the screen. |

## EFI Files Explained

### ACPI/SSDTs

| Name | Function |
| ---- | -------- |
| SSDT-AC | This patch attaches an AC Adapter Device that already exists in the DSDT to the `AppleACPIACAdapter` service in the IORegistry of macOS. This is optional and mostly cosmetic – it doesn't make any difference in terms of functionality, see: [Emulate a layer of AppleACPIACAdapter](https://github.com/acidanthera/bugtracker/issues/1808). |
| SSDT-ALS0 | Starting with macOS Catalina, laptops either require a fake ambient light sensor device `(ALS0)` or, if the laptop has one, `(ALSD)` needs to be enabled for macOS to store the current brightness/auto-brightness level. Otherwise, the brightness level returns to the maximum level after every reboot. For us, this patch adds `(ALS0)` as we do not have an ambient light sensor. |
| SSDT-EC | On laptops, the `EC` is used for enabling hotkeys and correct battery management. The problem is our `EC`'s name isn't compatible, so we will create a simple "fake" `EC` device that will satisfy macOS. |
| SSDT-FHWD | Adds a virtual `FWHD` device to the IORegistry in macOS. Research of DSDT and `.ioreg` files shows that the `Intel Firmware Hub Device` is present in almost every Intel-based Mac. It's listed in IORegistryExplorer as `FWHD` with the HID `INT0800`. |
| SSDT-HPET | Sound cards on older systems (Ivy Bridge, for example) require `High Precision Event Timer` (`HPET PNP0103`) to provide interrupts `0` and `8`, otherwise the sound card won't work, even if AppleALC is present and the correct `layout-id` is used. In most cases, almost all machines have `HPET` without any interrupts. Usually, interrupts `0` & `8` are occupied by `RTC PNP0B00` or `TIMR PNP0100` respectively. To solve this issue, we need to fix `HPET`, `RTC` and `TIMR` simultaneously. However, the issue can occur on newer platforms as well. This is due to the fact that `HPET` is a legacy device from Intel's 6th Generation platform and is only present for backward compatibility with older Windows versions. If you use a ≤ 7th Generation Intel Core CPU with ≤ Windows 8.1, `HPET` is no longer present in Device Manager. In macOS 10.12 and newer, if the problem occurs on 6th Generation Intel, `HPET` can be blocked directly to solve the problem. This patch fixes `HPET` to enable sound. |
| SSDT-LANC & SSDT-UPRW | macOS will instantly wake if ethernet or power states change while sleeping. To fix this, we need to reroute the `LANC` & `UPRW` calls to a new SSDT, which is what this patch does. |
| SSDT-PNLF| This patch creates a `PNLF` device for macOS to use for brightness controls, specifically a `PNLF` device with a hardware ID of `APP0002`. |
| SSDT-XCPM | Apple deactivated the `X86PlatformPlugin` support for Ivy Bridge in macOS a few years back. Instead, the `ACPI_SMC_PlatformPlugin` is used for CPU power management, although XCPM is supported by Ivy Bridge CPUs natively. This patch brings back XCPM power management. |

### Patches

| Name | Function |
| ---- | -------- |
| BoardIDSkip + VMM | Enables booting macOS Monterey with an unsupported Board ID and adds patches that [OpenCore Legacy Patcher](https://github.com/dortania/Opencore-Legacy-Patcher/releases) uses to trick macOS into thinking it's running on a virtual machine. |
| Enable XCPM | Patches for enabling XCPM power management, used with SSDT-XCPM. |
| IRQ-HPET | Patches for `HPET` to fix sound issues, used with SSDT-HPET. |
| UPRW & LANC Rename | Patches for fixing power state & ethernet changes instantly waking macOS. |

## Debugging File Swaps
| EFI > BOOT | EFI > OC | EFI > OC > Drivers |
| ---------- | -------- | ------------------ |
| BOOTx64.efi | OpenCore.efi | OpenRuntime.efi | 
|             |              | OpenVariableRuntimeDxe.efi |
|             |              | OpenCanopy.efi             |

## Thank You!

- [acidanthera](https://github.com/acidanthera) for [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), [AppleALC](https://github.com/acidanthera/AppleALC), [MaciASL](https://github.com/acidanthera/MaciASL), [VoodooPS2](https://github.com/acidanthera/VoodooPS2), [FeatureUnlock](https://github.com/acidanthera/FeatureUnlock), [WhateverGreen](https://github.com/acidanthera/WhateverGreen), [Lilu](https://github.com/acidanthera/Lilu), [VirtualSMC](https://github.com/acidanthera/VirtualSMC), [IntelMausi](https://github.com/acidanthera/IntelMausi), [gfxutil](https://github.com/acidanthera/gfxutil), and [macserial](https://github.com/acidanthera/macserial).
- [RehabMan](https://github.com/RehabMan) for the various patches he made.
- [corpnewt](https://github.com/corpnewt) for [ProperTree](https://github.com/corpnewt/ProperTree), [SSDTTime](https://github.com/corpnewt/SSDTTime), [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), and [BootChime](https://github.com/corpnewt/BootChime).
- [Dortania](https://github.com/dortania) for [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).
- [5T33Z0](https://github.com/5T33Z0) for [OC Little Translated](https://5t33z0.gitbook.io/oc-litte-translated/), from which many fixes were created for this laptop.
