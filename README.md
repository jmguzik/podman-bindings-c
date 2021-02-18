# Podman Go bindings exported to C

## Sample Application for Podman Go Bindings via C shared library

This directory contains a sample application to run Podman operations in
external applications via a set of Go bindings which are later exported 
as C shared library. Due to a limitation of such export, wrappers are
needed around original Go bindings.

There's a single Go file, which uses the Podman Go bindings to provide
interface to execute following steps from C application:

0. Pull Image
1. List Images
2. Start Container
3. List Containers
4. Inspect Container
5. Stop Container

There is example main.c which demonstrates the usage of above operations.

### Running the application

0. Clone the repo and enter to the directory.

1. Ensure podman.socket is activated
```bash
systemctl --user start podman.socket
```

2. Start service (here using podman command)
```bash
podman system service -t 0
```

2. Build go wrapper library (this step will generate C header file as well)
```bash
$ go build -buildmode=c-shared -o libpodc.so libpodc.go
```

3. Build example C application
```bash
$ gcc -O2 -L. -Wl,-rpath=. -Wall -o main main.c -lpodc
```

4. Run it
```bash
$ ./main
Trying to pull registry.fedoraproject.org/fedora-minimal:latest...
Getting image source signatures
Copying blob sha256:2fa61fedb54d576e17d9129a27fbd3c1ff8503b1e0c45622ba8de6a51fb6a9ef
Copying config sha256:fa011f8784baff6b77fc56152b5024c368809c0f4c6b1279dbd9b173f534028a
Writing manifest to image destination
Storing signatures
Listing images...
[k8s.gcr.io/pause:3.2 docker.io/jess/chromium:latest registry.fedoraproject.org/fedora:latest docker.io/library/busybox:latest registry.fedoraproject.org/fedora-minimal:latest]
INFO[0002] Going to start container "a6171bb2da55377d58e0bd05e1469a9a1f4ab83e406050c0521e23f40c5a8518" 
Status of the container with imgName: registry.fedoraproject.org/fedora-minimal:latest is: running
```
### Additional info
0. Go wrapper library is based on demo application: https://github.com/containers/Demos/tree/master/podman_go_bindings
1. For go troubleshooting refer to: https://podman.io/blogs/2020/08/10/podman-go-bindings.html
