# solo5-mirage-OSX

_NOT QUITE WORKING_

Run a mirageOS unikernel built with solo5 on QEMU on OSX

Checkout the _mirage_ branch of the [Solo5](https://github.com/djwillia/solo5/tree/mirage) repo.

Follow the instructions for building and running the Docker container.

```sh
docker run djwillia/solo5-mirage
docker exec -it solo5-mirage /bin/bash -l
```

Inside the container, build one of the examples:
```sh
cd ~/solo5
make config_www
make
make kvm
ls
```

There should be _iso_ and _img_ files for the kernel and disk:
```
kernel.iso
```

Copy the file from the container to the host (OSX):
```
docker cp solo5-mirage:/home/solo5/solo5/kernel.iso ./
docker cp solo5-mirage:/home/solo5/solo5/disk.img ./
```

Install qemu with brew:
```sh
brew install qemu
```

Run the iso:
```sh
sudo qemu-system-x86_64 \
    -s -nographic \
    -name mirage \
    -m 1024 \
    -cdrom kernel.iso \
    -net nic,model=virtio,macaddr=54:54:00:55:55:55 \
    -net tap,script=./tap-up.sh,downscript=./tap-down.sh \
    -drive file=disk.img,if=virtio \
    -boot d
```

## Links

* [Solo5](https://github.com/djwillia/solo5/tree/mirage)
* [Networking bridging on Mac OS X (Mavericks) with QEMU](http://drupal.bitfunnel.net/drupal/macosx-bridge-qemu)

