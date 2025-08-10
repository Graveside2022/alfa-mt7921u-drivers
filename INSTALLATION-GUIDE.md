# MT7921U Driver Installation Guide

## System Requirements

### Minimum Requirements

#### Kernel Version Support
- **Minimum**: Linux kernel 5.4.0 (LTS)
- **Recommended**: Linux kernel 5.15.0+ (LTS)
- **Tested**: Linux kernel 6.8.0+ (current)
- **Architecture**: x86_64, aarch64 (ARM64), armhf

#### Dependencies
- **cfg80211**: Wireless configuration framework
- **mac80211**: Generic 802.11 stack
- **USB 2.0/3.0**: Host controller support
- **Firmware loader**: Built-in or module support

#### Hardware Requirements
- **USB Port**: USB 2.0 minimum, USB 3.0 recommended
- **RAM**: 512MB minimum, 1GB+ recommended
- **Storage**: 50MB free space for modules and firmware

### Supported Distributions

#### Verified Compatible
- **Ubuntu**: 20.04 LTS, 22.04 LTS, 24.04 LTS
- **Debian**: 11 (Bullseye), 12 (Bookworm)
- **Fedora**: 38, 39, 40
- **CentOS/RHEL**: 8, 9
- **Arch Linux**: Rolling release
- **openSUSE**: Leap 15.4+, Tumbleweed

#### Raspberry Pi Support
- **Raspberry Pi OS**: Based on Debian Bullseye/Bookworm
- **Ubuntu**: 22.04 LTS for Raspberry Pi
- **Kernel**: 6.1+ (Raspberry Pi specific)
- **Architecture**: ARM64 recommended, ARMv7 supported

## Pre-Installation Verification

### 1. Check System Prerequisites

#### Verify Kernel Version
```bash
# Check current kernel version
uname -r

# Verify minimum version (should be 5.4.0+)
if [ "$(uname -r | cut -d. -f1,2 | tr -d '.')" -ge "54" ]; then
    echo "✓ Kernel version supported"
else
    echo "✗ Kernel version too old - please upgrade"
fi
```

#### Check Wireless Framework
```bash
# Verify cfg80211 support
if lsmod | grep -q cfg80211; then
    echo "✓ cfg80211 loaded"
elif modinfo cfg80211 >/dev/null 2>&1; then
    echo "✓ cfg80211 available"
    sudo modprobe cfg80211
else
    echo "✗ cfg80211 not found - install wireless tools"
fi

# Verify mac80211 support
if lsmod | grep -q mac80211; then
    echo "✓ mac80211 loaded"
elif modinfo mac80211 >/dev/null 2>&1; then
    echo "✓ mac80211 available"
    sudo modprobe mac80211
else
    echo "✗ mac80211 not found - install wireless tools"
fi
```

#### Check USB Subsystem
```bash
# Verify USB support
lsusb | head -5
if [ $? -eq 0 ]; then
    echo "✓ USB subsystem working"
else
    echo "✗ USB subsystem issue"
fi

# Check for USB 3.0 support
if lsusb -t | grep -q "5000M"; then
    echo "✓ USB 3.0 available"
else
    echo "⚠ USB 3.0 not detected (USB 2.0 will work)"
fi
```

### 2. Device Detection Verification

#### Before Driver Installation
```bash
# Check if MT7921U device is detected (before driver)
echo "Checking for MT7921U device..."
if lsusb | grep -i "0e8d:7961\|MediaTek.*MT7921"; then
    echo "✓ MT7921U device detected"
    lsusb | grep -i "0e8d\|MediaTek"
else
    echo "✗ MT7921U device not found"
    echo "Please ensure:"
    echo "  - Device is properly connected"
    echo "  - Device has power"
    echo "  - Try different USB port"
fi
```

## Installation Methods

### Method 1: Distribution Package Manager (Recommended)

#### Ubuntu/Debian
```bash
# Update package lists
sudo apt update

# Install kernel headers (required for DKMS)
sudo apt install linux-headers-$(uname -r)

# Install wireless tools
sudo apt install wireless-tools iw

# Install firmware (if available in repos)
sudo apt install firmware-misc-nonfree

# Check if driver is already available
sudo apt search mt76 | grep mt7921
```

#### Fedora/CentOS/RHEL
```bash
# Install kernel headers
sudo dnf install kernel-devel kernel-headers

# Install wireless tools
sudo dnf install wireless-tools iw

# Install firmware packages
sudo dnf install linux-firmware

# Enable additional repositories if needed
sudo dnf install rpmfusion-free-release
```

#### Arch Linux
```bash
# Install kernel headers
sudo pacman -S linux-headers

# Install wireless tools
sudo pacman -S wireless_tools iw

# Install firmware
sudo pacman -S linux-firmware
```

### Method 2: Manual Module Installation

#### Step 1: Prepare System
```bash
# Create working directory
mkdir -p ~/mt7921u-install
cd ~/mt7921u-install

# Install build dependencies
# Ubuntu/Debian:
sudo apt install build-essential dkms git

# Fedora/RHEL:
sudo dnf groupinstall "Development Tools"
sudo dnf install dkms git

# Arch:
sudo pacman -S base-devel dkms git
```

#### Step 2: Install Modules
```bash
# Copy modules to kernel directory
KERNEL_VERSION=$(uname -r)
MODULE_DIR="/lib/modules/${KERNEL_VERSION}/kernel/drivers/net/wireless/mediatek/mt76"

# Create directory structure
sudo mkdir -p "${MODULE_DIR}/mt7921"

# Copy main modules
sudo cp modules/mt76.ko "${MODULE_DIR}/"
sudo cp modules/mt76-usb.ko "${MODULE_DIR}/"
sudo cp modules/mt76-connac-lib.ko "${MODULE_DIR}/"
sudo cp modules/mt792x-lib.ko "${MODULE_DIR}/"
sudo cp modules/mt792x-usb.ko "${MODULE_DIR}/"

# Copy MT7921-specific modules
sudo cp modules/mt7921-common.ko "${MODULE_DIR}/mt7921/"
sudo cp modules/mt7921u.ko "${MODULE_DIR}/mt7921/"

# Update module dependencies
sudo depmod -a

echo "✓ Modules installed successfully"
```

#### Step 3: Install Firmware
```bash
# Create firmware directory
sudo mkdir -p /lib/firmware/mediatek

# Copy firmware files
sudo cp firmware/WIFI_MT7961_patch_mcu_1_2_hdr.bin /lib/firmware/mediatek/
sudo cp firmware/WIFI_RAM_CODE_MT7961_1.bin /lib/firmware/mediatek/

# Set correct permissions
sudo chmod 644 /lib/firmware/mediatek/WIFI_MT7961_*

echo "✓ Firmware installed successfully"
```

#### Step 4: Configure Module Loading
```bash
# Create module loading configuration
sudo tee /etc/modprobe.d/mt7921u.conf << EOF
# MT7921U WiFi Driver Configuration

# Ensure proper loading order
install mt7921u /sbin/modprobe mt76; /sbin/modprobe mt76-usb; /sbin/modprobe mt76-connac-lib; /sbin/modprobe mt792x-lib; /sbin/modprobe mt792x-usb; /sbin/modprobe mt7921-common; /sbin/modprobe --ignore-install mt7921u

# Optional: Set debug level (0=none, 7=verbose)
options mt76 debug_mask=0

# Optional: Disable ASPM if experiencing issues
# options mt7921u disable_aspm=1
EOF

echo "✓ Module configuration created"
```

### Method 3: DKMS Installation (Advanced)

#### Step 1: Prepare DKMS Structure
```bash
# Create DKMS source directory
DKMS_DIR="/usr/src/mt7921u-1.0"
sudo mkdir -p "${DKMS_DIR}"

# Copy source files (if available)
sudo cp -r modules/ "${DKMS_DIR}/"
sudo cp dkms.conf "${DKMS_DIR}/" 2>/dev/null || true

# Create basic dkms.conf if not present
sudo tee "${DKMS_DIR}/dkms.conf" << EOF
PACKAGE_NAME="mt7921u"
PACKAGE_VERSION="1.0"
CLEAN="make clean"
MAKE="make"
BUILT_MODULE_NAME[0]="mt7921u"
BUILT_MODULE_LOCATION[0]="."
DEST_MODULE_LOCATION[0]="/kernel/drivers/net/wireless/mediatek/mt76/mt7921"
AUTOINSTALL="yes"
EOF
```

#### Step 2: Install via DKMS
```bash
# Add to DKMS
sudo dkms add -m mt7921u -v 1.0

# Build modules
sudo dkms build -m mt7921u -v 1.0

# Install modules
sudo dkms install -m mt7921u -v 1.0

echo "✓ DKMS installation complete"
```

## Loading and Testing

### 1. Load Driver Modules

#### Automatic Loading
```bash
# Load driver (will load dependencies automatically)
sudo modprobe mt7921u

# Verify loading
if lsmod | grep -q mt7921u; then
    echo "✓ MT7921U driver loaded successfully"
    lsmod | grep mt76
else
    echo "✗ Driver loading failed"
    dmesg | tail -20
fi
```

#### Manual Loading (Troubleshooting)
```bash
# Load modules in dependency order
echo "Loading wireless framework..."
sudo modprobe cfg80211
sudo modprobe mac80211

echo "Loading MT76 base framework..."
sudo modprobe mt76

echo "Loading USB support..."
sudo modprobe mt76-usb

echo "Loading connectivity library..."
sudo modprobe mt76-connac-lib

echo "Loading MT792x support..."
sudo modprobe mt792x-lib
sudo modprobe mt792x-usb

echo "Loading MT7921 support..."
sudo modprobe mt7921-common

echo "Loading MT7921U driver..."
sudo modprobe mt7921u

echo "Driver loading complete!"
```

### 2. Device Verification

#### Check Device Recognition
```bash
# Verify device is detected
echo "=== Device Detection ==="
lsusb | grep -i "0e8d\|MediaTek"

# Check if driver bound to device
echo -e "\n=== Driver Binding ==="
if ls /sys/class/net/ | grep -q wlan; then
    echo "✓ Wireless interface created:"
    ls /sys/class/net/wlan*
else
    echo "✗ No wireless interface found"
fi

# Check kernel messages
echo -e "\n=== Kernel Messages ==="
dmesg | grep -i "mt7921\|mt76\|usb.*7961" | tail -10
```

#### Verify Firmware Loading
```bash
# Check firmware loading in kernel log
echo "=== Firmware Loading Status ==="
dmesg | grep -i firmware | grep -i mt | tail -5

# Check firmware files exist
echo -e "\n=== Firmware Files ==="
ls -la /lib/firmware/mediatek/WIFI_MT7961_*
```

### 3. Interface Configuration

#### Basic Interface Setup
```bash
# Get interface name
INTERFACE=$(ls /sys/class/net/ | grep wlan | head -1)

if [ -n "$INTERFACE" ]; then
    echo "Found interface: $INTERFACE"
    
    # Bring interface up
    sudo ip link set "$INTERFACE" up
    
    # Check interface status
    ip link show "$INTERFACE"
    
    # Scan for networks
    echo -e "\n=== Network Scan ==="
    sudo iw "$INTERFACE" scan | grep SSID | head -10
else
    echo "✗ No wireless interface found"
fi
```

#### Advanced Interface Configuration
```bash
# Create monitor interface (for analysis)
sudo iw phy phy0 interface add mon0 type monitor 2>/dev/null
if [ $? -eq 0 ]; then
    echo "✓ Monitor interface created"
    sudo ip link set mon0 up
else
    echo "⚠ Monitor interface creation failed"
fi

# Check supported interface types
echo -e "\n=== Supported Interface Types ==="
iw phy phy0 info | grep "Supported interface modes" -A 10
```

## Distribution-Specific Instructions

### Ubuntu 20.04/22.04/24.04 LTS

#### Method 1: Native Packages
```bash
# Update and install
sudo apt update
sudo apt install linux-modules-extra-$(uname -r)

# Install wireless firmware
sudo apt install firmware-misc-nonfree

# Install wireless tools
sudo apt install wireless-tools wpasupplicant

# Reboot to load new modules
sudo reboot
```

#### Method 2: Backports (for older kernels)
```bash
# Enable backports repository
echo "deb http://archive.ubuntu.com/ubuntu $(lsb_release -cs)-backports main restricted universe multiverse" | sudo tee -a /etc/apt/sources.list

sudo apt update

# Install newer kernel from backports
sudo apt -t $(lsb_release -cs)-backports install linux-generic

# Reboot with new kernel
sudo reboot
```

### Debian 11/12

#### Standard Installation
```bash
# Add non-free firmware repository
echo "deb http://deb.debian.org/debian $(lsb_release -cs) main contrib non-free non-free-firmware" | sudo tee -a /etc/apt/sources.list

sudo apt update

# Install firmware and tools
sudo apt install firmware-misc-nonfree wireless-tools wpasupplicant
sudo apt install linux-headers-$(uname -r)

# Load driver
sudo modprobe mt7921u
```

### Fedora 38/39/40

#### DNF Installation
```bash
# Install RPM Fusion repositories
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm

# Install kernel modules and firmware
sudo dnf install kernel-devel akmod-mt76 linux-firmware

# Wait for akmod to build (can take 5-10 minutes)
sudo akmods

# Load driver
sudo modprobe mt7921u
```

### CentOS Stream/RHEL 8/9

#### EPEL Installation
```bash
# Enable EPEL repository
sudo dnf install epel-release

# Install ELRepo for hardware drivers
sudo dnf install elrepo-release

# Install required packages
sudo dnf --enablerepo=elrepo install kmod-mt76

# Load driver
sudo modprobe mt7921u
```

### Arch Linux

#### Pacman Installation
```bash
# Install kernel headers
sudo pacman -S linux-headers

# Install firmware
sudo pacman -S linux-firmware

# Install wireless tools
sudo pacman -S wireless_tools wpa_supplicant

# The mt76 driver is included in the kernel
sudo modprobe mt7921u
```

### Raspberry Pi (ARM64)

#### Raspberry Pi OS
```bash
# Update system
sudo apt update && sudo apt upgrade

# Install kernel headers
sudo apt install raspberrypi-kernel-headers

# Install wireless firmware
sudo apt install firmware-misc-nonfree

# Install wireless tools
sudo apt install wireless-tools wpasupplicant

# Enable USB 3.0 (if disabled)
echo "dtparam=usb_max_current_enable=1" | sudo tee -a /boot/config.txt

# Reboot
sudo reboot

# After reboot, load driver
sudo modprobe mt7921u
```

## Troubleshooting Common Issues

### 1. Driver Loading Failures

#### Issue: "Module not found"
```bash
# Check if modules exist
find /lib/modules/$(uname -r) -name "*mt7921*" -o -name "*mt76*"

# If no modules found, reinstall:
sudo apt install linux-modules-extra-$(uname -r)
# or compile manually
```

#### Issue: "Firmware loading failed"
```bash
# Check firmware location
ls -la /lib/firmware/mediatek/

# Check firmware permissions
sudo chmod 644 /lib/firmware/mediatek/WIFI_MT7961_*

# Check kernel firmware loader
dmesg | grep firmware

# Manual firmware copy if needed
sudo cp firmware/*.bin /lib/firmware/mediatek/
```

#### Issue: "Symbol resolution failed"
```bash
# Check module dependencies
modinfo mt7921u | grep depends

# Check for symbol conflicts
sudo depmod -a
sudo modprobe -r mt7921u
sudo modprobe mt7921u
```

### 2. Device Recognition Issues

#### Issue: "Device not detected"
```bash
# Basic USB troubleshooting
lsusb -v | grep -A 10 -B 5 "0e8d"

# Check USB power
echo "Checking USB power management..."
for usb_dev in /sys/bus/usb/devices/*/power/control; do
    echo "Setting $usb_dev to 'on'"
    echo 'on' | sudo tee "$usb_dev" >/dev/null
done

# Reset USB subsystem
sudo modprobe -r xhci_hcd
sudo modprobe xhci_hcd
```

#### Issue: "Interface not created"
```bash
# Check if device is bound to driver
lsusb -t | grep -i mt

# Check kernel messages
dmesg | grep -E "mt7921|usb.*7961" | tail -20

# Check interface enumeration
ls -la /sys/class/net/
cat /proc/net/wireless
```

### 3. Performance Issues

#### Issue: "Slow connection speeds"
```bash
# Check USB connection speed
lsusb -t | grep -A 2 -B 2 "7961"

# Disable USB power management
echo 'on' | sudo tee /sys/bus/usb/devices/*/power/control

# Check for interference
sudo iw wlan0 scan | grep -E "SSID|freq|signal"

# Optimize USB buffer sizes
echo 'options mt76_usb disable_usb_sg=1' | sudo tee -a /etc/modprobe.d/mt7921u.conf
```

#### Issue: "Frequent disconnections"
```bash
# Disable USB autosuspend
echo 'ACTION=="add", SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", ATTR{idProduct}=="7961", TEST=="power/control", ATTR{power/control}="on"' | sudo tee /etc/udev/rules.d/99-mt7921u-power.rules

# Reload udev rules
sudo udevadm control --reload-rules

# Restart network manager
sudo systemctl restart NetworkManager
```

### 4. Compilation Issues

#### Issue: "Kernel headers missing"
```bash
# Install appropriate headers
# Ubuntu/Debian:
sudo apt install linux-headers-$(uname -r)

# Fedora/RHEL:
sudo dnf install kernel-devel-$(uname -r)

# Arch:
sudo pacman -S linux-headers
```

#### Issue: "Build dependencies missing"
```bash
# Install build tools
# Ubuntu/Debian:
sudo apt install build-essential

# Fedora/RHEL:
sudo dnf groupinstall "Development Tools"

# Arch:
sudo pacman -S base-devel
```

## Verification and Testing

### 1. Driver Functionality Test

```bash
#!/bin/bash
# MT7921U Driver Test Script

echo "=== MT7921U Driver Verification Test ==="

# Test 1: Module Loading
echo "1. Testing module loading..."
if lsmod | grep -q mt7921u; then
    echo "   ✓ mt7921u module loaded"
else
    echo "   ✗ mt7921u module not loaded"
    exit 1
fi

# Test 2: Device Detection
echo "2. Testing device detection..."
if lsusb | grep -q "0e8d:7961"; then
    echo "   ✓ MT7921U device detected"
else
    echo "   ✗ MT7921U device not found"
    exit 1
fi

# Test 3: Interface Creation
echo "3. Testing interface creation..."
if ls /sys/class/net/ | grep -q wlan; then
    IFACE=$(ls /sys/class/net/ | grep wlan | head -1)
    echo "   ✓ Wireless interface created: $IFACE"
else
    echo "   ✗ No wireless interface found"
    exit 1
fi

# Test 4: Interface Activation
echo "4. Testing interface activation..."
sudo ip link set "$IFACE" up
sleep 2
if ip link show "$IFACE" | grep -q "UP"; then
    echo "   ✓ Interface activated successfully"
else
    echo "   ✗ Interface activation failed"
    exit 1
fi

# Test 5: Scanning Capability
echo "5. Testing scanning capability..."
if timeout 10 sudo iw "$IFACE" scan >/dev/null 2>&1; then
    echo "   ✓ Scanning works"
else
    echo "   ✗ Scanning failed"
    exit 1
fi

# Test 6: Monitor Mode (if supported)
echo "6. Testing monitor mode..."
if sudo iw phy phy0 interface add mon0 type monitor 2>/dev/null; then
    echo "   ✓ Monitor mode supported"
    sudo ip link delete mon0
else
    echo "   ⚠ Monitor mode not supported or failed"
fi

echo ""
echo "=== Test Results ==="
echo "✓ MT7921U driver is working correctly!"
echo ""
echo "Device Information:"
lsusb | grep "0e8d:7961"
echo ""
echo "Network Interfaces:"
ip link show | grep -A 1 wlan
```

### 2. Performance Benchmark

```bash
#!/bin/bash
# MT7921U Performance Test

echo "=== MT7921U Performance Benchmark ==="

IFACE=$(ls /sys/class/net/ | grep wlan | head -1)

if [ -z "$IFACE" ]; then
    echo "No wireless interface found"
    exit 1
fi

echo "Testing interface: $IFACE"

# Test connection speed
echo "1. Checking link quality..."
iwconfig "$IFACE"

# Test throughput (requires connection)
echo "2. Throughput test (requires network connection)..."
if iwconfig "$IFACE" | grep -q "ESSID:"; then
    echo "Connected to network, testing throughput..."
    # Add throughput test commands here
else
    echo "Not connected to any network"
fi

# Test power consumption
echo "3. Power consumption info..."
cat /sys/class/net/"$IFACE"/device/power/runtime_status 2>/dev/null || echo "Power info not available"

echo "Benchmark complete!"
```

## Uninstallation

### Remove Modules
```bash
# Unload driver modules
sudo modprobe -r mt7921u
sudo modprobe -r mt7921-common
sudo modprobe -r mt792x-usb
sudo modprobe -r mt792x-lib
sudo modprobe -r mt76-connac-lib
sudo modprobe -r mt76-usb
sudo modprobe -r mt76

# Remove module files
KERNEL_VERSION=$(uname -r)
sudo rm -f /lib/modules/${KERNEL_VERSION}/kernel/drivers/net/wireless/mediatek/mt76/mt7921/mt7921*
sudo rm -f /lib/modules/${KERNEL_VERSION}/kernel/drivers/net/wireless/mediatek/mt76/mt792x*
sudo rm -f /lib/modules/${KERNEL_VERSION}/kernel/drivers/net/wireless/mediatek/mt76/mt76*

# Remove configuration
sudo rm -f /etc/modprobe.d/mt7921u.conf

# Update module dependencies
sudo depmod -a

echo "MT7921U driver uninstalled"
```

### Remove DKMS Installation
```bash
# Remove from DKMS
sudo dkms remove -m mt7921u -v 1.0 --all

# Remove source
sudo rm -rf /usr/src/mt7921u-1.0

echo "DKMS installation removed"
```

## Support and Resources

### Getting Help
- **Kernel Documentation**: `/usr/src/linux/Documentation/networking/wireless/`
- **Ubuntu Community**: [Ubuntu Forums - Networking](https://ubuntuforums.org/forumdisplay.php?f=336)
- **Linux Wireless**: [linux-wireless mailing list](https://wireless.wiki.kernel.org/)
- **MT76 Development**: [OpenWrt MT76 project](https://github.com/openwrt/mt76)

### Reporting Issues
When reporting problems, include:
1. Distribution and kernel version (`uname -a`)
2. Device information (`lsusb`)
3. Driver loading messages (`dmesg | grep mt`)
4. Module information (`lsmod | grep mt`)
5. Wireless interface status (`iwconfig`)

This completes the comprehensive installation guide for MT7921U drivers. Follow the method that best suits your distribution and use case.