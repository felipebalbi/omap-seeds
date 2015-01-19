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
head=$(git describe)
basedir="logs"

for file in $(ls ../omap-seeds/*.config)
do
	config=$(basename $file)
	DIR=$basedir/$head/$config
	for i in $(seq 1 5);
	do
		export ARCH="arm"
		export CROSS_COMPILE="arm-linux-"
		export KCONFIG_ALLCONFIG="$file"
		TDIR=$DIR/randconfig$i

		mkdir -p $TDIR

		make randconfig;
		cp .config $TDIR/defconfig;
		make 1> $TDIR/stdout.txt 2> $TDIR/stderr.txt
	done
done
```
