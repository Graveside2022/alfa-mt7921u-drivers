# MT7921U Hardware Specifications

## Overview

The MediaTek MT7921U is a highly integrated single-chip solution for 802.11ax (WiFi 6) USB wireless network adapters. This document provides comprehensive hardware specifications for the MT7921U chipset and the Alfa AWUS036AXNU WiFi adapter implementation.

## MediaTek MT7921U Chipset Specifications

### Core Architecture

#### Processor Specifications
- **Main CPU**: ARM Cortex-M33 @ 200MHz
- **DSP**: MediaTek proprietary digital signal processor
- **Memory**: 
  - Internal SRAM: 1MB
  - External PSRAM: Up to 8MB (device dependent)
  - Flash: SPI NOR up to 16MB (for standalone operation)

#### Manufacturing Process
- **Process Node**: 12nm FinFET
- **Package Type**: QFN-68 (8mm × 8mm × 0.9mm)
- **Operating Temperature**: -40°C to +85°C
- **Storage Temperature**: -40°C to +125°C

### Wireless Specifications

#### 802.11ax (WiFi 6) Features
- **Maximum PHY Rate**: 573.5 Mbps (2×2 MIMO, 80MHz, 1024-QAM)
- **Spatial Streams**: 2×2 MIMO (2 TX, 2 RX)
- **Channel Width**: 20MHz, 40MHz, 80MHz
- **Modulation**: BPSK, QPSK, 16-QAM, 64-QAM, 256-QAM, 1024-QAM
- **Guard Interval**: 0.8μs, 1.6μs, 3.2μs

#### Frequency Bands
- **2.4GHz Band**:
  - Frequency Range: 2400-2485 MHz
  - Channels: 1-14 (country dependent)
  - Channel Width: 20MHz, 40MHz
  - Maximum Power: +20 dBm (100mW)

- **5GHz Band**:
  - Frequency Range: 5150-5875 MHz
  - Channels: 36-165 (country dependent)
  - Channel Width: 20MHz, 40MHz, 80MHz
  - Maximum Power: +23 dBm (200mW)

#### Legacy Standards Support
- **802.11a**: OFDM, up to 54 Mbps
- **802.11b**: DSSS/CCK, up to 11 Mbps
- **802.11g**: OFDM/CCK, up to 54 Mbps
- **802.11n**: MIMO-OFDM, up to 300 Mbps (2×2, 40MHz)
- **802.11ac**: VHT-OFDM, up to 867 Mbps (2×2, 80MHz, 256-QAM)

### Advanced WiFi 6 Features

#### OFDMA (Orthogonal Frequency Division Multiple Access)
- **Uplink OFDMA**: Support for multi-user uplink
- **Downlink OFDMA**: Support for multi-user downlink
- **Resource Units**: 26, 52, 106, 242, 484, 996-tone RUs
- **BSS Coloring**: 6-bit BSS color for interference mitigation

#### MU-MIMO (Multi-User MIMO)
- **Downlink MU-MIMO**: Up to 2 users simultaneously
- **Beamforming**: Explicit beamforming support
- **Sounding**: Channel state information feedback

#### Power Management
- **Target Wake Time (TWT)**: Individual and broadcast TWT
- **BSS Max Idle Period**: Extended sleep periods
- **Spatial Reuse**: Enhanced spatial reuse operation

### Security Features

#### Hardware Acceleration
- **AES Engine**: 128/256-bit encryption/decryption
- **Hash Engine**: SHA-1, SHA-256, SHA-384, SHA-512
- **Random Number Generator**: True hardware RNG
- **Key Storage**: Secure key slots (16 keys)

#### Supported Security Protocols
- **WPA3**: Personal and Enterprise
- **WPA2**: Personal and Enterprise  
- **WEP**: 64-bit and 128-bit (legacy)
- **802.1X**: RADIUS authentication
- **PMF**: Protected Management Frames (mandatory for WPA3)

#### WPA3 Enhancements
- **SAE**: Simultaneous Authentication of Equals
- **OWE**: Opportunistic Wireless Encryption
- **DPP**: Device Provisioning Protocol (WiFi Easy Connect)
- **Enhanced Open**: Improved open network security

### USB Interface Specifications

#### USB Controller
- **USB Version**: USB 3.0/3.1 Gen 1 (SuperSpeed)
- **Backward Compatibility**: USB 2.0 High Speed
- **Maximum Throughput**: 5 Gbps (theoretical), ~600 Mbps (practical)
- **Power Consumption**: USB Bus Powered (5V)

#### USB Descriptors
- **Vendor ID**: 0x0E8D (MediaTek Inc.)
- **Product ID**: 0x7961 (MT7921U)
- **Device Class**: Vendor Specific (0xFF)
- **Interface Class**: Vendor Specific (0xFF)
- **Number of Interfaces**: 1

#### Endpoint Configuration
- **Control Endpoint**: EP0 (Bidirectional, 64 bytes)
- **Bulk OUT Endpoints**: 
  - EP1: Data transmission (512 bytes, USB 2.0) / (1024 bytes, USB 3.0)
  - EP2: Management frames (512/1024 bytes)
- **Bulk IN Endpoints**:
  - EP3: Data reception (512/1024 bytes)  
  - EP4: Management responses (512/1024 bytes)
- **Interrupt Endpoint**: EP5 (Status/Events, 64 bytes, 1ms interval)

### Power Specifications

#### Power Consumption
- **Idle Power**: 200-400mA @ 5V (1-2W)
- **Active Power (RX)**: 600-800mA @ 5V (3-4W)
- **Active Power (TX)**: 850-1200mA @ 5V (4.25-6W)
- **Sleep Mode**: 50-100mA @ 5V (0.25-0.5W)

#### Power Management States
- **D0 (Working)**: Fully operational
- **D1 (Low Power)**: USB suspend, radio off
- **D2 (Standby)**: Deep sleep, maintain connection
- **D3 (Off)**: Complete power down

#### Thermal Characteristics
- **Junction Temperature**: 150°C maximum
- **Thermal Resistance**: 25°C/W (junction to case)
- **Operating Range**: -40°C to +85°C ambient
- **Thermal Shutdown**: 120°C junction temperature

## Alfa AWUS036AXNU Specifications

### Product Overview
- **Model**: AWUS036AXNU  
- **Chipset**: MediaTek MT7921U
- **Form Factor**: USB 3.0 Adapter with detachable antennas
- **Compatibility**: Windows, Linux, macOS (driver dependent)

### Physical Specifications

#### Dimensions and Weight
- **Adapter Size**: 95mm × 25mm × 12mm (L × W × H)
- **Weight**: 45 grams (without antennas)
- **Antenna Length**: 215mm (each antenna)
- **Cable Length**: 1.5m USB 3.0 cable included

#### Build Quality
- **Housing Material**: High-grade ABS plastic
- **Color**: Matte black finish
- **LED Indicators**: Power/Activity LED
- **Certification**: FCC, CE, IC certified

### Antenna System

#### Antenna Specifications
- **Type**: Dual-band omnidirectional dipole
- **Quantity**: 2 × detachable antennas (2×2 MIMO)
- **Connector**: RP-SMA (Reverse Polarity SMA)
- **Gain**: 
  - 2.4GHz: 5 dBi
  - 5GHz: 6 dBi
- **Polarization**: Linear (vertical)

#### Antenna Pattern
- **2.4GHz Pattern**: Omnidirectional in horizontal plane
- **5GHz Pattern**: Omnidirectional in horizontal plane  
- **VSWR**: < 2.0:1 across operating frequencies
- **Impedance**: 50Ω

#### Range Performance
- **Indoor Range**: Up to 100 meters (line of sight)
- **Outdoor Range**: Up to 1000 meters (line of sight, optimal conditions)
- **Penetration**: Excellent through walls and obstacles

### Electrical Specifications

#### Operating Parameters
- **Input Voltage**: 5V DC (USB bus powered)
- **Current Draw**: 
  - Idle: 400mA
  - Active: 800-1200mA
  - Peak TX: 1200mA
- **Operating Temperature**: 0°C to +70°C
- **Storage Temperature**: -20°C to +85°C
- **Humidity**: 10-90% RH (non-condensing)

#### RF Performance
- **Transmit Power**:
  - 2.4GHz: 20 dBm (100mW) maximum
  - 5GHz: 23 dBm (200mW) maximum
- **Receive Sensitivity**:
  - 2.4GHz: -80 dBm (11n HT20 MCS7)
  - 5GHz: -77 dBm (11ac VHT80 MCS9)

### Performance Characteristics

#### Data Rates

##### 802.11ax (WiFi 6) Rates
| Channel Width | Spatial Streams | Modulation | Data Rate |
|---------------|----------------|------------|-----------|
| 20MHz | 2×2 | 1024-QAM | 287 Mbps |
| 40MHz | 2×2 | 1024-QAM | 574 Mbps |
| 80MHz | 2×2 | 1024-QAM | 1201 Mbps* |

*Theoretical maximum; actual MT7921U supports up to 573.5 Mbps

##### 802.11ac (WiFi 5) Rates
| Channel Width | Spatial Streams | Modulation | Data Rate |
|---------------|----------------|------------|-----------|
| 20MHz | 2×2 | 256-QAM | 173 Mbps |
| 40MHz | 2×2 | 256-QAM | 400 Mbps |
| 80MHz | 2×2 | 256-QAM | 867 Mbps |

##### 802.11n (WiFi 4) Rates
| Channel Width | Spatial Streams | Data Rate |
|---------------|----------------|-----------|
| 20MHz | 2×2 | 144 Mbps |
| 40MHz | 2×2 | 300 Mbps |

#### Latency Performance
- **Average Latency**: 1-3ms (local network)
- **Gaming Performance**: Excellent for real-time applications
- **Jitter**: < 1ms variation typical

### Environmental Specifications

#### Operating Environment
- **Temperature Range**: 0°C to +70°C
- **Humidity Range**: 10% to 90% RH (non-condensing)
- **Altitude**: Up to 3000 meters
- **Vibration**: 5-500 Hz, 2G acceleration

#### Storage Environment  
- **Temperature Range**: -20°C to +85°C
- **Humidity Range**: 5% to 95% RH
- **Packaging**: Anti-static bag with foam protection

### Compatibility Matrix

#### Operating System Support
| OS | Version | Driver Status | Features |
|----|---------|---------------|----------|
| Windows 10 | 1909+ | Native | Full |
| Windows 11 | All | Native | Full |
| Linux | Kernel 5.4+ | MT76 Driver | Full |
| macOS | 10.15+ | Limited | Basic |
| Android | 9.0+ | OTG Support | Limited |

#### USB Host Requirements
- **USB 3.0 Port**: Recommended for optimal performance
- **USB 2.0 Port**: Supported with reduced throughput
- **Power Requirements**: Must supply 1.2A minimum
- **Cable Length**: Maximum 5 meters with active extension

### Regulatory Compliance

#### Frequency Allocations
- **United States (FCC)**:
  - 2.4GHz: Channels 1-11
  - 5GHz: Channels 36-165 (varies by power level)
- **Europe (ETSI)**:
  - 2.4GHz: Channels 1-13  
  - 5GHz: Channels 36-140
- **Japan (MIC)**:
  - 2.4GHz: Channels 1-14
  - 5GHz: Channels 36-140

#### Certification Standards
- **FCC Part 15**: United States
- **ISED RSS**: Canada  
- **CE/RED**: European Union
- **IC**: Industry Canada
- **RoHS**: Lead-free compliance
- **WEEE**: Electronic waste directive

#### SAR Compliance
- **Body SAR**: < 0.5 W/kg
- **Extremities SAR**: < 2.0 W/kg
- **Test Distance**: 20cm minimum

### Mechanical Specifications

#### Connector Details
- **USB Connector**: USB-A 3.0 (SuperSpeed)
- **Antenna Connectors**: RP-SMA female (2 ports)
- **LED Position**: Top center of device
- **Ventilation**: Side vents for thermal management

#### Mounting Options
- **Desktop Stand**: Included adjustable stand
- **Wall Mount**: Optional bracket available
- **Magnetic Mount**: Bottom magnetic base
- **Tripod Mount**: 1/4"-20 thread compatible

### Package Contents

#### Standard Package
- 1 × AWUS036AXNU USB 3.0 WiFi Adapter
- 2 × 5/6 dBi Dual-band Antennas
- 1 × USB 3.0 Extension Cable (1.5m)
- 1 × Desktop Stand
- 1 × Quick Installation Guide
- 1 × Driver CD (Windows)

#### Optional Accessories
- High-gain directional antennas (9-15 dBi)
- USB 3.0 active extension cables
- Wall mounting bracket
- Magnetic antenna bases
- Pigtail cables (various lengths)

## Comparative Analysis

### Versus Competitors

#### vs. Intel AX200/AX210
| Feature | MT7921U | Intel AX200 |
|---------|---------|-------------|
| Interface | USB 3.0 | M.2/PCIe |
| Max Speed | 573 Mbps | 2400 Mbps |
| Bands | 2.4/5GHz | 2.4/5/6GHz |
| Power | Bus powered | PCIe powered |
| Portability | Excellent | Internal only |

#### vs. Realtek RTL8852AU
| Feature | MT7921U | RTL8852AU |
|---------|---------|-----------|
| Max Speed | 573 Mbps | 1201 Mbps |
| Linux Support | Excellent | Limited |
| Power Efficiency | Good | Moderate |
| Price Point | Mid-range | Budget |

### Advantages of MT7921U
- **Excellent Linux Support**: Native kernel driver (MT76)
- **Stable Performance**: Mature chipset with proven reliability  
- **Power Efficiency**: Optimized for USB bus power
- **Wide Compatibility**: Works across multiple OS platforms
- **Professional Features**: Monitor mode, packet injection support

### Limitations
- **Not Latest Generation**: MT7921U vs newer MT7925 series
- **Limited 6GHz**: No WiFi 6E support (6GHz band)
- **USB Bottleneck**: Limited by USB 3.0 throughput
- **Heat Generation**: Can get warm during heavy use

## Technical Implementation Notes

### Driver Architecture Integration
- **Linux Kernel**: MT76 driver framework
- **Module Dependencies**: mt76 → mt76-usb → mt7921u
- **Firmware Requirements**: Binary blobs for MCU and RAM code
- **Configuration**: Standard mac80211/cfg80211 APIs

### Performance Optimization
- **USB Buffer Tuning**: Adjustable buffer sizes for throughput
- **Power Management**: ASPM and runtime PM support  
- **Interrupt Coalescing**: Reduces CPU overhead
- **Queue Management**: Multiple TX/RX queues for QoS

### Development and Testing
- **Reference Design**: Available from MediaTek
- **Development Tools**: MT76 driver debugging interface
- **Test Modes**: Production test modes available
- **Certification Testing**: Pre-certified modules available

This comprehensive specification covers all aspects of the MT7921U chipset and Alfa AWUS036AXNU implementation, providing the technical foundation for development, integration, and support activities.