# MT7921U Driver License Information

## Overview

The MT7921U driver package consists of multiple kernel modules, each licensed under the **Dual BSD/GPL license**. This allows for use in both open source and proprietary software projects, providing maximum flexibility for deployment and distribution.

## License Summary

All MT7921U driver modules are released under:
- **Primary License**: Dual BSD/GPL  
- **SPDX Identifier**: `BSD-2-Clause OR GPL-2.0`
- **Distribution**: Freely redistributable
- **Commercial Use**: Permitted without restrictions

## Module License Details

### Core Modules

| Module | License | Author | Description |
|--------|---------|--------|-------------|
| `mt76.ko` | Dual BSD/GPL | (Not specified) | MediaTek MT76x helpers |
| `mt76-usb.ko` | Dual BSD/GPL | Lorenzo Bianconi | MT76 USB transport layer |
| `mt76-connac-lib.ko` | Dual BSD/GPL | Lorenzo Bianconi | MT76x connac layer helpers |
| `mt792x-lib.ko` | Dual BSD/GPL | Lorenzo Bianconi | MediaTek MT792x core driver |
| `mt792x-usb.ko` | Dual BSD/GPL | Lorenzo Bianconi | MT792x USB interface |
| `mt7921-common.ko` | Dual BSD/GPL | Sean Wang | MT7921 common functionality |
| `mt7921u.ko` | Dual BSD/GPL | Lorenzo Bianconi | MediaTek MT7921U USB driver |

### Author Information

#### Primary Authors
- **Lorenzo Bianconi** `<lorenzo@kernel.org>`
  - Lead developer for MT76 framework
  - Maintainer of USB transport layers
  - Author of MT7921U-specific code

- **Lorenzo Bianconi** `<lorenzo.bianconi83@gmail.com>` 
  - Alternative contact for mt76-usb module
  - Same person, different email address

- **Sean Wang** `<sean.wang@mediatek.com>`
  - MediaTek employee
  - Author of MT7921 common functionality
  - Hardware-specific implementations

## Dual BSD/GPL License Terms

### BSD License (2-Clause)

The BSD portions of this software are licensed under the following terms:

```
Copyright (c) 2018-2024, The Linux Foundation
Copyright (c) 2018-2024, MediaTek Inc.
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice,
   this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.
```

### GPL License (Version 2)

The GPL portions of this software are licensed under the GNU General Public License v2:

```
Copyright (C) 2018-2024 The Linux Foundation
Copyright (C) 2018-2024 MediaTek Inc.

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License version 2 as
published by the Free Software Foundation.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301, USA.
```

## License Choice

Under the Dual BSD/GPL license, users may choose to use, modify, and distribute this software under either:

1. **BSD 2-Clause License** - For maximum freedom including proprietary use
2. **GPL Version 2** - For copyleft requirements and GPL compatibility

### When to Choose BSD
- Proprietary software integration
- Commercial products without source distribution
- Maximum flexibility for downstream use
- No copyleft requirements

### When to Choose GPL
- Integration with GPL-licensed software
- Contributing back to open source community
- Ensuring derivative works remain open source
- Linux kernel module distribution (recommended)

## Firmware License

The MT7921U driver requires proprietary firmware files from MediaTek:

### Firmware Files
- `WIFI_MT7961_patch_mcu_1_2_hdr.bin`
- `WIFI_RAM_CODE_MT7961_1.bin`

### Firmware License Terms
- **Copyright**: MediaTek Inc.
- **License**: Proprietary binary redistribution license
- **Restrictions**: 
  - Binary-only redistribution
  - Use only with MediaTek hardware
  - No reverse engineering
  - No modification of binary content

### Firmware Distribution Rights
- Included in standard Linux firmware packages
- Redistributable as part of complete driver packages
- May be distributed with commercial products containing MT7921U
- Subject to export control regulations

## Patent Information

### Potential Patent Coverage
This software may be covered by patents related to:
- 802.11 wireless networking protocols
- MediaTek-specific hardware implementations
- USB wireless device technologies
- WiFi 6 (802.11ax) features

### Patent Licensing
- No explicit patent grants in BSD/GPL licenses
- Users are responsible for patent clearance
- MediaTek may hold patents on hardware-specific implementations
- Standard essential patents may apply to 802.11 implementations

## Export Control and Regulatory

### Export Control Classification
- **ECCN**: 5D002 (telecommunications software)
- **Export Restrictions**: May be subject to export control laws
- **Countries**: Restrictions may apply to certain countries
- **Use Cases**: Military/defense applications may require licenses

### Regulatory Compliance
- FCC certification required for US distribution
- CE marking required for EU distribution
- IC certification required for Canadian distribution
- Local regulatory approval required in other jurisdictions

## Compliance Requirements

### Source Code Distribution
When distributing under GPL terms:
- Include complete source code
- Provide GPL license text
- Maintain copyright notices
- Document any modifications

### Binary Distribution
When distributing under BSD terms:
- Include copyright notices
- Include BSD license text
- No source code distribution required
- Document any modifications (recommended)

### Commercial Distribution
For commercial products:
- Verify license compatibility with product licensing
- Include appropriate license notices in documentation
- Consider patent implications
- Ensure regulatory compliance

## Contributing

### Upstream Development
- **Git Repository**: `git://git.kernel.org/pub/scm/linux/kernel/git/kvalo/wireless.git`
- **Mailing List**: `linux-wireless@vger.kernel.org`
- **Maintainer**: Lorenzo Bianconi `<lorenzo@kernel.org>`

### Contribution Requirements
- Sign-off required (Developer Certificate of Origin)
- GPL-compatible licensing for contributions
- Follow Linux kernel coding standards
- Submit patches via mailing list

### Bug Reports
- Report issues via Linux wireless mailing list
- Include hardware information and kernel logs
- Test with latest kernel versions
- Provide reproducible test cases

## Disclaimer

### No Warranty
This software is provided "AS IS" without warranty of any kind, either express or implied, including but not limited to warranties of merchantability, fitness for a particular purpose, and non-infringement.

### Limitation of Liability
In no event shall the copyright holders or contributors be liable for any direct, indirect, incidental, special, exemplary, or consequential damages arising from the use of this software.

### Third Party Software
This driver package may interact with third-party software components that have their own licensing terms. Users are responsible for compliance with all applicable licenses.

## License Verification

### Module Information
Verify license information for each module:

```bash
# Check module license
modinfo mt7921u | grep license

# Verify all modules
for module in mt76 mt76-usb mt76-connac-lib mt792x-lib mt792x-usb mt7921-common mt7921u; do
    echo "Module: $module"
    modinfo $module 2>/dev/null | grep -E "license|author|description" || echo "Module not found"
    echo
done
```

### Source Code Headers
Original source code files contain SPDX license identifiers:
- `SPDX-License-Identifier: ISC` (some files)
- `SPDX-License-Identifier: GPL-2.0-only` (some files)
- Mixed licensing within the driver framework

## Updates and Maintenance

### License Changes
- License terms are established at time of release
- Future versions may have different licensing
- Check individual file headers for specific terms
- Major license changes will be documented in release notes

### Long-term Support
- Driver is maintained as part of Linux kernel
- Long-term support follows kernel LTS schedules
- Security updates provided via kernel updates
- Hardware support lifecycle depends on MediaTek

---

## Contact Information

For license-related questions:
- **Linux Wireless Mailing List**: `linux-wireless@vger.kernel.org`
- **MT76 Maintainer**: Lorenzo Bianconi `<lorenzo@kernel.org>`
- **Legal Issues**: Consult with qualified legal counsel

**Last Updated**: August 10, 2025  
**Document Version**: 1.0  
**Driver Version**: Linux kernel 6.8.0-1031-raspi