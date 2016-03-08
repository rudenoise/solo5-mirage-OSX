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

## Current output


```
WARNING: Image format was not specified for 'disk.img' and probing guessed raw.
         Automatically detecting the format is dangerous for raw images, write operations on block 0 will be restricted.
         Specify the 'raw' format explicitly to remove the restrictions.
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


 35 *.annot
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
```

```
# ifconfig bridge0
bridge0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=3<RXCSUM,TXCSUM>
	ether a2:99:9b:20:7b:00
	inet6 fe80::a099:9bff:fe20:7b00%bridge0 prefixlen 64 scopeid 0x9
	inet 169.254.65.18 netmask 0xffff0000 broadcast 169.254.255.255
	Configuration:
		id 0:0:0:0:0:0 priority 0 hellotime 0 fwddelay 0
		maxage 0 holdcnt 0 proto stp maxaddr 100 timeout 1200
		root id 0:0:0:0:0:0 priority 0 ifcost 0 port 0
		ipfilter disabled flags 0x2
	member: en1 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 5 priority 0 path cost 0
	member: en2 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 6 priority 0 path cost 0
	member: tap0 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 14 priority 0 path cost 0
	Address cache:
		52:54:0:12:34:56 Vlan1 tap0 767 flags=0<>
	nd6 options=1<PERFORMNUD>
	media: autoselect
	status: active
```


## Links

* [Solo5](https://github.com/djwillia/solo5/tree/mirage)
* [Networking bridging on Mac OS X (Mavericks) with QEMU](http://drupal.bitfunnel.net/drupal/macosx-bridge-qemu)

