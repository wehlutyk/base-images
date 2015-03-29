Base Images
===========

Base images for Eauchat, along with methods to rebuild them from scratch.

Building the images
-------------------

Images are built using [Packer](http://www.packer.io/). An image is defined with two files:

* `<image-name>/packer.json`: the packer json definition, which configures the way packer builds the image
* `<image-name>/httpdir/preseed.cfg`: the preseed file fed to the debian installer

To build an image:
```shell
cd <image-name>   # This is important so that packer finds the `httpdir` folder
packer build packer.json
```

On a reasonable DSL connection the build process shouldn't take more than 30 minutes. Once finished, the image is available as `<image-name>/image/base-<image-name>.qcow2`.

Using the images
----------------

Any one of the images can be used with qemu. Here's an example:

```bash
qemu-system-x86_64 \
    -m 1024M \
    -drive file=trusty/image/base-trusty.qcow2,if=virtio \
    -name base-trusty \
    -redir tcp:2224::22 \
    -vnc 0.0.0.0:47 \
    -machine type=pc-1.0,accel=kvm \
    -display sdl \
    -netdev user,id=user.0 \
    -device virtio-net,netdev=user.0
```

Note that the hostname configured on the disk image is always `base-<image-name>` (in this example, `<image-name>` is `trusty`).

There is a single sudoer user on the system, name `ubuntu` and password `ubuntu`, and you can ssh into the box with `ssh -p 2224 ubuntu@localhost`.

TODO
----

write a packer plugin that can copy an existing qemu image and provision it, instead of starting from scratch each time.
