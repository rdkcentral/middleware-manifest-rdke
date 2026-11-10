# Middleware Manifest for Raspberry Pi

[![Release](https://img.shields.io/github/v/tag/rdkcentral/middleware-manifest-rdke?label=latest%20tag)](https://github.com/rdkcentral/middleware-manifest-rdke/tags)
[![License](https://img.shields.io/github/license/rdkcentral/middleware-manifest-rdke)](LICENSE)
[![Yocto](https://img.shields.io/badge/Yocto-kirkstone-blue?logo=yoctoproject)](https://docs.yoctoproject.org/)
[![Platform](https://img.shields.io/badge/Platform-RaspberryPi4--64-green?logo=raspberrypi)](https://www.raspberrypi.com/)

Middleware layer manifest for Raspberry Pi — this repo provides the manifest and instructions to build and produce IPK feeds and middleware test images for RDK-based stacks.

---

## Table of Contents

- [Middleware Manifest for Raspberry Pi](#middleware-manifest-for-raspberry-pi)
  - [Table of Contents](#table-of-contents)
  - [Quick Start](#quick-start)
  - [Setup IPK Feeds](#setup-ipk-feeds)
    - [Configure Vendor and Vendor-OSS IPK Feed](#configure-vendor-and-vendor-oss-ipk-feed)
  - [Build Environment](#build-environment)
  - [Building Targets](#building-targets)
  - [Deploying IPK Feed](#deploying-ipk-feed)
    - [Notes](#notes)
  - [Example: Building from `develop` Branch or Release tags](#example-building-from-develop-branch-or-release-tags)
  - [License Details](#license-details)
  - [Release and change Details](#release-and-change-details)

---

## Quick Start

> Use a release tags for stable builds or `develop` for the latest development branch.

```bash
repo init -u https://github.com/rdkcentral/middleware-manifest-rdke.git -m raspberrypi4-64.xml -b <tag-or-branch>
repo sync
```

---

## Setup IPK Feeds

IPK feeds are required for package installation and dependency resolution in downstream layers. You can configure local (file-based) feeds or remote feeds.

> ⚠️ **Important:** When using a local file feed, include the `file:/` prefix and make sure the path ends with a trailing `/`.

### Configure Vendor and Vendor-OSS IPK Feed

Edit the file:
```
rdke/vendor/meta-vendor-release/conf/machine/include/vendor.inc
```

Set the feed path (example):
```
# Configure Vendor OSS IPK's path
VENDOR_OSS_IPK_SERVER_PATH = "file:/${HOME}/community_shared/rdk-arm64-oss-vendor/<Vendor-Oss-IPK-Version>/ipk/"

# Configure Vendor IPK's path
VENDOR_IPK_SERVER_PATH = "file:/${HOME}/community_shared/raspberrypi4-64-rdke-vendor/<Vendor-IPK-Version>/ipk/"
```

---

## Build Environment

Initialize your build environment for the Raspberry Pi 4 (64-bit) server:

```bash
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
```

Enable generation of a deployable IPK feed during the build:

```bash
echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf
```

When enabled, the build will produce `Packages.gz` for each architecture in:
```
./build-raspberrypi4-64-rdke/tmp/deploy/ipk/
```

---

## Building Targets

Choose one (or both) of the following targets depending on your goal:

- **Middleware layer and Middleware layer OSS IPK feed** (produces IPKs consumed by upper layers):
```bash
bitbake lib32-packagegroup-middleware-layer
```

- **Bootable middleware test image** (wrapper that builds a bootable image including middleware packages):
```bash
bitbake lib32-middleware-test-image
```

✅ At least one of the above must succeed to produce a usable IPK feed. Ideally both should succeed.

The generated IPKs are located at:
```
./build-raspberrypi4-64-rdke/tmp/deploy/ipk/rdk-arm64-oss-middleware/
./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-middleware/
```

---

## Deploying IPK Feed

Copy or sync the generated IPK feed to your shared/local repository path:

```bash
rsync -av ./build-raspberrypi4-64-rdke/tmp/deploy/ipk/rdk-arm64-oss-middleware/*  ~/community_shared/rdk-arm64-oss-middleware/<Middleware-Oss-IPK-Version>/ipk/

rsync -av ./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-middleware/*  ~/community_shared/raspberrypi4-64-rdke-middleware/<Middleware-IPK-Version>/ipk/
```

After syncing, confirm the `Packages.gz` files and directory layout are correct for the consumers of the feed.

### Notes

- **IPK feed not found by consumers**  
  Verify `VENDOR_OSS_IPK_SERVER_PATH` and `VENDOR_IPK_SERVER_PATH` has `file:/` prefix and a trailing `/`. Confirm `Packages.gz` exists for each arch.

- **Paths with spaces**  
  Avoid spaces in file-system paths for feeds. Use underscores `_` instead.
  
---

## Example: Building from `develop` Branch or Release tags

A fully copy-paste example to build from `develop` or Release tags:

```bash
# Option A: Building from release tags
repo init -u https://github.com/rdkcentral/middleware-manifest-rdke.git -m raspberrypi4-64.xml -b refs/tags/<tag version>

# Option B: Building from develop Branch
repo init -u https://github.com/rdkcentral/middleware-manifest-rdke.git -m raspberrypi4-64.xml -b develop

repo sync
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf

# Option A: Build middleware IPK feed
bitbake lib32-packagegroup-middleware-layer

# Option B: Build bootable middleware test image
bitbake lib32-middleware-test-image
```

---

## License Details

This project is distributed under the terms outlined in the associated [License](LICENSE) and [Notice](NOTICE) files. Please review these files for detailed information.

## Release and change Details

For a comprehensive list of changes, updates, and release history, refer to the [Changelog](CHANGELOG.md).