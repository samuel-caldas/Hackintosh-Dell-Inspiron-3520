# macOS High Sierra (10.13.6) on Dell Inspiron 3520

Clover EFI configuration, patched ACPI files, and post-installation drivers for Dell Inspiron 3520 running macOS High Sierra 10.13.6.

## Previews
<p align="center">
  <img src="images/img1.png" alt="Desktop Screenshot" width="48%" />
  <img src="images/img2.png" alt="About This Mac" width="48%" />
</p>

---

## Hardware Specifications
| Component | Specification | Status |
| :--- | :--- | :--- |
| **CPU** | Intel Core i5-3230M @ 2.60 GHz (Ivy Bridge) | Supported (Native Power Management / SpeedStep via `PluginType=1`) |
| **GPU** | Intel HD Graphics 4000 (1536 MB) | Supported (Full QE/CI via WhateverGreen) |
| **RAM** | 8 GB DDR3 1600 MHz | Supported |
| **Storage** | 500 GB HDD / SATA SSD | Supported |
| **USB 3.0 / 2.0** | Intel 7-Series Panther Point (USB 3.0 SuperSpeed + Power Injection) | Supported (Native 5 Gbps via `FakePCIID_XHCIMux` + `SSDT-USBX`) |
| **Wi-Fi + BT** | Broadcom BCM943225HM (Half Mini PCIe) | Supported (via `Post-Install/WiFi-BCM943225HM`) |
| **Audio** | Cirrus Logic CS4213 | Supported (VoodooHDA 2.8.9) |
| **Touchpad** | ALPS Trackpad (2 fingers scrolling) | Supported (via `Post-Install/Trackpad-ALPS`) |
| **Webcam** | Integrated Webcam | Supported |
| **Sleep / Wake** | Native S3 (Instant Wake fixed via `SSDT-GPRW`) | Supported |
| **Microphone** | Internal Mic | Not working / Needs tuning |
| **HDMI Video** | HDMI Output | Untested |

---

## Repository Structure
```
Hackintosh-Dell-Inspiron-3520/
├── EFI/                              # Drop-in EFI folder for EFI partition
│   ├── BOOT/
│   │   └── BOOTX64.efi               # UEFI Bootloader executable
│   └── CLOVER/
│       ├── ACPI/patched/             # Patched ACPI tables:
│       │   ├── DSDT.aml              # Patched system DSDT
│       │   ├── SSDT-PNLF.aml         # Display backlight injection
│       │   ├── SSDT-USBX.aml         # USB bus power supply and current limits
│       │   └── SSDT-GPRW.aml         # Eliminates instant wake from sleep
│       ├── config.plist              # Tuned Clover config with GPRW patch & USB HighCurrent
│       ├── drivers64UEFI/            # UEFI drivers (AptioMemoryFix, HFSPlus, APFS, etc.)
│       ├── kexts/Other/              # Core kexts (Lilu, WhateverGreen, FakePCIID_XHCIMux, etc.)
│       ├── misc/
│       ├── themes/
│       └── tools/
├── DSDT/                             # Original ACPI source files for reference
│   ├── DSDT.aml
│   └── SSDT-PNLF.aml
├── Post-Install/                     # Device-specific post-install packages
│   ├── Audio-VoodooHDA/              # VoodooHDA 2.8.9 kext and prefPane
│   ├── Keyboard-VoodooPS2/           # VoodooPS2 controller and daemon
│   ├── Trackpad-ALPS/                # ALPS trackpad prefPane, plugin & kext
│   └── WiFi-BCM943225HM/             # Broadcom WiFi injector kexts
├── Tools/                            # Essential macOS diagnostic & editing tools
│   ├── Clover Configurator.app
│   ├── IORegistryExplorer.app
│   ├── Karabiner-Elements-12.1.0.dmg
│   ├── MaciASL.app
│   ├── PlistEdit Pro.app
│   └── iasl.zip
└── images/                           # Screenshots and assets
```

---

## Installation Guide

### Prerequisites
- Dell Inspiron 3520 with UEFI support.
- BIOS Settings:
  - Disable **Secure Boot**.
  - SATA Operation set to **AHCI**.
- 8GB+ USB Flash Drive.
- macOS High Sierra 10.13.6 installer application (`Install macOS High Sierra.app`).

### 1. Create macOS High Sierra USB Installer
Format your USB drive as `Mac OS Extended (Journaled)` with `GUID Partition Map` and name it `install_osx`.
Run in Terminal:
```sh
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/install_osx --applicationpath /Applications/Install\ macOS\ High\ Sierra.app --nointeraction
```

### 2. Install Clover EFI
1. Mount the EFI partition of your USB drive using Clover Configurator (available in [`Tools/`](Tools/)).
2. Copy the entire [`EFI/`](EFI/) directory from this repository directly into the root of the EFI partition.
3. Boot the laptop from the USB drive and proceed with macOS installation.

### 3. Post-Installation Setup
After booting into the installed macOS system:

1. **Copy EFI to Internal Disk:**
   Mount the EFI partition of your internal SSD/HDD and copy the [`EFI/`](EFI/) folder to it.

2. **ALPS Trackpad:**
   The files are located in [`Post-Install/Trackpad-ALPS/`](Post-Install/Trackpad-ALPS/):
   - Install `Trackpad.prefPane` into `/System/Library/PreferencePanes/`.
   - Place `IOHIDKeyboardFilter.plugin` in `/System/Library/HIDPlugins/`.
   - Use `Touchpad Settings.app` to customize gesture options.
   - Rebuild kext cache and repair permissions using Kext Utility.

3. **Audio (Cirrus Logic CS4213):**
   Extract archives in [`Post-Install/Audio-VoodooHDA/`](Post-Install/Audio-VoodooHDA/):
   - Install `VoodooHDA.kext` (v2.8.9) to `/Library/Extensions`.
   - Install `VoodooHDA.prefPane` to `/Library/PreferencePanes`.

4. **Wi-Fi (BCM943225HM):**
   If using the Broadcom BCM943225HM mini PCIe card, install the kexts in [`Post-Install/WiFi-BCM943225HM/`](Post-Install/WiFi-BCM943225HM/) to `/Library/Extensions` or keep them injected via Clover.

5. **Function Keys & Keyboard Mapping:**
   - Install Karabiner Elements from [`Tools/Karabiner-Elements-12.1.0.dmg`](Tools/) or [Karabiner-Elements GitHub](https://github.com/tekezo/Karabiner-Elements).
   - Shortcut mapping:
     - `F1`: Brightness Down
     - `F2`: Brightness Up
     - `F3`: Mission Control
     - `F4`: Launchpad
     - `F5`: Keyboard Backlight Down
     - `F6`: Keyboard Backlight Up
     - `F7`: Media Rewind
     - `F8`: Media Play / Pause
     - `F9`: Media Fast Forward
     - `F10`: Mute
     - `F11`: Volume Down
     - `F12`: Volume Up

---

## Advanced Hardware Notes
- **USB 3.0 Power Management & Routing:**
  - Uses `FakePCIID_XHCIMux.kext` to route USB 2.0 companion ports on USB 3.0 connectors to `EH01`/`EH02`, allowing `XHC` to operate under the 15-port limit with native 5 Gbps SuperSpeed.
  - `SSDT-USBX.aml` injects standard power properties (`kUSBSleepPowerSupply=2600mA`, `kUSBWakePowerSupply=3200mA`, `kUSBSleepPortCurrentLimit=2100mA`, `kUSBWakePortCurrentLimit=2100mA`) for high-current device charging and bus stability.
- **Sleep / Wake Fix (`SSDT-GPRW`):**
  - ACPI patch intercepts `GPRW(0x6D, 0x04)` calls from USB/LAN to avoid immediate wake when entering sleep state S3.
- **Native CPU SpeedStep:**
  - Handled via Clover's `PluginType=1` and generated C-States/P-States for `X86PlatformPlugin`.

---

## Credits & Acknowledgements
- [RehabMan](https://github.com/RehabMan) for DSDT patches, VoodooPS2, and kext tools.
- [Acidanthera](https://github.com/acidanthera) for Lilu and WhateverGreen.
- [Niemtin007](http://niemtin007.blogspot.com/)
- [Hackintosh - The OS X on PC World](https://www.facebook.com/groups/hackintoshPC/)
- [Hieu - Admin Hackintosh Facebook Page](https://www.facebook.com/cobaohieu)
- [Thang Duong](https://www.facebook.com/thangduong.dev)
- [BADRUZEUS SHAVA - Hackintosh Indonesia](https://www.facebook.com/badruzeus)
- Original repository by [thuanvoit](https://github.com/thuanvoit/Hackintosh-Dell-Inspiron-3520)
