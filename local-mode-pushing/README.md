# Pushing to local device

## `balena build`

Build the image locally:

```sh
❯ cd some-image
❯ docker build . -t hgaiser/some-image
```

Optionally verify that it exists with `docker image ls | grep some-image`.

Now try building with `balena build`:

```sh
❯ cd ../local
❯ balena build --fleet h_gaiser/test
[Build]   Building services...
[Build]   some-image Preparing...
[Info]    Building for amd64/genericx86-64-ext
[Build]   Built 1 service in 0:02
[Error]   Build failed.
(HTTP code 404) unexpected - pull access denied for hgaiser/some-image, repository does not exist or may require 'docker login': denied: requested access to the resource is denied

Additional information may be available with the `--debug` flag.

For further help or support, visit:
https://www.balena.io/docs/reference/balena-cli/#support-faq-and-troubleshooting
```

Apparently `balena build` can **only** pull images from registries, it can't use local images.

## `balena deploy`

`balena deploy` pushes local images to balenaCloud, however `balena deploy` doesn't seem to have any support for local devices.
Having this support would be ideal, since it means you can directly push images to balena devices running in local mode.
Other solutions depend on a registry that needs to be setup.

## Pushing to VM

1. Create a fleet on balenaCloud dashboard.
2. Download a balenaOS image by creating a new device for QEMU X86 64bit and downloading it from that page to `./vm`.
3. Run the device using the provided command (also exposes some ports for balena communication):

```sh
qemu-system-x86_64 \
	-device ahci,id=ahci \
	-drive file=vm/balena-cloud-simple-test-qemux86-64-2.83.18+rev5-dev-v12.10.3.img,media=disk,cache=none,format=raw,if=none,id=disk \
	-device ide-hd,drive=disk,bus=ahci.0 \
	-net nic,model=virtio \
	-net user,hostfwd=tcp::22222-:22222,hostfwd=tcp::48484-:48484,hostfwd=tcp::2375-:2375 \
	-m 512 \
	-nographic \
	-machine type=pc,accel=kvm \
	-smp 4 \
	-cpu host
```

4. Check the balena dashboard, it should now show a new device.
5. Run the device in local mode using the balena dashboard.
6. Push the local image to the local device:

```sh
❯ cd some-image
❯ docker build . -t hgaiser/some-image
❯ cd ../local
❯ balena push 127.0.0.1
[Info]    Starting build on device 127.0.0.1
Some services failed to build:
        some-image: (HTTP code 404) unexpected - pull access denied for hgaiser/some-image, repository does not exist or may require 'docker login': denied: requested access to the resource is denied


Additional information may be available with the `--debug` flag.

For further help or support, visit:
https://www.balena.io/docs/reference/balena-cli/#support-faq-and-troubleshooting
```

Similarly, it is trying to pull images from dockerhub, instead of pushing local images to the device.

## Local registry

1. Run a local registry:

```sh 
❯ docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

2. Run the VM:

```sh
qemu-system-x86_64 \
	-device ahci,id=ahci \
	-drive file=vm/balena-cloud-simple-test-qemux86-64-2.83.18+rev5-dev-v12.10.3.img,media=disk,cache=none,format=raw,if=none,id=disk \
	-device ide-hd,drive=disk,bus=ahci.0 \
	-net nic,model=virtio \
	-net user,hostfwd=tcp::22222-:22222,hostfwd=tcp::48484-:48484,hostfwd=tcp::2375-:2375 \
	-m 512 \
	-nographic \
	-machine type=pc,accel=kvm \
	-smp 4 \
	-cpu host
```

3. Push the images from the local registry to the VM:

```sh
❯ cd some-image
❯ docker build . -t localhost:5000/hgaiser/some-image
❯ docker push localhost:5000/hgaiser/some-image
❯ cd ../local-registry
❯ balena push 127.0.0.1
[Info]    Starting build on device 127.0.0.1
Some services failed to build:
        some-image: (HTTP code 500) server error - Get https://10.0.2.2:5000/v2/: http: server gave HTTP response to HTTPS client


Additional information may be available with the `--debug` flag.

For further help or support, visit:
https://www.balena.io/docs/reference/balena-cli/#support-faq-and-troubleshooting
```

Note that `10.0.2.2` is the address of the host from within the VM.
To verify that communication is possible, run this in the VM:

```sh
root@c1f0175:~# curl 10.0.2.2:5000/v2/_catalog
{"repositories":["hgaiser/some-image"]}
```

Also note that running the Docker registry local in the VM does seem to work, but preferably this is not used because it nearly doubles the VM size.
