bitcoin, lightning, lightningnetwork, clightning,raspberry, pi, raspberrypi, raspbian, macos

https://github.com/ElementsProject/lightning/blob/master/doc/INSTALL.md
https://github.com/raspberrypi/tools

# Build and install for macOS

Assume you have Xcode and HomeBrew installed on your Mac. Get dependencies:

    $ brew install autoconf automake libtool python3 gmp gnu-sed

If you don't have bitcoind installed locally you'll need to install that as well

Clone and build lightning:

    $ git clone https://github.com/ElementsProject/lightning.git
    $ cd lightning
    $ ./configure
    $ make
    Running lightning:

Note: Edit your ~/Library/Application\ Support/Bitcoin/bitcoin.conf to include rpcuser=<foo> and rpcpassword=<bar> first, you may also need to include testnet=1

    bitcoind &
    ./lightningd/lightningd &
    ./cli/lightning-cli help

# Build and install for Raspbian

Note: using instructions for Ubuntu

You need bitcoind installed plus these dependencies:

    sudo apt-get update
    sudo apt-get install -y \
      autoconf automake build-essential git libtool libgmp-dev \
      libsqlite3-dev python python3 net-tools zlib1g-dev

Build lightning:

    git clone https://github.com/ElementsProject/lightning.git
    cd lightning
    ./configure
    make

Running lightning:

    ./lightningd/lightningd &
    ./cli/lightning-cli help

# Cross-compile for Raspberry 3 B+ on macOS (not working)

Get the toolchain

    git clone https://github.com/raspberrypi/tools

We need `qemu-user` package. On macOS could only find this:

    brew install qemu

Depending on your toolchain location and target arch, source env variables such as:

    export PATH=$PATH:~/src/com/github/raspberrypi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
    # Change next line depending on target device arch
    target_host=gcc-linaro-arm-linux-gnueabihf-raspbian-x64
    export AR=$target_host-ar
    export AS=$target_host-as
    export CC=$target_host-gcc
    export CXX=$target_host-g++
    export LD=$target_host-ld
    export STRIP=$target_host-strip

    # Could not find sysroot for linaro
    export QEMU_LD_PREFIX=/Users/dl/src/tools/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/arm-linux-gnueabihf/sysroot/

Download and build zlib:

    curl https://zlib.net/zlib-1.2.11.tar.gz -s -o zlib-1.2.11.tar.gz
    tar xvf zlib-1.2.11.tar.gz
    cd zlib-1.2.11
    ./configure --prefix=$QEMU_LD_PREFIX
    make
    make install

Got error on `./configure`

    $ ./configure --prefix=$QEMU_LD_PREFIX
    Compiler error reporting is too harsh for ./configure (perhaps remove -Werror).
    ** ./configure aborting.
