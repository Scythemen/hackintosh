
# XPS

- DELL XPS 13 9360
- Intel Core i7-8550U @ 1.80GHz 4 cores (Kaby Lake R)
- Intel(R) UHD Graphics 620
- RAM Micron 8 GB 1867MHz
- Samsung PM971 BGA NVMe SSD 256 GB (M.2 PCIe)
- Screen Sharp SHP1449 13.3' FHD, 1920 * 1080
- WIFI & Bluetooth: replace `Atheros Killer Wireless Network Adapter` with`DW1560 BCM 94352ZAE_3`
- Audio/Mic: Realtek ALC256/ALC3246 
- BIOS Ver. 2.21.0
- Video bios version : 1059 memory : 64M

CPU Details: https://en.wikipedia.org/wiki/Kaby_Lake#List_of_8th_generation_Kaby_Lake_R_processors



# OpenCore

## Ver. 0.8.8 + BigSur

Every thing works fine except:
- SD Card
- UVC Camera VendorID_3141 ProductID_26380.
see: https://github.com/theQuert/XPS-9360-macOS/issues/64

## Ver. 0.8.8 + Monterey
AirDrop only works on one way.


# Install

See: https://dortania.github.io/OpenCore-Install-Guide/


## BIOS Setting

### settings

settings/general/boot sequence:
	UEFI, checked

settings/general/advanced boot options:
	enable legacy options ROMs, checked
	
settings/system configuration/SATA operation:
	AHCI, checked
	
settings/system configuration/drivers:
	SATA-2, checked
	M2 PCIe-ssd-0, checked

settings/system configuration/smart reporting:
	enable, checked

settings/system configuration/USB configuration:
	enable USB boot support, checked
	enable extenal usb port, checked

settings/system configuration/dell type-c dock configuration:
	disable
	
settings/system configuration/thunderbolt adapter configuration:
	disabel thunderbolt technology support 
	disable adapter boot support
	disable pre-boot modules
	security level no security ,checked
	
settings/system configuration/usb power share, checked

settings/system configuration/audio: checked all

settings/system configuration/ unobtrusive mode: unchecked

settings/system configuration/miscellaneous devices:
	camera, checked, disable the others.
	

### security

security/cpu xd support: unchecked

security/OROM keyboard access, disable

security/SMM security Mitigateion: unchecked


### security boot

security boot/security boot , disable

security boot/Expert key management: unchecked

### intel software 

intel software guard extensions/SGX :disable


### performance

performance/ multi core support: all

performance/ intel speed step: checked

performance/ c-states control: checked

performance/ interl turbo boost: unchecked

performance/ hyper thread control: enable


### power management

power management/ ac behavior: unchecked

power management/ usb wake support/ wake on dell usb-c dock: unchecked 
	 

### post behavior

post behavior/ fastboot: minimal

post behavior/ extend bios post time: 0 seconds


### virtualization support

virtualization support/virtualization: enable ,checked

virtualization support/vt for direct I/O: enable , checked


### support assist system

support assist system resolution/ auto OS recovery threshold: off
	
support assist system resolution/ support assist OS recovery: unchecked
	 



## Make the Installer

- Download the recovery image and make the installer with USB Flash Drive.
https://dortania.github.io/OpenCore-Install-Guide/installer-guide/

- Follow the instruction make the EFI
**the most important is unlock the cfg-lock.**
Config `config.plist`, enable `AppleCpuPmCfgLock` and `AppleXcpmCfgLock` under `Kernel -> Quirks` for installing.
https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#what-is-cfg-lock

- Use the tool [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools) to config `config.plist` and generate the SMBIOS.

- Install BigSur...
- Format disk
Diskutil -> show all devices -> select SSD:
set Scheme：GUID, ....format：APFS
format...



# Post Install

## Copy EFI 
Copy the EFI folder from USB Flash Drive to SSD 

```bash
# show 
$ diskutil list

# mount then copy
$ sudo diskutil mount /dev/disk0s1

```


## Disable Hibernate

set boot-args: darkwake=0

https://dortania.github.io/OpenCore-Post-Install/usb/misc/keyboard.html#method-3-configuring-darkwake

```bash
# disable hibernate
sudo pmset -a hibernatemode 0
sudo rm -rf /var/vm/sleepimage
sudo mkdir /var/vm/sleepimage
sudo pmset -a standby 0
sudo pmset -a autopoweroff 0

# check wakeup schedule
sudo pmset schedule cancelall

```

## Fix Audio
https://dortania.github.io/OpenCore-Post-Install/universal/audio.html

test:
NVRAM > ADD > 7C436110-AB2A-4BBB-A880-FE41995C9F82 > boot-args
add: alcid=11


install `ComboJack for ALC`
https://github.com/hoaug-tran/ComboJack


## iGPU

https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#what-is-cfg-lock

### DVMT
Dynamic video memory technology (DVMT) 

Use the tool `ControlMsrE2.efi` to check the value of cfg-lock.
Use the tool `DVMT.efi` to set the value of cfg-lock.


the default values:
```
setup_var 0x4de 0x01
setup_var 0x785 0x01
setup_var 0x786 0x01
```

change to:
 ```
 setup_var 0x4de 0x00  // Disable CFG Lock
 setup_var 0x785 0x06  // Increase DVMT pre-allocated size to 192M For FHD version, 
                       // it's also recommended setting to 192M
 setup_var 0x786 0x03  // Increase CFG Memory to maximum
 ```


### External Monitor 4K UHD Display Support

XPS 9360 i7-8550U UHD 620 can only output 4K 30Hz by the USB-C port. We need to transform the DP output to HMDI-1.4 by the LSPCON driver. By doing this, in the `DeviceProperties` of `config.plist`, set a `fake id 16590000` and the `framebuffer`.
The parameters of `DeviceProperties` can be generated by [Hackintool](https://github.com/benbaker76/Hackintool).


**Thanks to**
https://github.com/theQuert/XPS-9360-macOS
https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/kaby-lake.html#deviceproperties
https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md
https://github.com/vulgo/IORegistryExplorer
https://github.com/acidanthera/gfxutil