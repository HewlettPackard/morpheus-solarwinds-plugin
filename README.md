# Morpheus SolarWinds IPAM Plugin

This plugin provides an IPAM integration between [SolarWinds IP Address Manager](https://www.solarwinds.com/ip-address-manager) and [Morpheus](https://morpheusdata.com). It enables subnet sync, IP address sync, IP reservation, IP release, and hostname metadata updates from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.2 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-solarwinds-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **SolarWinds IPAM** integration type will appear after the plugin loads.

## Configuration

When adding a SolarWinds IPAM integration in Morpheus (**Infrastructure → Network → IPAM → Add IPAM Integration**), provide the following:

| Field | Description |
|-------|-------------|
| **API Url** | SolarWinds Information Service endpoint, e.g. `https://x.x.x.x:17778`. |
| **Credentials** | Morpheus username/password credential for SolarWinds. |
| **Username** | Local username field used when not selecting a stored credential. |
| **Password** | Local password field used when not selecting a stored credential. |
| **Throttle Rate** | Optional API throttling rate for SolarWinds requests. |
| **Disable SSL SNI Verification** | Disable SSL SNI verification for the SolarWinds endpoint. |
| **Inventory Existing** | Import existing IP node records from synced subnets. |

## Features

### IPAM Sync
The plugin registers an `IPAMProvider` for SolarWinds IPAM. The following resources are discovered and kept in sync from SolarWinds:

- **Subnets** — SolarWinds `IPAM.Subnet` records as Morpheus network pools
- **Network Ranges** — calculated pool ranges from each synced subnet CIDR
- **IP Nodes** — SolarWinds `IPAM.IPNode` records when **Inventory Existing** is enabled
- **Hostnames** — SolarWinds `DnsBackward` values as Morpheus IP hostnames
- **IP Status** — SolarWinds IP node status mapped into Morpheus assigned, reserved, and transient records

Any additions, updates, and removals in SolarWinds are reflected in Morpheus on the next IPAM refresh.

### IP Reservation
Morpheus can reserve addresses in synced SolarWinds subnets. Supported operations include:

- Reserve the next available IP address with SolarWinds `StartIpReservation` and `FinishIpReservation`
- Allocate a specific available IP address by changing its status to used
- Store the SolarWinds `IpNodeId` on the Morpheus IP record
- Update the SolarWinds `DNSBackward` field with the Morpheus hostname
- Serialize allocation per network pool to avoid overlapping reservations

### IP Release and Updates
Morpheus host record lifecycle operations are mapped to SolarWinds IP nodes. Supported operations include:

- Update SolarWinds `DNSBackward` when a hostname changes
- Mark released addresses available with SolarWinds `ChangeIpStatus`
- Keep Morpheus IP records synchronized with SolarWinds status and hostname changes

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2022 the original author or authors. Licensed under the [Apache License, Version 2.0](LICENSE).
