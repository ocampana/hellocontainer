SUBDIRS = src

if ARCHITECTURE_386
ARCHITECTURE=386
endif

if ARCHITECTURE_AMD64
ARCHITECTURE=amd64
endif

if ARCHITECTURE_ARM
ARCHITECTURE=arm
endif

if ARCHITECTURE_ARM64
ARCHITECTURE=arm64
endif

if OS_LINUX
OS=linux
endif

container:
	rm -rf $(abs_top_builddir)/$(PACKAGE)
	mkdir -p $(abs_top_builddir)/$(PACKAGE)/blobs/sha256
	echo -n {\"imageLayoutVersion\": \"1.0.0\"} > $(abs_top_builddir)/$(PACKAGE)/oci-layout
	mkdir layer_dir
	DESTDIR=$(abs_top_builddir)/layer_dir/rootfs make install-strip
	find $(abs_top_builddir)/layer_dir/ -print0 | xargs -0r touch --no-dereference --date=@`git log -1 --pretty=%ct`
	tar --sort=name -C $(abs_top_builddir)/layer_dir/rootfs/ -cf $(abs_top_builddir)/layer_dir/rootfs.tar .
	touch --no-dereference --date=@`git log -1 --pretty=%ct` $(abs_top_builddir)/layer_dir/rootfs.tar
	gzip -nk $(abs_top_builddir)/layer_dir/rootfs.tar
	cp $(abs_top_builddir)/layer_dir/rootfs.tar.gz $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/`sha256sum $(abs_top_builddir)/layer_dir/rootfs.tar.gz | cut -d ' ' -f 1`
	echo -n "{\"created\":\"`date -d @$$(git log -1 --pretty=%ct) -Ins -u | cut -d '+' -f 1 | sed  s/,/./g`Z\",\"author\":\"$(PACKAGE_BUGREPORT)\",\"architecture\":\"$(ARCHITECTURE)\",\"os\":\"$(OS)\",\"config\":{\"Env\":[\"PATH=/usr/local/bin\"],\"Cmd\":[\"/usr/local/bin/hellocontainer\"]},\"rootfs\":{\"type\":\"layers\",\"diff_ids\":[\"sha256:`sha256sum $(abs_top_builddir)/layer_dir/rootfs.tar | cut -d ' ' -f 1`\"]}}" > $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration
	cp $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/`sha256sum $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration | cut -d ' ' -f 1`
	echo -n "{\"schemaVersion\":2,\"config\":{\"mediaType\":\"application/vnd.oci.image.config.v1+json\",\"digest\":\"sha256:`sha256sum $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration | cut -d ' ' -f 1`\",\"size\":`du -b $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration | cut -f 1`},\"layers\":[{\"mediaType\":\"application/vnd.oci.image.layer.v1.tar+gzip\",\"digest\":\"sha256:`sha256sum $(abs_top_builddir)/layer_dir/rootfs.tar.gz | cut -d ' ' -f 1`\",\"size\":`du -b $(abs_top_builddir)/layer_dir/rootfs.tar.gz | cut -f 1`}]}" > $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest
	cp $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/`sha256sum $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest | cut -d ' ' -f 1`
	echo -n "{\"schemaVersion\":2,\"manifests\":[{\"mediaType\":\"application/vnd.oci.image.manifest.v1+json\",\"digest\":\"sha256:`sha256sum $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest | cut -d ' ' -f 1`\",\"size\":`du -b $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest | cut -f 1`}]}" > $(abs_top_builddir)/$(PACKAGE)/index.json
	rm $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_configuration
	rm $(abs_top_builddir)/$(PACKAGE)/blobs/sha256/image_manifest
	find $(abs_top_builddir)/$(PACKAGE) -print0 | xargs -0r touch --no-dereference --date=@`git log -1 --pretty=%ct`
	rm -rf layer_dir

clean-local:
	rm -rf layer_dir
	rm -rf $(PACKAGE)
