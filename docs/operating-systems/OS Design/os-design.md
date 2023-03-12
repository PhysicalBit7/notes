---
layout: default
title: Operating System Design
description: Functions learned during OS Design
has_children: true
has_toc: false
parent: Operating Systems
permalink: /operating-systems/os-design
---
# Operating Systems Design
Fall 2022

## Resources
- Linux Kernel Development 3rd edition

---
A repository describing what I learned during OS Design. I made two system calls in the Linux kernel called `elevate` and `stealth`. I then made a proc entry called `underStealth` which displayed a process that was "stealthed", as well as the time it had been under stealth

---

## Swapping to a new Linux kernel
Firstly, here are the steps to follow in order to compile and swap to a new Linux kernel

### Required Packages
```bash
sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
```

---

### Acquiring the new kernel

```bash
git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git linux
git pull #To update after being checked out
```

This will download the current kernel into a folder called `linux`

---

### Configuring the new kernel
You have three options to choose from the configure the kernel

```bash
make config #Basic utility, going through every option one by one
make menuconfig #ncurses-based graphical utility
make gconfig #gtk+ based graphical utility
make defconfig #Default config based on your architecture
```

#### Error messages
You may receive the following error message `No rule to make target 'debian/canonical-certs.pem`. You can fix using 

```bash
scripts/config --disable SYSTEM_TRUSTED_KEYS
scripts/config --disable SYSTEM_REVOCATION_KEYS
```
---

### Building the new kernel
You can build the kernel with a single command

```bash
make
make > ../detritus #Minimize build noise but still see error and warnings
make > /dev/null #Redirects all output

#You can also parallelize the build job with
make -jn #where n is the number of jobs, usually two per processor
```

Now we can install modules to the new kernel. This installs all the compiled modules to their correct home under /lib/modules

```bash
make modules_install
```
---

### Installing the new kernel
Now we can simply install the new kernel using

```bash
sudo make install
```

This step also updates the `Grub` bootloader. You can do this manually if you please with

```bash
sudo update-initramfs -c -k <version-number>
sudo update-grub
```
---

### Booting into the new kernel
Before rebooting, check you kernel version with 

```bash
uname -r
```

Now we restart holding the `shift` key. You can also edit `/etc/default/grub` to boot every time into grub. Place a '#' at the beginning of the line `GRUB_HIDDEN_TIMEOUT=0` and then update Grub using `sudo update-grub`

From the Grub menu, choose advanced options, and choose your new kernel. You can verify by using the `uname` command from above