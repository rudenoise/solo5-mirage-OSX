# solo5-mirage-OSX

Run a mirageOS unikernel built with solo5 on QEMU on OSX

Checkout the _mirage_ branch of the [Solo5](https://github.com/djwillia/solo5/tree/mirage) repo.

Follow the instructions for building and running the Docker container.

Inside the container, build one of the examples:
```sh
make config_www
make
make kvm
ls
```

There should be an _iso_ file for the kernel:
```
kernel.iso
```

Copy the file from the container to the host (OSX):
```
docker cp solo5-mirage:/home/solo5/solo5/kernel.iso ./
```

Install qemu with brew:
```sh
brew install qemu
```

Run the iso:
```sh
qemu-system-x86_64 -s -nographic -name foo -m 1024 -cdrom kernel.iso -net nic,model=virtio -net tap,ifname=bridge0,script=kvm-br.bash -drive file=disk.img,if=virtio -boot d
```

## Links

* [Solo5](https://github.com/djwillia/solo5/tree/mirage)
* [Networking bridging on Mac OS X (Mavericks) with QEMU](http://drupal.bitfunnel.net/drupal/macosx-bridge-qemu)

