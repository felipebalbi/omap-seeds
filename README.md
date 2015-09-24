omap-seeds
==========

Linux OMAP Randconfig seeds

Why?
===

randconfig is meant to catch build issues - remember it may not always
mean a bootable kernel.

randconfig can be seeded with a file containing Kconfig symbols we
want to manually set on the randconfig. Many folks always start with
RMK's seeds which you can get from [1] and the ones in this repository
are based on those for OMAP2/3/4/5/etc.

[1] http://www.arm.linux.org.uk/developer/build/

Quick HOWTO on usage:
=====================

Here is the simplest, crapiest script you can use (from the kernel
directory) for this:

```sh
#!/bin/sh

head=$(git describe)
basedir="logs"

for file in ../omap-seeds/*.config
do
	config=$(basename "$file")
	DIR="$basedir"/"$head"/"$config"

	export ARCH="arm"
	export CROSS_COMPILE="arm-linux-gnueabihf-"
	export KCONFIG_ALLCONFIG="$file"

	TDIR="$DIR"/allnoconfig

	mkdir -p "$TDIR"

	make allnoconfig 1> "$TDIR"/stdout.txt 2> "$TDIR"/stderr.txt;
	cp .config "$TDIR"/defconfig;
	make -j16 1>> "$TDIR"/stdout.txt 2>> "$TDIR"/stderr.txt

	for i in $(seq 0 9);
	do
		TDIR="$DIR"/randconfig$i

		mkdir -p "$TDIR"

		make randconfig 1> "$TDIR"/stdout.txt 2> "$TDIR"/stderr.txt;
		cp .config "$TDIR"/defconfig;
		make -j16 1>> "$TDIR"/stdout.txt 2>> "$TDIR"/stderr.txt
	done
done
```
