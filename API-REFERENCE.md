# MT7921U Driver API Reference

## Overview

This document provides comprehensive reference information for developers working with the MT7921U driver stack. It covers module parameters, sysfs interfaces, debugfs entries, configuration options, and performance tuning parameters available in the MT76 driver framework and MT7921U-specific implementations.

## Module Parameters

### mt76 (Base Framework)

#### debug_mask
Controls debug output verbosity for the MT76 framework.

**Type**: `uint`  
**Default**: `0`  
**Access**: Read/Write  
**Location**: `/sys/module/mt76/parameters/debug_mask`

```bash
# Set debug mask (bitmask values)
echo 7 > /sys/module/mt76/parameters/debug_mask

# Read current mask
cat /sys/module/mt76/parameters/debug_mask
```

**Bitmask Values**:
- `0x01` - Basic driver operations
- `0x02` - TX/RX data path
- `0x04` - MAC operations  
- `0x08` - Firmware communication
- `0x10` - Power management
- `0x20` - Calibration procedures
- `0x40` - Error conditions
- `0x80` - Verbose debugging

#### led_pin
GPIO pin number for LED control (hardware dependent).

**Type**: `int`  
**Default**: `0`  
**Range**: `0-31`

```bash
# Set LED GPIO pin
modprobe mt76 led_pin=2
```

### mt76-usb (USB Transport)

#### disable_usb_sg
Disables USB scatter-gather functionality to improve compatibility.

**Type**: `bool`  
**Default**: `false`  
**Access**: Read-only (set at module load)

```bash
# Disable USB scatter-gather
modprobe mt76_usb disable_usb_sg=1
```

#### usb_timeout
USB transfer timeout in milliseconds.

**Type**: `uint`  
**Default**: `1000`  
**Range**: `100-10000`

```bash
# Set 2-second timeout
modprobe mt76_usb usb_timeout=2000
```

### mt7921u (Main Driver)

#### disable_aspm
Disables Active State Power Management (ASPM) for stability.

**Type**: `bool`  
**Default**: `false`  
**Access**: Read-only

```bash
# Disable ASPM if experiencing stability issues
modprobe mt7921u disable_aspm=1
```

#### fw_debug
Enables firmware debugging features.

**Type**: `bool`  
**Default**: `false`  
**Access**: Read-only

```bash
# Enable firmware debugging
modprobe mt7921u fw_debug=1
```

#### deep_sleep
Controls deep sleep power management.

**Type**: `bool`  
**Default**: `true`  
**Access**: Read/Write

```bash
# Disable deep sleep for better latency
echo 0 > /sys/module/mt7921u/parameters/deep_sleep
```

## Sysfs Interfaces

### Device Information

#### Device Path
`/sys/class/net/{interface}/device/`

Contains standard USB device information and MT7921U-specific attributes.

#### Driver Information
```bash
# Driver version and information
cat /sys/class/net/wlan0/device/uevent

# USB device details
cat /sys/class/net/wlan0/device/idVendor    # 0e8d
cat /sys/class/net/wlan0/device/idProduct   # 7961
```

### Power Management

#### Runtime Power Management
`/sys/class/net/{interface}/device/power/`

```bash
# Check current power state
cat /sys/class/net/wlan0/device/power/runtime_status

# Control autosuspend
echo 'on' > /sys/class/net/wlan0/device/power/control
echo 'auto' > /sys/class/net/wlan0/device/power/control

# Set autosuspend delay (milliseconds)
echo 2000 > /sys/class/net/wlan0/device/power/autosuspend_delay_ms
```

**Power States**:
- `active` - Device fully operational
- `suspended` - Device in low-power state  
- `suspending` - Transition to suspend
- `resuming` - Transition from suspend

#### Wake-on-WLAN Configuration
```bash
# Enable wake-on-WLAN
echo 'enabled' > /sys/class/net/wlan0/device/power/wakeup

# Check wake capability
cat /sys/class/net/wlan0/device/power/wakeup
```

### Network Interface Control

#### Interface Statistics
`/sys/class/net/{interface}/statistics/`

```bash
# Basic network statistics
cat /sys/class/net/wlan0/statistics/rx_bytes
cat /sys/class/net/wlan0/statistics/tx_bytes
cat /sys/class/net/wlan0/statistics/rx_packets
cat /sys/class/net/wlan0/statistics/tx_packets
cat /sys/class/net/wlan0/statistics/rx_errors
cat /sys/class/net/wlan0/statistics/tx_errors
```

#### Interface Configuration
```bash
# MTU size
cat /sys/class/net/wlan0/mtu
echo 1500 > /sys/class/net/wlan0/mtu

# MAC address
cat /sys/class/net/wlan0/address

# Interface flags
cat /sys/class/net/wlan0/flags
```

### Wireless Configuration

#### PHY Information
`/sys/class/ieee80211/phy*/`

```bash
# Get PHY index
PHY=$(ls /sys/class/net/wlan0/phy80211/device/ieee80211/)

# Check supported features
cat /sys/class/ieee80211/${PHY}/supported_features

# Hardware capabilities
cat /sys/class/ieee80211/${PHY}/hw_modes
```

## DebugFS Interfaces

### Base Path
All debugfs entries are located under:
`/sys/kernel/debug/ieee80211/phy*/mt76/`

**Note**: Requires `CONFIG_MAC80211_DEBUGFS=y` and debugfs mounted.

```bash
# Mount debugfs if not mounted
mount -t debugfs none /sys/kernel/debug

# Find PHY interface
PHY=$(ls /sys/class/net/wlan0/phy80211/device/ieee80211/)
DEBUGFS_PATH="/sys/kernel/debug/ieee80211/${PHY}/mt76"
```

### Register Access

#### regidx / regval
Direct hardware register access for debugging.

```bash
# Set register index to read
echo 0x1000 > ${DEBUGFS_PATH}/regidx

# Read register value
cat ${DEBUGFS_PATH}/regval

# Write register value
echo 0x12345678 > ${DEBUGFS_PATH}/regval
```

**Common Registers**:
- `0x0000` - Chip ID register
- `0x1000` - MAC control register
- `0x2000` - PHY control register
- `0x3000` - Power management register

#### eeprom
EEPROM/Flash memory access for calibration data.

```bash
# Read EEPROM contents
hexdump -C ${DEBUGFS_PATH}/eeprom | head -20

# Check EEPROM size
wc -c ${DEBUGFS_PATH}/eeprom
```

### Statistics and Monitoring

#### tx_stats
Detailed transmission statistics.

```bash
# View TX statistics
cat ${DEBUGFS_PATH}/tx_stats
```

**Output Fields**:
- `tx_packets` - Total transmitted packets
- `tx_bytes` - Total transmitted bytes  
- `tx_dropped` - Dropped packets
- `tx_retries` - Retry attempts
- `tx_failed` - Failed transmissions
- `queue_stats` - Per-queue statistics

#### rx_stats  
Detailed reception statistics.

```bash
# View RX statistics
cat ${DEBUGFS_PATH}/rx_stats
```

**Output Fields**:
- `rx_packets` - Total received packets
- `rx_bytes` - Total received bytes
- `rx_dropped` - Dropped packets
- `rx_duplicates` - Duplicate packets
- `rx_crc_errors` - CRC error count
- `signal_avg` - Average signal strength

#### queues
Hardware queue status and statistics.

```bash
# View queue information
cat ${DEBUGFS_PATH}/queues
```

**Queue Types**:
- `AC_VO` - Voice (highest priority)
- `AC_VI` - Video  
- `AC_BE` - Best effort
- `AC_BK` - Background (lowest priority)
- `MGMT` - Management frames
- `BEACON` - Beacon frames

### Firmware Debug

#### fw_debug
Firmware debug information and logs.

```bash
# View firmware debug info
cat ${DEBUGFS_PATH}/fw_debug

# Enable/disable firmware debugging
echo 1 > ${DEBUGFS_PATH}/fw_debug_enable
```

#### fw_util
Firmware utilization and performance metrics.

```bash
# Check firmware CPU utilization
cat ${DEBUGFS_PATH}/fw_util
```

#### fw_state
Current firmware state and version information.

```bash
# View firmware state
cat ${DEBUGFS_PATH}/fw_state
```

**State Information**:
- Firmware version
- Boot status
- Feature flags
- Error counters

### Power Management Debug

#### pm_stats
Power management statistics and state information.

```bash
# View power management stats
cat ${DEBUGFS_PATH}/pm_stats
```

**PM Information**:
- Current power state
- Sleep/wake cycles
- Power consumption estimates
- Suspend/resume counts

### Calibration Debug

#### cal_data
RF calibration data and status.

```bash
# View calibration information
cat ${DEBUGFS_PATH}/cal_data
```

#### temperature
Current chip temperature readings.

```bash
# Check chip temperature
cat ${DEBUGFS_PATH}/temperature
```

## Configuration Files

### Module Configuration

#### /etc/modprobe.d/mt7921u.conf
System-wide module parameter configuration.

```bash
# Create configuration file
sudo tee /etc/modprobe.d/mt7921u.conf << EOF
# MT7921U Driver Configuration

# Load modules in correct order
install mt7921u /sbin/modprobe mt76; /sbin/modprobe mt76-usb; /sbin/modprobe mt76-connac-lib; /sbin/modprobe mt792x-lib; /sbin/modprobe mt792x-usb; /sbin/modprobe mt7921-common; /sbin/modprobe --ignore-install mt7921u

# Debug settings
options mt76 debug_mask=0

# USB optimization
options mt76_usb disable_usb_sg=0 usb_timeout=1000

# Power management
options mt7921u disable_aspm=0 deep_sleep=1

# Firmware debugging (disable in production)
options mt7921u fw_debug=0
EOF
```

#### /etc/udev/rules.d/99-mt7921u.rules
Udev rules for device-specific configuration.

```bash
# Create udev rules
sudo tee /etc/udev/rules.d/99-mt7921u.rules << EOF
# MT7921U USB WiFi Adapter Rules

# Disable USB autosuspend for MT7921U
ACTION=="add", SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", ATTR{idProduct}=="7961", TEST=="power/control", ATTR{power/control}="on"

# Set USB power settings
ACTION=="add", SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", ATTR{idProduct}=="7961", TEST=="power/autosuspend_delay_ms", ATTR{power/autosuspend_delay_ms}="2000"

# Set device permissions for regular users
ACTION=="add", SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", ATTR{idProduct}=="7961", MODE="0664", GROUP="plugdev"
EOF

# Reload udev rules
sudo udevadm control --reload-rules
sudo udevadm trigger
```

### Network Configuration

#### NetworkManager Configuration
```bash
# Create NetworkManager configuration
sudo tee /etc/NetworkManager/conf.d/mt7921u.conf << EOF
[device-mt7921u]
match-device=interface-name:wlan*;driver:mt7921u
wifi.backend=wpa_supplicant
wifi.powersave=2
wifi.scan-rand-mac-address=yes
EOF

# Restart NetworkManager
sudo systemctl restart NetworkManager
```

#### wpa_supplicant Configuration
```bash
# Example wpa_supplicant.conf
sudo tee /etc/wpa_supplicant/wpa_supplicant-mt7921u.conf << EOF
ctrl_interface=/run/wpa_supplicant
update_config=1

# Enable WPA3 features
pmf=2
ieee80211w=2

# Network configuration example
network={
    ssid="YourNetwork"
    psk="YourPassword"
    key_mgmt=WPA-PSK WPA-PSK-SHA256 SAE
    ieee80211w=2
}
EOF
```

## Performance Tuning

### Throughput Optimization

#### Buffer Size Tuning
```bash
# Increase network buffers for high throughput
echo 'net.core.rmem_max = 16777216' >> /etc/sysctl.conf
echo 'net.core.wmem_max = 16777216' >> /etc/sysctl.conf
echo 'net.core.rmem_default = 262144' >> /etc/sysctl.conf
echo 'net.core.wmem_default = 262144' >> /etc/sysctl.conf

# Apply changes
sysctl -p
```

#### USB Performance Tuning
```bash
# Disable USB autosuspend globally
echo 'SUBSYSTEM=="usb", TEST=="power/control", ATTR{power/control}="on"' > /etc/udev/rules.d/99-usb-power.rules

# Increase USB memory allocation
echo 1024 > /sys/module/usbcore/parameters/usbfs_memory_mb
```

### Latency Optimization

#### Interrupt Coalescing
```bash
# Configure interrupt coalescing for low latency
ethtool -C wlan0 rx-usecs 1 tx-usecs 1 2>/dev/null || true

# Disable power saving for gaming/real-time apps
iw wlan0 set power_save off
```

#### CPU Affinity
```bash
# Bind interrupts to specific CPU cores
IRQ=$(grep mt7921u /proc/interrupts | cut -d: -f1 | tr -d ' ')
if [ -n "$IRQ" ]; then
    echo 2 > /proc/irq/${IRQ}/smp_affinity
fi
```

### Power Optimization

#### Dynamic Power Management
```bash
# Configure aggressive power saving
iw wlan0 set power_save on

# Set TWT (Target Wake Time) parameters
iw wlan0 twt setup \
    req_type individual \
    setup_cmd request \
    trigger 1 \
    implicit 1 \
    flow_type announced \
    flow_id 1 \
    wake_interval_mantissa 512 \
    wake_interval_exponent 10
```

#### Frequency Scaling
```bash
# Use conservative CPU governor for power saving
echo conservative > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# Set minimum USB speed for power saving
echo 1 > /sys/module/usbcore/parameters/old_scheme_first
```

## Diagnostic Tools

### Built-in Diagnostics

#### Driver Health Check
```bash
#!/bin/bash
# MT7921U Driver Health Check Script

echo "=== MT7921U Driver Diagnostics ==="

# Check module loading
echo "1. Module Status:"
lsmod | grep -E "mt76|mt7921" | sort

# Check device binding
echo -e "\n2. Device Binding:"
lsusb -t | grep -A 5 -B 5 "7961\|mt7921u"

# Check interface status
echo -e "\n3. Interface Status:"
ip link show | grep -A 3 wlan

# Check wireless capabilities
echo -e "\n4. Wireless Capabilities:"
iw list | grep -A 20 "Wiphy"

# Check power state
echo -e "\n5. Power Management:"
cat /sys/class/net/wlan*/device/power/runtime_status 2>/dev/null

# Check firmware status
echo -e "\n6. Firmware Status:"
dmesg | grep -i "mt7921\|firmware" | tail -5

# Check error counters
echo -e "\n7. Error Statistics:"
cat /sys/class/net/wlan*/statistics/*errors 2>/dev/null | paste -s -d' '

echo -e "\n=== Diagnostic Complete ==="
```

#### Performance Monitor
```bash
#!/bin/bash
# MT7921U Performance Monitor

INTERFACE=$(ls /sys/class/net/ | grep wlan | head -1)
PHY=$(ls /sys/class/net/${INTERFACE}/phy80211/device/ieee80211/)

echo "=== Performance Monitoring for ${INTERFACE} ==="

while true; do
    clear
    echo "Interface: ${INTERFACE} | PHY: ${PHY} | $(date)"
    echo "================================================"
    
    # Network statistics
    echo "Network Statistics:"
    cat /sys/class/net/${INTERFACE}/statistics/rx_bytes | awk '{print "RX Bytes: " $1}'
    cat /sys/class/net/${INTERFACE}/statistics/tx_bytes | awk '{print "TX Bytes: " $1}'
    cat /sys/class/net/${INTERFACE}/statistics/rx_packets | awk '{print "RX Packets: " $1}'
    cat /sys/class/net/${INTERFACE}/statistics/tx_packets | awk '{print "TX Packets: " $1}'
    
    # Wireless statistics
    echo -e "\nWireless Statistics:"
    iw ${INTERFACE} station dump 2>/dev/null | grep -E "signal avg|tx bitrate|rx bitrate" || echo "Not connected"
    
    # Power state
    echo -e "\nPower State:"
    cat /sys/class/net/${INTERFACE}/device/power/runtime_status
    
    sleep 2
done
```

### Debugging Commands

#### Comprehensive Debug Output
```bash
# Enable all debugging
echo 'module mt76 +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module mt7921u +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module mt76_usb +p' > /sys/kernel/debug/dynamic_debug/control

# Monitor kernel messages
tail -f /var/log/kern.log | grep -E "mt76|mt7921u"
```

#### USB Analysis
```bash
# Monitor USB traffic
sudo usbmon -i 1 | grep "0e8d:7961"

# Check USB power consumption
lsusb -v -d 0e8d:7961 | grep -E "MaxPower|bcdUSB"
```

## Error Codes and Troubleshooting

### Common Error Codes

#### USB Errors
- `ETIMEDOUT (-110)` - USB transfer timeout
- `ENODEV (-19)` - Device disconnected  
- `EPROTO (-71)` - USB protocol error
- `EPIPE (-32)` - USB pipe/endpoint error

#### Firmware Errors
- `FIRMWARE_LOAD_FAILED` - Firmware file not found or corrupted
- `FIRMWARE_TIMEOUT` - Firmware boot timeout
- `FIRMWARE_CRASH` - Firmware assertion or crash

#### Driver Errors
- `EINVAL (-22)` - Invalid parameter
- `ENOMEM (-12)` - Out of memory
- `EBUSY (-16)` - Resource busy

### Error Recovery

#### Automatic Recovery Script
```bash
#!/bin/bash
# MT7921U Error Recovery Script

recover_driver() {
    echo "Attempting driver recovery..."
    
    # Unload all modules
    sudo modprobe -r mt7921u
    sudo modprobe -r mt7921-common
    sudo modprobe -r mt792x-usb
    sudo modprobe -r mt792x-lib
    sudo modprobe -r mt76-connac-lib
    sudo modprobe -r mt76-usb
    sudo modprobe -r mt76
    
    sleep 2
    
    # Reload driver
    sudo modprobe mt7921u
    
    sleep 5
    
    # Check if recovery successful
    if lsmod | grep -q mt7921u && ls /sys/class/net/ | grep -q wlan; then
        echo "Driver recovery successful"
        return 0
    else
        echo "Driver recovery failed"
        return 1
    fi
}

# Main recovery logic
if ! lsmod | grep -q mt7921u; then
    echo "Driver not loaded, attempting recovery..."
    recover_driver
elif ! ls /sys/class/net/ | grep -q wlan; then
    echo "Interface missing, attempting recovery..."  
    recover_driver
else
    echo "Driver appears healthy"
fi
```

This comprehensive API reference provides developers with the tools and knowledge needed to effectively work with, debug, and optimize the MT7921U driver stack.