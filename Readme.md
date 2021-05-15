# Hellocontainer - OCI image for embedded system

This is an experimental setup for studying and developing container with the OCI image format for embedded systems.

## Why?

Embedded systems have limited resources. It is very unlikely that an embedded system will have enough NAND space for pulling an image based on a GNU/Linux distribution.

In this project, I explore the possibility of shipping a single statically linked binary in form of an OCI container.

## How can I build and test it?

This is a standard autotools-based project, thus the following commands are necessary:

```bash
ottavio@debian:~/hellocontainer$ ./bootstrap
ottavio@debian:~/hellocontainer$ ./configure
ottavio@debian:~/hellocontainer$ make
ottavio@debian:~/hellocontainer$ make container
```

At this point, the OCI image will ke available in the `hellocontainer` directory. In order to run it, you will need to use `podman`:

```bash
ottavio@debian:~/hellocontainer$ podman run oci:hellocontainer
Getting image source signatures
Copying blob 58b386ba5fa9 [--------------------------------------] 0.0b / 0.0b
Copying config f44537c948 done  
Writing manifest to image destination
Storing signatures
Hello container!
ottavio@debian:~/hellocontainer$
```

As you can see, we have a minimal but complete container that greets us. How big is it? As you cna see, for the x86\_64 architecture it is 316KB

```bash
ottavio@debian:~/hellocontainer$ du -k hellocontainer/
300	hellocontainer/blobs/sha256
304	hellocontainer/blobs
316	hellocontainer/
ottavio@debian:~/hellocontainer$
```

## How can I integrate this solution in my project?

In an autotools project probably you will probably have you sources in the `src` directory. If this is the case, you can use the `Makefile.am` in the top directory of this repository, otherwise you will have to adapt it.

You also will need to put the following lines in your `configure.ac`:

```
AM_CONDITIONAL([ARCHITECTURE_386], [test x"${host_cpu}" = xi386])
AM_CONDITIONAL([ARCHITECTURE_AMD64], [test x"${host_cpu}" = xx86_64])
AM_CONDITIONAL([ARCHITECTURE_ARM], [test x"${host_cpu}" = xarm])
AM_CONDITIONAL([ARCHITECTURE_ARM64], [test x"${host_cpu}" = xaarch64])
AM_CONDITIONAL([OS_LINUX], [test x"${build_os}" = xlinux-gnu])
```
