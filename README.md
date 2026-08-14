# Morpheus SolarWinds IPAM Plugin

The Morpheus SolarWinds IPAM Plugin integrates Morpheus with SolarWinds IP Address Manager (IPAM) to provide IP address management within Morpheus. The plugin communicates with the SolarWinds Information Service (SWIS) API to reserve and release IP addresses from SolarWinds-managed subnets.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### IP Address Management

Allocate and release IP addresses from SolarWinds IPAM subnets within Morpheus. Supports automatic next-available IP selection and existing inventory import. Note: this plugin manages IP addresses only; DNS management on SolarWinds is not implemented.

### Cloud Sync

Morpheus synchronises the following SolarWinds resources for inventory:

- IPAM subnets as Morpheus network pools
- IP node allocations

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.2 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A running SolarWinds Orion platform with the IPAM module and the Information Service (SWIS) REST API accessible from the Morpheus appliance (default port: 17778 over HTTPS)
- A SolarWinds user account with read/write access to IPAM objects
- Network access from the Morpheus appliance to the SolarWinds Orion server on port 17778

---

## Repository structure

```
src/main/groovy/com/morpheusdata/solarwinds/
├── SolarWindsPlugin.groovy    - Plugin entry point; registers SolarWindsProvider
└── SolarWindsProvider.groovy  - IPAMProvider implementation; IPAM operations, sync, OptionTypes
build.gradle, gradle.properties - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-solarwinds-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Networks > IP Pools > Add** and select **SolarWinds** to configure the integration.

---

## Detailed Usage Steps

### Adding a SolarWinds IPAM Integration

1. Go to **Infrastructure > Networks > IP Pools > Add**.
2. Select **SolarWinds** as the pool server type.
3. Enter the **API Url** (e.g. `https://solarwinds.example.com:17778`), **Username**, and **Password**.
4. Optionally configure **Throttle Rate** and **Disable SSL SNI Verification**.
5. Save. Morpheus connects to the SWIS API and syncs available IPAM subnets as network pools.

### Allocating an IP Address

When provisioning an instance on a network backed by a SolarWinds subnet, Morpheus reserves the next available IP by updating the corresponding IP node record in SolarWinds IPAM.

### Releasing an IP Address

When an instance is decommissioned, Morpheus updates the IP node record in SolarWinds to mark it as available again.

---

## API Endpoints

This plugin communicates with the **SolarWinds Information Service (SWIS) REST API** at `https://<solarwinds-host>:17778`. Authentication uses HTTP Basic credentials.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/SolarWinds/InformationService/v3/Json/Query` | GET/POST | SWQL query for subnets and IP nodes |
| `/SolarWinds/InformationService/v3/Json/swis://{host}/Orion/IPAM.IPNode/IpNodeId={id}` | PUT | Reserve or release an IP address |
