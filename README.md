# Android Device Provisioner — Cloud Phone Fleet SDK

> Provision, manage, and orchestrate Android devices in the cloud at scale

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Platform: Android](https://img.shields.io/badge/Platform-Android-green.svg)](https://www.android.com/)

A lightweight **device provisioning SDK** for cloud Android fleets. Takes a device serial or ADB connect string, provisions it with a given profile (OS version, region, app set), and exposes a gRPC + REST control plane for fleet management.

## Use Cases

- **Multi-account matrix management** — isolate each account session on its own provisioned device
- **Cross-region deployment** — bind a device to a specific geo IP for content targeting
- **24/7 cloud AFK farming** — keep devices online without physical hardware
- **CI/CD device lab** — on-demand ephemeral device allocation for test runners

## Architecture

```
┌──────────────────────────────────────────────┐
│         Device Provisioner Service           │
│   (gRPC: device allocate / release / status) │
└──────────┬───────────────────┬───────────────┘
           │                   │
    ┌──────▼──────┐     ┌──────▼──────┐
    │  Android #1 │     │  Android #N │
    │  Device Pod  │ ... │  Device Pod  │
    └─────────────┘     └─────────────┘
           │                   │
    ┌──────▼───────────────────▼──────┐
    │  ADB Connect / Virtual Network   │
    │  (per-device independent IP)      │
    └──────────────────────────────────┘
```

## Features

| Feature | Description |
|---------|-------------|
| Device Pool | Pre-warm N devices; allocate on demand via gRPC call |
| Profile Provisioning | Apply OS image, installed APK set, and device settings per session |
| Independent IP Routing | Bind each device to its own proxy/exit-node IP |
| Fleet Status Dashboard | REST endpoint to list online/idle/allocated device states |
| Health Probe | Periodic ADB heartbeat + screen-state check; auto-recover crashed pods |
| ADB Gateway | Expose ADB over TLS so external automation clients can connect |

## Quick Start

```bash
# Install
pip install android-device-provisioner

# Start provisioner (requires ADB on PATH)
python -m provisioner.server --port 50051 --device-pool 5

# Allocate a device for a session
grpcurl -plaintext -d '{"profile_id": "us-east-1"}' localhost:50051 provisioner.DevicePool/Allocate
```

## REST API

```bash
# List all devices
curl http://localhost:8080/devices

# Allocate device with profile
curl -X POST http://localhost:8080/allocate -d '{"profile": "sg-region-1"}'

# Release device
curl -X POST http://localhost:8080/release/<device_id>
```

## Profiles

| Profile ID | OS | Region | Notes |
|------------|----|--------|-------|
| `default` | Android 12+ | any | Base install |
| `sg-region-1` | Android 13 | Singapore | Geo-targeted IP |
| `us-east-1` | Android 14 | Virginia, US | US carrier SIM |
| `eu-west-1` | Android 13 | Dublin | EU compliance |

## Contributing

Issues and pull requests welcome. For major changes, please open an issue first.

---

## About & Contact

**QTPhone** — Global Independent IP Cloud Android Devices

- 🌐 Official Website: [https://www.qtphone.com/](https://www.qtphone.com/)
- 💬 WhatsApp: **@along915**
- 📧 Gmail: **ailong9281@gmail.com**
- ✈️ Telegram: **@Alongyun**
