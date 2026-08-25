# raspi-timesync

## Intro

Time sync is important, and in some fields, REALLY important.


### Plan

Create a Master time sync box leveraging Raspi CM4, POE Box and GPS signals to create an accurate time system.

We will leverage Fedora Core/BootC to build the system and possibly use RHEM to manage the device.

### Build Container

podman build --platform linux/arm64 -t quay.io/markd/timesync:latest -f Docker/Containerfile .

### Create raw image

podman run \
    --rm \
    -it \
    --privileged \
    --pull=newer \
    --security-opt label=type:unconfined_t \
    -v ./config.toml:/config.toml:ro \
    -v ./output:/output \
    -v /var/lib/containers/storage:/var/lib/containers/storage \
    quay.io/centos-bootc/bootc-image-builder:latest \
    --type qcow2 \
    --use-librepo=True \
    --rootfs ext4 \
    registry.xphyrlab.net/markd/timesync:latest

> On macOS, the podman machine must be running in rootful mode:
>
>$ podman machine stop   # if already running
>Waiting for VM to exit...
>Machine "podman-machine-default" stopped successfully
>$ podman machine set --rootful
>$ podman machine start

### Booting Raspi from this

Its not possible (at least I cant figure it out yet) to get the raspi to boot the first time from an original bootc image, instead we need to use fedoraIOT to bootstrap the sd card, and then we can bootc switch to our image.

### raw image needs to be updated once for boot on raspi

will need a linux host to make this change, or need mac with qemu installed to boot it and add some files

`arm-image-installer` looks like it can be used to make a bootable image
