# MT7921U Firmware Files Documentation

## Overview
This directory contains firmware files extracted from the Raspberry Pi system for the Alfa AWUS036AXNU WiFi adapter which uses the MediaTek MT7921U chipset.

## Firmware Files Summary

### Core MT7921U/MT7961 Firmware Files
The MT7921U chipset uses MT7961 firmware internally:

| File | Size | Format | Purpose |
|------|------|--------|---------|
| `WIFI_MT7961_patch_mcu_1_2_hdr.bin` | 91K | Binary | Main MCU patch firmware for MT7961 |
| `WIFI_MT7961_patch_mcu_1_2_hdr.bin.zst` | 53K | Zstd compressed | Compressed version of MCU patch |
| `WIFI_RAM_CODE_MT7961_1.bin` | 774K | Binary | RAM code firmware for MT7961 |
| `WIFI_RAM_CODE_MT7961_1.bin.zst` | 400K | Zstd compressed | Compressed version of RAM code |

### Supporting MT76 Series Firmware Files
Additional firmware files from the MT76 series that may be referenced or needed:

| File | Size | Format | Purpose |
|------|------|--------|---------|
| `mt7601u.bin.zst` | 28K | Zstd compressed | MT7601U USB WiFi firmware |
| `mt7610e.bin.zst` | 44K | Zstd compressed | MT7610E PCIe WiFi firmware |
| `mt7610u.bin.zst` | 44K | Zstd compressed | MT7610U USB WiFi firmware |
| `mt7615_cr4.bin.zst` | 120K | Zstd compressed | MT7615 CR4 processor firmware |
| `mt7615_n9.bin.zst` | 412K | Zstd compressed | MT7615 N9 processor firmware |
| `mt7615_rom_patch.bin.zst` | 8.0K | Zstd compressed | MT7615 ROM patch firmware |
| `mt7663_n9_rebb.bin.zst` | 296K | Zstd compressed | MT7663 N9 REBB firmware |
| `mt7663_n9_v3.bin.zst` | 400K | Zstd compressed | MT7663 N9 v3 firmware |

## Firmware Loading Sequence

### 1. Driver Load Sequence
1. `mt76.ko` - Base MT76 driver module
2. `mt76-usb.ko` - MT76 USB interface driver
3. `mt76-connac-lib.ko` - MT76 ConnAC library (connectivity acceleration)
4. `mt792x-lib.ko` - MT792x series common library
5. `mt792x-usb.ko` - MT792x USB interface
6. `mt7921-common.ko` - MT7921 common driver
7. `mt7921u.ko` - MT7921U specific driver

### 2. Firmware Loading Process
When the MT7921U device is detected:
1. Driver requests `WIFI_RAM_CODE_MT7961_1.bin` (RAM code)
2. Driver loads `WIFI_MT7961_patch_mcu_1_2_hdr.bin` (MCU patch)
3. Device initializes with loaded firmware

## Firmware Versions and Compatibility

### MT7961 Firmware (for MT7921U)
- **RAM Code Version**: 1 (based on filename)
- **MCU Patch Version**: 1.2 (based on filename)
- **Format**: MediaTek proprietary binary format
- **Chipset Compatibility**: MT7921, MT7921U, MT7921AU variants

### Compression Format
- **Zstd files**: Zstandard compressed versions
- **Binary files**: Uncompressed ready-to-load format
- The kernel can load either compressed or uncompressed versions

## System Integration

### Firmware Search Paths
The Linux firmware subsystem searches for firmware in this order:
1. `/lib/firmware/` (highest priority)
2. `/usr/lib/firmware/`
3. Custom paths via kernel parameters

### Installation
To install these firmware files on a target system:
```bash
sudo cp firmware/*.bin /lib/firmware/mediatek/
sudo cp firmware/*.bin.zst /lib/firmware/mediatek/
```

## Hardware Requirements

### Alfa AWUS036AXNU Specifications
- **Chipset**: MediaTek MT7921U
- **USB Interface**: USB 3.0/2.0 compatible
- **WiFi Standards**: 802.11a/b/g/n/ac/ax (WiFi 6)
- **Frequency Bands**: 2.4GHz and 5GHz dual-band
- **Antenna**: High-gain external antennas

### Power Requirements
- **USB Power**: Standard USB bus power
- **Current Draw**: ~500mA typical
- **Voltage**: 5V DC from USB

## Troubleshooting

### Common Issues
1. **Firmware not found**: Ensure files are in `/lib/firmware/mediatek/`
2. **Permission errors**: Check file permissions (644 recommended)
3. **Load failures**: Check dmesg for specific error messages
4. **Device not recognized**: Verify USB connection and device power

### Diagnostic Commands
```bash
# Check if device is detected
lsusb | grep -i mediatek

# Check driver status
lsmod | grep mt7921

# Check firmware loading
dmesg | grep -i "mt7921\|firmware"

# Check interface status
iwconfig
```

## File Integrity
All firmware files have been copied directly from a working Raspberry Pi system with the Alfa AWUS036AXNU adapter functioning correctly.

**Extraction Date**: August 10, 2025
**Source System**: Raspberry Pi with Ubuntu/Debian
**Verification**: Files tested and confirmed working with MT7921U hardware

## License and Legal
These firmware files are proprietary MediaTek binaries redistributed under the terms that allow inclusion in Linux distributions. The firmware files are:
- Copyright MediaTek Inc.
- Licensed for use with MediaTek hardware
- Distributed as part of standard Linux firmware packages

## Additional Notes
- The .zst compressed versions save space but require zstd decompression
- Both compressed and uncompressed versions are provided for maximum compatibility
- The MT7921U uses MT7961 firmware internally (this is normal and expected)
- These files are compatible with Linux kernel versions 5.4+ with mt76 driver support