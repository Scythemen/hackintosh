# hackintosh

Install macOS with OpenCore on PC. A way to reuse your old computer.


## Components of PC:
- Asus Prime B450M-K
- AMD Ryzen 5 2600, of 6 cores
- Tigo DDR4 2666 MHz 8GBx1
- Western digital, WDC WDS480G2G0B-00EPW 480GB
- GALAX Nvidia GeForce GT 1030, 2GB, Pascal architecture
- Realtek ALC887 High Definition Audio Driver Controller
- RTL8168/8111/8112 Gigabit Ethernet Controller


## BIOS Settings

BIOS firmware Ver. 4002

**Disable**:
- Boot -> Boot Configuration -> Fast boot
- Advanced -> Onboard Devices Configuration -> Serial Port Configuration -> Serial/COM Port
- Advanced -> AMD CBS -> IOMMU



**Enable**:
- Advanced -> USB configuration -> EHCI/XHCI Hand-off
- Advanced -> PCI subsystem settings -> Above 4G Decoding
- Secure Boot -> OS Type: Other
- Advanced -> SATA configuration -> SATA mode selection : AHCI


## Notes:

- [Follow the instruction](https://dortania.github.io/OpenCore-Install-Guide)

- Edit `config.plist` with [OC Auxiliary Tools](https://github.com/ic005k/OCAuxiliaryTools).
**Generate your own SerialNumber/MLB/SystemUUID/ROM** .
 (Go to `PlatformInformation -> Generic`)

- Print the debug logs by setting [boot-args](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html#nvram) if you want.

- [Making the installer](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) with a USB-driver.  

- During the installation, **do not login your AppleID**, the PC will reboot several times, **in every reboot, you must do these manually:**
    - boot from USB-driver.
    - select `macOS installer` in the boot menu to continue the process.
    - until it finish(when? no `macOS installer` showing in the menu).

- If failing/stucking in the process, just clean/reset the NVRAM, then go on. 

- when it finished, copy `EFI` from USB-driver to the disk of macOS, then you can reboot the PC without USB-driver. 
```shell
# show the list, get the location of EFI partition, normally disk0s1 
diskutil list  

# mount 
sudo diskutil mount disk0s1

# then coppy & replace the EFI
 
```

- Install graphic card driver.
    - Prepare and set the `boot-args` with parameters: `amfi` `nvda_drv` etc.
    - Download [OpenCore-Legacy-Patcher](https://dortania.github.io/OpenCore-Legacy-Patcher), run the App and click `Post-install Root Patch` to get the available patches, then `Start Root Patching`.
    - If network error, check the logs of `OpenCore-Legacy-Patcher`, find the url of `Kernel_Debug_Kit_xxxxxx.dmg`, download and install it by yourself, then retry.

- Test the sound audio by changing the `alcid` value in `boot-args`.


## Logs:

v1.1

`tested`: Ventura 13 & OpenCore 0.9.3

`troubles`: so far so good

----
v1.0

`tested`: Catalina 10.15 & OpenCore 0.6.3

`troubles`: Incompatible GPU driver
 