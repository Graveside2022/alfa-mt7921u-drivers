# MT7921U Driver Dependency Analysis

## Complete Dependency Chain

### Level 0 - Kernel Frameworks
- `cfg80211` (kernel built-in) - Wireless configuration framework
- `mac80211` (kernel built-in) - MAC layer implementation

### Level 1 - Base MT76 Framework  
- `mt76.ko` - Core MediaTek MT76 driver framework
  - **Size:** 262,777 bytes
  - **Dependencies:** mac80211, cfg80211
  - **Description:** Base driver for MediaTek MT76 wireless chipsets

### Level 2 - USB and Connectivity Support
- `mt76-usb.ko` - MT76 USB interface driver
  - **Size:** 86,969 bytes  
  - **Dependencies:** mt76
  - **Description:** USB transport layer for MT76 devices

- `mt76-connac-lib.ko` - MT76 connectivity library
  - **Size:** 211,841 bytes
  - **Dependencies:** mt76, mac80211, cfg80211  
  - **Description:** Common connectivity functions for MT76xx devices

### Level 3 - MT792X Family Support
- `mt792x-lib.ko` - MT792X family library
  - **Size:** 138,385 bytes
  - **Dependencies:** mt76, mt76-connac-lib, mac80211
  - **Description:** Common library for MT7921/MT7922 devices

- `mt792x-usb.ko` - MT792X USB support
  - **Size:** 44,225 bytes  
  - **Dependencies:** mt792x-lib, mt76-usb, mt76
  - **Description:** USB transport for MT792X family

### Level 4 - MT7921 Common Functions
- `mt7921-common.ko` - MT7921 common functionality
  - **Size:** 212,745 bytes
  - **Dependencies:** mt76-connac-lib, mac80211, mt792x-lib, mt76, cfg80211
  - **Description:** Common functions shared by MT7921 variants (PCIe/USB)

### Level 5 - Final Driver
- `mt7921u.ko` - MT7921U USB driver
  - **Size:** 66,401 bytes
  - **Dependencies:** mt76-usb, mt792x-usb, mt7921-common, mt76, mt76-connac-lib, mt792x-lib
  - **Description:** USB-specific driver for MT7921U WiFi cards

## Loading Sequence (Bottom-Up)

```bash
# 1. Load kernel frameworks (usually auto-loaded)
modprobe cfg80211
modprobe mac80211

# 2. Load base MT76 framework
insmod mt76.ko

# 3. Load USB support
insmod mt76-usb.ko

# 4. Load connectivity library
insmod mt76-connac-lib.ko

# 5. Load MT792X family support
insmod mt792x-lib.ko
insmod mt792x-usb.ko

# 6. Load MT7921 common functions
insmod mt7921-common.ko

# 7. Load final MT7921U driver
insmod mt7921u.ko
```

## Automatic Loading with modprobe

```bash
# This single command will load all dependencies automatically
modprobe mt7921u
```

## Module Verification

After loading, verify with:
```bash
lsmod | grep mt76
# Should show:
# mt7921u               66401  0 
# mt7921_common        212745  1 mt7921u
# mt792x_usb            44225  1 mt7921u  
# mt792x_lib           138385  2 mt792x_usb,mt7921_common
# mt76_connac_lib      211841  2 mt792x_lib,mt7921_common
# mt76_usb              86969  2 mt792x_usb,mt7921u
# mt76                 262777  6 mt76_usb,mt76_connac_lib,mt792x_lib,mt7921_common,mt792x_usb,mt7921u
```

## Hardware Detection

Once loaded, the driver should detect MT7921U devices:
```bash
lsusb | grep -i mediatek
# Expected: Bus XXX Device XXX: ID 0e8d:7961 MediaTek Inc. Wireless_Device

dmesg | grep mt7921
# Should show initialization messages
```

## Total Driver Package Size

- **Compressed (.ko.zst):** 1,023,343 bytes (~1.0MB)
- **Uncompressed (.ko):** 1,483,448 bytes (~1.4MB)
- **All dependencies included:** 7 modules total