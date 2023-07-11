# Big Sur  11.7.8

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
- UVC Camera VendorID_3141 ProductID_26380.
see: https://github.com/theQuert/XPS-9360-macOS/issues/64


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
- Install...
- Then Audio setting 
see: https://github.com/hoaug-tran/ComboJack


