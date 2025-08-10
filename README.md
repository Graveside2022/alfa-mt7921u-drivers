# MT7921U Driver Package for Alfa WiFi Cards

**Extracted from:** Raspberry Pi 4 - Ubuntu 24.04 ARM64  
**Kernel Version:** 6.8.0-1031-raspi  
**Device ID:** 0e8d:7961 (MediaTek MT7921U)  
**Date:** 2025-08-10

## Directory Structure

```
alfa-mt7921u-drivers/
├── modules/           # Kernel driver modules (.ko and .ko.zst)
├── firmware/          # MediaTek firmware binaries (.bin and .bin.zst)
├── info/             # Module information files (.info) 
├── config/           # Modprobe configuration files
├── deps/             # Dependency tree documentation
└── README.md         # This file
```

## Core Driver Modules

### Main Driver
- `mt7921u.ko` - MT7921U USB WiFi driver (66,401 bytes)

### Dependencies (Load Order)
1. `mt76.ko` - Base MT76 driver framework (262,777 bytes)
2. `mt76-usb.ko` - MT76 USB interface support (86,969 bytes)
3. `mt76-connac-lib.ko` - MT76 connectivity library (211,841 bytes)
4. `mt792x-lib.ko` - MT792X family library (138,385 bytes)
5. `mt792x-usb.ko` - MT792X USB support (44,225 bytes)
6. `mt7921-common.ko` - MT7921 common functionality (212,745 bytes)
7. `mt7921u.ko` - Final MT7921U USB driver

## Dependency Graph

```
mt7921u.ko
├── mt792x-usb.ko
│   ├── mt792x-lib.ko
│   │   ├── mt76.ko
│   │   │   ├── mac80211
│   │   │   └── cfg80211
│   │   └── mt76-connac-lib.ko
│   │       ├── mt76.ko
│   │       ├── mac80211
│   │       └── cfg80211
│   └── mt76-usb.ko
│       └── mt76.ko
├── mt7921-common.ko
│   ├── mt76-connac-lib.ko
│   ├── mac80211
│   ├── mt792x-lib.ko
│   ├── mt76.ko
│   └── cfg80211
└── mt76-connac-lib.ko
```

## Installation Instructions

### Manual Module Loading
```bash
# Load in dependency order
sudo modprobe cfg80211
sudo modprobe mac80211
sudo insmod modules/mt76.ko
sudo insmod modules/mt76-usb.ko
sudo insmod modules/mt76-connac-lib.ko
sudo insmod modules/mt792x-lib.ko
sudo insmod modules/mt792x-usb.ko
sudo insmod modules/mt7921-common.ko
sudo insmod modules/mt7921u.ko
```

### Installation to Target System
```bash
# Copy modules to target system
sudo cp modules/*.ko /lib/modules/$(uname -r)/kernel/drivers/net/wireless/mediatek/mt76/
sudo cp modules/*.ko /lib/modules/$(uname -r)/kernel/drivers/net/wireless/mediatek/mt76/mt7921/

# Install firmware files
sudo mkdir -p /lib/firmware/mediatek/
sudo cp firmware/*.bin /lib/firmware/mediatek/
sudo cp firmware/*.bin.zst /lib/firmware/mediatek/

# Update module dependencies
sudo depmod -a

# Load driver
sudo modprobe mt7921u
```

## Device Information

**USB Device ID:** 0e8d:7961  
**Vendor:** MediaTek Inc.  
**Product:** MT7921U WiFi Adapter  
**Compatible Cards:**
- Alfa AWUS036AXNU (Primary target - MediaTek MT7921U)
- Alfa AWUS036ACHU (MT7921AU variant)
- Various MT7921U/MT7921AU-based USB WiFi adapters

## Kernel Requirements

- Linux kernel 5.4+
- cfg80211 wireless framework
- mac80211 stack
- USB 2.0/3.0 support

## Firmware Requirements

The MT7921U driver requires firmware files located in `/lib/firmware/mediatek/`:
- `WIFI_MT7961_patch_mcu_1_2_hdr.bin` - MCU patch firmware (91KB)
- `WIFI_RAM_CODE_MT7961_1.bin` - RAM code firmware (774KB)

**Note:** MT7921U uses MT7961 firmware internally. Both compressed (.zst) and uncompressed versions are included.

## Driver Features

- 802.11ac Wave 2 (WiFi 5)
- 2.4GHz and 5GHz bands
- MU-MIMO support
- WPA3 security
- Monitor mode capability
- Packet injection support

## Troubleshooting

### Loading Issues
```bash
# Check if device is detected
lsusb | grep 0e8d:7961

# Check module loading
dmesg | grep mt7921
lsmod | grep mt76
```

### Unload All Modules
```bash
sudo rmmod mt7921u
sudo rmmod mt7921-common  
sudo rmmod mt792x-usb
sudo rmmod mt792x-lib
sudo rmmod mt76-connac-lib
sudo rmmod mt76-usb
sudo rmmod mt76
```

## Files Extracted

### Kernel Modules (14 files)
- 7 compressed modules (.ko.zst)
- 7 decompressed modules (.ko)

### Module Information (7 files)
- mt7921u.info - Main driver information
- mt7921-common.info - Common functions
- mt792x-usb.info - USB interface
- mt792x-lib.info - Library functions
- mt76-connac-lib.info - Connectivity library
- mt76-usb.info - USB support
- mt76.info - Base framework

### Configuration Files (11 files)
Standard modprobe.d configuration files (no MT7921-specific configs found)

### Documentation
- modules.dep - Complete dependency tree
- README.md - This documentation

## Source Information

**Extracted from system:**
- OS: Ubuntu 24.04.3 LTS (Noble Numbat)  
- Architecture: aarch64 (ARM64)
- Kernel: Linux 6.8.0-1031-raspi
- UHD Version: Multiple (4.1.0 - 4.6.0 tested)
- Hardware: Raspberry Pi 4 Model B

This driver package was extracted from a working Raspberry Pi system with confirmed MT7921U compatibility.