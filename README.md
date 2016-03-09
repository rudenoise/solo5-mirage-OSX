# solo5-mirage-OSX

Run a mirageOS unikernel built with solo5 on QEMU on OSX

Check that you have a _bridge0_ interface:
```sh
ifconfig bridge0
```

If not set one up as per [this guide](http://drupal.bitfunnel.net/drupal/macosx-bridge-qemu).

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
disk.img
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
    -net nic,model=virtio \
    -net tap,script=./tap-up.sh,downscript=./tap-down.sh \
    -drive file=disk.img,if=virtio \
    -boot d
```

In another terminal:
```sh
sudo ifconfig tap0 10.0.0.1 alias
```

## Current output

Assuming that you see the output below, the Mirage Site should be up
at (http://10.0.0.2/)[http://10.0.0.2/]

```
            |      ___|
  __|  _ \  |  _ \ __ \
\__ \ (   | | (   |  ) |
____/\___/ _|\___/____/
Found virtio network device with MAC: 54 54 00 55 55 55
host features: 71000ed4: 2 4 6 7 9 10 11 24 28 29 30
Found virtio block device with capacity: 2048 * 512 = 1048576
queue size is 128
DJW: new bindings
getenv(OCAMLRUNPARAM) -> null
getenv(CAMLRUNPARAM) -> null
getenv(PATH) -> null
Unsupported function lseek called in Mini-OS kernel
Unsupported function lseek called in Mini-OS kernel
Unsupported function lseek called in Mini-OS kernel
getenv(OCAMLRUNPARAM) -> null
getenv(CAMLRUNPARAM) -> null
getenv(TMPDIR) -> null
getenv(TEMP) -> null
getenv(DEBUG) -> null
getenv(OMD_DEBUG) -> null
getenv(OMD_FIX) -> null
lib/solo5_stubs.c: connect!!

lib/solo5_stubs.c: WARNING: returning hardcoded MAC
Netif: plugging into tap0 with mac 52:54:00:12:34:56
Netif: connect tap0
tap0 with mac 52:54:00:12:34:56
Attempt to open(/dev/urandom)!
Unsupported function getpid called in Mini-OS kernel
Unsupported function getppid called in Mini-OS kernel
Manager: connect
Manager: configuring
Manager: Interface to 10.0.0.2 nm 255.255.255.0 gw [10.0.0.1]

Manager: connect
Manager: configuring
Manager: Interface to 10.0.0.2 nm 255.255.255.0 gw [10.0.0.1]

ARP: sending gratuitous from 10.0.0.2
Manager: configuration done
Listening on http://localhost/
```

```
# ifconfig tap0
tap0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
    ether 72:22:82:06:d4:00
    inet 10.0.0.1 netmask 0xff000000 broadcast 10.255.255.255
    media: autoselect
    status: active
    open (pid 1698)
```

## Links

* [Solo5](https://github.com/djwillia/solo5/tree/mirage)
* [Networking bridging on Mac OS X (Mavericks) with QEMU](http://drupal.bitfunnel.net/drupal/macosx-bridge-qemu)

