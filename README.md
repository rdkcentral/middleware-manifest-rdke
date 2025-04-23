# middleware-manifest-rdke
Manifest to build RDKE middleware layer for RDKV platforms

## Build Steps
Note: use latest `TAG` for released versions or `develop` branch for develop HEAD. Refer product layer for supported `machine-name`.
```bash
# Initialize the repository
repo init -u https://github.com/rdkcentral/middleware-manifest-rdke.git -m <product-specific.xml> -b <branch>

# Synchronize the repository
repo sync --no-clone-bundle --no-tags

# Build the project
MACHINE=<machine-name> source ./scripts/setup-environment
bitbake ${MLPREFIX}packagegroup-middleware-layer
bitbake ${MLPREFIX}middleware-test-image
```
Eg: Steps to build the reference RaspberryPi middleware stack from `develop` branch
```bash
# Initialize the repository
repo init -u https://github.com/rdkcentral/middleware-manifest-rdke.git -m raspberrypi4-64.xml -b develop

# Synchronize the repository
repo sync --no-clone-bundle --no-tags

# Build the project
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
bitbake lib32-packagegroup-middleware-layer
bitbake lib32-middleware-test-image
```

## License Details
This project is distributed under the terms outlined in the associated [License](LICENSE) and [Notice](NOTICE) files. Please review these files for detailed information.

## Release and change Details
For a comprehensive list of changes, updates, and release history, refer to the [Changelog](CHANGELOG.md).
