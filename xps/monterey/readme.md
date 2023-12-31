
# Monterey 12.6.7 (21G651)

Thanks to https://github.com/theQuert/XPS-9360-macOS

- **OpenCore**: 0.8.8, all drivers update to date

- **Laptop**:
    - DELL XPS 13 9360
    - Intel Core i7-8550U @ 1.80GHz 4 cores
    - Intel UHD 620
    - RAM Micron 8 GB 1867MHz
    - Samsung PM971 BGA NVMe SSD 256 GB 
    - Screen Sharp SHP1449  
    - WIFI & Bluetooth: replace `Atheros Killer Wireless Network Adapter` with `DW1560 BCM943527`
    - Audio/Mic: Realtek ALC256 

Every thing works fine except:
- SD Card

# How to

- BIOS setting https://github.com/theQuert/XPS-9360-macOS#bios-settings
- DVMT setting
  
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

- Use the tool [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools) to config `config.plist` and generate the SMBIOS. 

Set: 
```
Misc/Security/SecurityBootModel = disable
nvram/add/....f82/csr-active-config = 00000000
```

- Install...
- After installing Monterey. Audio setting 
see: https://github.com/hoaug-tran/ComboJack
- Go back to `config.plist`, set 
```
Misc/Security/SecurityBootModel = Default
nvram/add/....f82/csr-active-config = E7030000
```

# External Monitor 4K UHD Display Support

XPS 9360 i7-8550U UHD 620 can only output 4K 30Hz by the USB-C port. We need to transform the DP output to HMDI-1.4 by the LSPCON driver. By doing this, in the `DeviceProperties` of `config.plist`, set a `fake id 16590000` and the `framebuffer`.
The parameters of `DeviceProperties` can be generated by [Hackintool](https://github.com/benbaker76/Hackintool).


More info:
https://github.com/theQuert/XPS-9360-macOS#dev-notes
https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/kaby-lake.html#deviceproperties
https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md
https://github.com/vulgo/IORegistryExplorer
https://github.com/acidanthera/gfxutil


 