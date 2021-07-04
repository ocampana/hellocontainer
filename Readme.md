# Hellocontainer - A reproducible OCI image for embedded system

This is an experimental setup for studying and developing containers with the OCI image format for embedded systems.

## Why?

Embedded systems have limited resources. It is very unlikely that an embedded system will have enough NAND space for pulling an image based on a GNU/Linux distribution.

In this project, I explore the possibility of shipping a single statically linked binary in form of an OCI container. At the same time, I want to implement [reproducible builds](https://reproducible-builds.org/), for better auditing and debugging of the code.

## How can I build and test it?

This is a standard autotools-based project, but to implement reproducible builds it will need gcc >= 8 or clang >= 10 for `-fdebug-prefix-map=OLD=NEW` and `-fmacro-prefix-map=OLD=NEW`. In order to have reproducible builds, you should also use the same compiler and set of libraries. For embedded systems, this can be easily obtained by using frameworks such as [Buildroot](https://buildroot.org/) or [Yocto](https://www.yoctoproject.org/), which can be used to easily re-generate the crosscompiling tools.

Once you have tar and a compiler that match the requirements, the following commands are necessary:

```bash
ottavio@debian:~/hellocontainer$ ./bootstrap
ottavio@debian:~/hellocontainer$ ./configure
ottavio@debian:~/hellocontainer$ make
ottavio@debian:~/hellocontainer$ make container
```

At this point, the OCI image will be available in the `hellocontainer` directory. In order to run it, you will need to use `podman`:

```bash
ottavio@debian:~/hellocontainer$ podman run oci:hellocontainer
Getting image source signatures
Copying blob 022a0f28a4da [--------------------------------------] 0.0b / 0.0b
Copying config f4a9ba0e34 done  
Writing manifest to image destination
Storing signatures
Hello container!
ottavio@debian:~/hellocontainer$
```

As you can see, we have a minimal but complete container that greets us. If you recompile it, you will always end up on your machine with the same ids for `blob` and `config`.

How big is it? As you can see, for the x86\_64 architecture it is 316KB

```bash
ottavio@debian:~/hellocontainer$ du -k hellocontainer/
300	hellocontainer/blobs/sha256
304	hellocontainer/blobs
316	hellocontainer/
ottavio@debian:~/hellocontainer$
```

## How can I integrate this solution in my project?

In an autotools project, probably you will probably have your sources in the `src` directory. If this is the case, you can use the `Makefile.am` in the top directory of this repository, otherwise you will have to adapt it.

You also will need to put the following lines in your `configure.ac`:

```
AM_CONDITIONAL([ARCHITECTURE_386], [test x"${host_cpu}" = xi386])
AM_CONDITIONAL([ARCHITECTURE_AMD64], [test x"${host_cpu}" = xx86_64])
AM_CONDITIONAL([ARCHITECTURE_ARM], [test x"${host_cpu}" = xarm])
AM_CONDITIONAL([ARCHITECTURE_ARM64], [test x"${host_cpu}" = xaarch64])
AM_CONDITIONAL([OS_LINUX], [test x"${build_os}" = xlinux-gnu])
```
