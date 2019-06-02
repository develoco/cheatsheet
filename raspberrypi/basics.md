raspberry, raspberrypi, raspbian, pi

http://wiringpi.com/download-and-install/

# Checking system information and kernel architecture

You can find the processor model in /proc/cpuinfo. For example:

    cat /proc/cpuinfo 
    processor       : 0
    model name      : ARMv7 Processor rev 4 (v7l)

ARMv7 (and below) is 32-bit. ARMv8 introduces the 64-bit instruction set.

If you want to see whether your system supports 64-bit binaries, check the kernel architecture:

    uname -a
    Linux lightningstrikes2x 4.14.84-v7+ #1169 SMP Thu Nov 29 16:20:43 GMT 2018 armv7l GNU/Linux

On a 64-bit processor, you'd see armv8 (or above).

# Cross-compiling for raspbian the Pi 3 B+

https://medium.com/@au42/the-useful-raspberrypi-cross-compile-guide-ea56054de187

Get the toolchain

    git clone https://github.com/raspberrypi/tools

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

# raspberry pi locale troubleshooting

Error message is:

```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
```

# These 2 commands are equivalent and will not fix the issue
# dpkg-reconfigure locale
# raspi-config

sudo locale-gen UTF-8

# Make sure command exists
update-locale --help

# Check current config, it will be missing variables
cat /etc/default/locale

#  Update the file through the command, will give warning but that is ok
sudo update-locale  LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 LANGUAGE=en_US.UTF-8 LC_CTYPE=UTF-8

# Verify changes
cat /etc/default/locale

# Logout and log back in
exit

# log back in and check that warning disappeared
sudo update-locale

