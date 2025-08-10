# MT7921U Driver Architecture Documentation

## Overview

The MT7921U driver implements a layered architecture for MediaTek's MT7921U USB WiFi chipsets, providing comprehensive support for 802.11ax (WiFi 6) capabilities. The driver stack is built on top of Linux's mac80211 wireless framework and follows a modular design pattern for maximum code reuse and maintainability.

## Architecture Layers

### 1. Hardware Abstraction Layer (HAL)

The driver architecture follows a strict layered approach:

```
Application Layer (userspace)
    ↓
nl80211/cfg80211 (kernel wireless configuration API)
    ↓
mac80211 (generic 802.11 stack)
    ↓
MT76 Driver Framework (hardware abstraction)
    ↓
MT7921U Hardware Driver (device-specific implementation)
    ↓
USB Subsystem
    ↓
Hardware (MT7921U chipset)
```

### 2. Module Dependency Hierarchy

The MT7921U driver consists of seven interconnected kernel modules with strict loading dependencies:

```
mt7921u.ko (Main Driver)
├── mt792x-usb.ko (USB Interface Layer)
│   ├── mt792x-lib.ko (MT792x Series Common Library)
│   │   ├── mt76.ko (Base Framework)
│   │   │   ├── mac80211 (Linux 802.11 Stack)
│   │   │   └── cfg80211 (Wireless Configuration)
│   │   └── mt76-connac-lib.ko (Connectivity Acceleration Library)
│   │       ├── mt76.ko
│   │       ├── mac80211
│   │       └── cfg80211
│   └── mt76-usb.ko (USB Transport Layer)
│       └── mt76.ko
├── mt7921-common.ko (MT7921 Series Common Functions)
│   ├── mt76-connac-lib.ko
│   ├── mac80211
│   ├── mt792x-lib.ko
│   ├── mt76.ko
│   └── cfg80211
└── mt76-connac-lib.ko (Connectivity Helper Library)
```

### 3. Core Components

#### 3.1 MT76 Framework (`mt76.ko`)
- **Size**: 262,777 bytes
- **Purpose**: Base driver framework for all MediaTek 802.11 chipsets
- **Key Functions**:
  - Hardware register abstraction
  - DMA buffer management
  - Interrupt handling framework
  - Power management core
  - Debug infrastructure
- **Dependencies**: mac80211, cfg80211

#### 3.2 MT76 USB Transport (`mt76-usb.ko`)
- **Size**: 86,969 bytes
- **Purpose**: USB-specific transport layer for MT76 devices
- **Key Functions**:
  - USB endpoint management
  - Bulk transfer handling
  - USB power management
  - Device enumeration
- **Dependencies**: mt76

#### 3.3 ConnAC Library (`mt76-connac-lib.ko`)
- **Size**: 211,841 bytes
- **Purpose**: Connectivity Acceleration library for modern MT76 chipsets
- **Key Functions**:
  - Advanced power management
  - Multi-link operation (MLO) support
  - Beamforming implementations
  - Advanced security features
- **Dependencies**: mt76, mac80211, cfg80211

#### 3.4 MT792x Core Library (`mt792x-lib.ko`)
- **Size**: 138,385 bytes
- **Purpose**: Common functionality for MT792x series chipsets
- **Key Functions**:
  - Power state management
  - Firmware loading coordination
  - Channel management
  - Calibration routines
- **Dependencies**: mt76, mt76-connac-lib, mac80211

#### 3.5 MT792x USB Interface (`mt792x-usb.ko`)
- **Size**: 44,225 bytes
- **Purpose**: USB interface layer for MT792x series
- **Key Functions**:
  - USB device binding
  - Endpoint configuration
  - Suspend/resume handling
- **Dependencies**: mt792x-lib, mt76-usb

#### 3.6 MT7921 Common (`mt7921-common.ko`)
- **Size**: 212,745 bytes
- **Purpose**: Shared functionality for all MT7921 variants
- **Key Functions**:
  - Chip initialization
  - Firmware management
  - Configuration handling
  - Feature enablement
- **Dependencies**: mt76-connac-lib, mac80211, mt792x-lib, mt76, cfg80211

#### 3.7 MT7921U Main Driver (`mt7921u.ko`)
- **Size**: 66,401 bytes
- **Purpose**: USB-specific implementation for MT7921U
- **Key Functions**:
  - Device probing and binding
  - USB-specific power management
  - Device removal handling
- **Dependencies**: All above modules

## USB Communication Protocol

### 1. Device Enumeration

The MT7921U uses standard USB 3.0/2.0 protocols with the following characteristics:

- **Vendor ID**: 0x0e8d (MediaTek Inc.)
- **Product ID**: 0x7961 (MT7921U)
- **USB Class**: Vendor-specific (0xFF)
- **Interface Count**: 1
- **Endpoint Configuration**:
  - Control Endpoint: EP0 (bidirectional)
  - Bulk OUT Endpoints: Multiple for TX data
  - Bulk IN Endpoints: Multiple for RX data
  - Interrupt Endpoint: Status/event reporting

### 2. Communication Flow

```
Host                     MT7921U Device
 │                            │
 │── USB Device Detection ────│
 │                            │
 │── Firmware Loading ────────│
 │                            │
 │── Configuration Setup ─────│
 │                            │
 │── Bulk Data Transfer ──────│
 │   (TX/RX 802.11 frames)    │
 │                            │
 │── Status/Event Updates ────│
 │   (via Interrupt EP)       │
```

### 3. Data Transfer Mechanisms

#### Bulk Transfer (Primary Data Path)
- **TX Path**: Host → Device via bulk OUT endpoints
- **RX Path**: Device → Host via bulk IN endpoints
- **Buffer Management**: Circular buffer with DMA mapping
- **Performance**: Up to 5Gbps theoretical (limited by USB 3.0)

#### Control Transfer (Management)
- **Configuration**: Initial device setup
- **Calibration**: RF parameter adjustment
- **Debug**: Development and troubleshooting

#### Interrupt Transfer (Events)
- **Firmware Status**: Boot completion, error conditions
- **Hardware Events**: Channel changes, power state transitions
- **Statistics**: Performance counters and diagnostics

## MAC80211 Integration

### 1. Interface Types Supported

The MT7921U driver registers with mac80211 supporting multiple interface types:

- **Station (STA)**: Client mode for connecting to access points
- **Access Point (AP)**: Software AP functionality
- **Monitor**: Promiscuous packet capture mode
- **P2P Client/GO**: WiFi Direct support

### 2. Hardware Capabilities Registration

```c
// Simplified capability registration
hw->wiphy->interface_modes = 
    BIT(NL80211_IFTYPE_STATION) |
    BIT(NL80211_IFTYPE_AP) |
    BIT(NL80211_IFTYPE_MONITOR) |
    BIT(NL80211_IFTYPE_P2P_CLIENT) |
    BIT(NL80211_IFTYPE_P2P_GO);

hw->wiphy->flags |= 
    WIPHY_FLAG_HAS_CHANNEL_SWITCH |
    WIPHY_FLAG_SUPPORTS_SCHED_SCAN |
    WIPHY_FLAG_SUPPORTS_TDLS;
```

### 3. Frame Processing Pipeline

```
RX Path:
USB Bulk IN → Driver Buffer → DMA → mac80211 → Network Stack

TX Path:
Network Stack → mac80211 → Driver Queue → DMA → USB Bulk OUT
```

## Power Management Features

### 1. USB Power Management

The driver implements comprehensive USB power management:

#### Runtime PM States
- **D0**: Fully operational
- **D1**: Partial power down (USB suspend)
- **D2**: Deep sleep (firmware maintained)
- **D3**: Full power off

#### Power Saving Mechanisms
- **Dynamic Frequency Scaling**: Reduces clock speeds during low activity
- **Selective Suspend**: USB endpoints can be individually suspended
- **Wake-on-WLAN**: Device can wake system on specific 802.11 events

### 2. 802.11 Power Save

#### Legacy Power Save
- **Power Save Mode (PSM)**: Standard 802.11 power saving
- **DTIM Handling**: Dynamic beacon interval adjustment
- **TIM Parsing**: Traffic indication map processing

#### Advanced Power Features
- **WMM Power Save**: Per-AC power management
- **UAPSD**: Unscheduled automatic power save delivery
- **SMPS**: Spatial multiplexing power save

### 3. Platform Integration

#### System Suspend/Resume
```c
// Power management hooks
static const struct dev_pm_ops mt7921u_pm_ops = {
    .suspend = mt7921u_suspend,
    .resume = mt7921u_resume,
    .runtime_suspend = mt7921u_runtime_suspend,
    .runtime_resume = mt7921u_runtime_resume,
};
```

#### ACPI Integration
- **Device Power States**: ACPI D-states support
- **Wake Events**: GPIO and USB wake capabilities
- **Thermal Management**: Dynamic power adjustment based on temperature

## Firmware Architecture

### 1. Firmware Components

The MT7921U requires two main firmware components:

#### MCU Firmware (`WIFI_MT7961_patch_mcu_1_2_hdr.bin`)
- **Size**: 91KB
- **Purpose**: Main Control Unit firmware
- **Functions**:
  - System initialization
  - Protocol stack management
  - Power management
  - Security enforcement

#### RAM Code (`WIFI_RAM_CODE_MT7961_1.bin`)
- **Size**: 774KB
- **Purpose**: Runtime code and data
- **Functions**:
  - 802.11 MAC implementation
  - PHY layer control
  - Calibration data
  - Feature-specific code

### 2. Firmware Loading Sequence

```
Driver Load
    ↓
Device Detection
    ↓
Request RAM Code Firmware
    ↓
Load RAM Code to Device
    ↓
Request MCU Patch Firmware
    ↓
Load MCU Patch to Device
    ↓
Verify Firmware Integrity
    ↓
Start Firmware Execution
    ↓
Wait for Boot Complete
    ↓
Initialize Device Structures
    ↓
Register with mac80211
```

### 3. Firmware Communication

#### Command/Response Protocol
- **Command Queue**: Host-to-firmware command channel
- **Event Queue**: Firmware-to-host event channel
- **Sequence Numbers**: Command tracking and acknowledgment
- **Timeout Handling**: Automatic retry and error recovery

#### Shared Memory Layout
```
Device Memory Map:
┌─────────────────────┐ 0x0000_0000
│ Boot ROM            │
├─────────────────────┤ 0x0001_0000
│ RAM Code            │
├─────────────────────┤ 0x0020_0000
│ Shared Buffers      │
├─────────────────────┤ 0x0030_0000
│ MCU Firmware        │
├─────────────────────┤ 0x0040_0000
│ Calibration Data    │
└─────────────────────┘ 0x0050_0000
```

## Error Handling and Recovery

### 1. Error Detection Mechanisms

#### Hardware Level
- **USB Transfer Errors**: Bulk transfer failures, timeout conditions
- **Firmware Errors**: Watchdog timeouts, assertion failures
- **RF Errors**: Calibration failures, PA protection

#### Driver Level
- **Resource Exhaustion**: Memory allocation failures
- **State Machine Errors**: Invalid state transitions
- **Concurrency Issues**: Lock contention, race conditions

### 2. Recovery Strategies

#### Automatic Recovery
- **USB Reset**: Reinitialize USB connection
- **Firmware Restart**: Reload firmware without full reset
- **Interface Reset**: Restart network interface

#### Manual Recovery
- **Module Reload**: rmmod/insmod cycle
- **Device Replug**: Physical USB reconnection
- **System Reboot**: Full system restart

### 3. Debug Infrastructure

#### Kernel Logging
```bash
# Enable debug logging
echo 'module mt76 +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module mt7921u +p' > /sys/kernel/debug/dynamic_debug/control
```

#### DebugFS Interface
```
/sys/kernel/debug/ieee80211/phy*/mt76/
├── regidx          # Register access
├── regval          # Register values
├── queues          # Queue statistics
├── tx_stats        # Transmission statistics
├── rx_stats        # Reception statistics
└── fw_debug        # Firmware debug info
```

## Performance Characteristics

### 1. Throughput Performance

#### Theoretical Maximums
- **802.11ax (WiFi 6)**: Up to 600 Mbps (2x2 MIMO)
- **802.11ac**: Up to 433 Mbps (80MHz channel)
- **802.11n**: Up to 300 Mbps (40MHz channel)

#### Real-World Performance
- **USB 3.0 Limitation**: ~400-500 Mbps effective throughput
- **CPU Overhead**: 15-20% CPU utilization at maximum throughput
- **Latency**: < 2ms average, < 10ms worst-case

### 2. Memory Usage

#### Static Allocation
- **Driver Code**: ~1MB total for all modules
- **Firmware**: ~865KB resident memory
- **Configuration Data**: ~64KB

#### Dynamic Allocation
- **RX Buffers**: 32-128 buffers × 2KB = 64-256KB
- **TX Buffers**: 16-64 buffers × 2KB = 32-128KB
- **Management Frames**: ~16KB

### 3. Power Consumption

#### Operational States
- **Active (High Throughput)**: 850-1000mA @ 5V
- **Active (Idle)**: 400-600mA @ 5V
- **Power Save**: 100-200mA @ 5V
- **Deep Sleep**: 10-50mA @ 5V

## Security Architecture

### 1. Encryption Support

#### Hardware Acceleration
- **AES**: 128/256-bit encryption/decryption
- **TKIP**: Legacy WPA support
- **CCMP**: WPA2/WPA3 cipher suite
- **GCMP**: Advanced cipher for 802.11ac/ax

#### Key Management
- **PTK/GTK**: Pairwise and group temporal keys
- **Key Slots**: Hardware key storage (up to 16 slots)
- **Key Rotation**: Automatic key refresh

### 2. Authentication Mechanisms

#### WPA3 Support
- **SAE**: Simultaneous Authentication of Equals
- **OWE**: Opportunistic Wireless Encryption
- **DPP**: Device Provisioning Protocol

#### Enterprise Features
- **802.1X**: RADIUS authentication
- **EAP Methods**: Multiple authentication protocols
- **Certificate Validation**: X.509 certificate support

### 3. Security Offload

#### Hardware Features
- **PN Check**: Packet number validation
- **Replay Protection**: Automatic replay attack prevention
- **MIC Validation**: Message integrity checking

## Module Loading and Configuration

### 1. Recommended Loading Sequence

```bash
#!/bin/bash
# MT7921U Driver Loading Script

# Load base wireless framework
modprobe cfg80211
modprobe mac80211

# Load MT76 framework in dependency order
modprobe mt76
modprobe mt76-usb
modprobe mt76-connac-lib

# Load MT792x series support
modprobe mt792x-lib
modprobe mt792x-usb

# Load MT7921 specific support
modprobe mt7921-common

# Finally load USB driver
modprobe mt7921u
```

### 2. Module Parameters

#### mt76 Parameters
```
debug_mask=0x0          # Debug output control
led_pin=0               # LED control GPIO pin
```

#### mt7921u Parameters
```
disable_aspm=N          # Disable ASPM power management
fw_debug=N              # Enable firmware debug
```

### 3. Runtime Configuration

#### Interface Configuration
```bash
# Create wireless interface
iw phy phy0 interface add wlan0 type station

# Configure interface
ip link set wlan0 up
iw wlan0 scan
```

#### Monitor Mode Setup
```bash
# Enable monitor mode
iw phy phy0 interface add mon0 type monitor
ip link set mon0 up
iw mon0 set freq 2412
```

This architecture provides a robust, scalable foundation for MT7921U WiFi operations while maintaining compatibility with Linux wireless standards and enabling advanced WiFi 6 features.