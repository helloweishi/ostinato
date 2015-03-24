#summary Ostinato binary and source packages (includes installation instructions)



---

<font color='#336600' size='3'><b>The Ostinato project is looking for help with packaging. If you can maintain binary packages (any platform/distro) - please send an email to ostinato at googlegroups dot com</b></font>

---

# Binary Packages #
## Windows ##
A portable binary package (32-bit) is available at [Bintray](https://bintray.com/pstavirs/ostinato) - just unzip and run! (_NOTE_: you need to have [WinPcap](http://www.winpcap.org/) installed)

[Older releases](http://code.google.com/p/ostinato/downloads/list?can=1) are still available.

## Mac OS X ##
Universal binaries are available as a .dmg at [Bintray](https://bintray.com/pstavirs/ostinato) for OS X 10.5 or later. Please read the HOWTO-INSTALL.txt inside the .dmg for important instructions.

[Older releases](http://code.google.com/p/ostinato/downloads/list?can=1) are still available.

Thanks to [@atulchitnis](http://twitter.com/atulchitnis) for his help in finding and [@gkjohn](http://twitter.com/gkjohn) for lending his Mac Mini to revive Ostinato development/support on OSX.

## BSD ##
Ostinato is available in the FreeBSD ports collection. To install the port -

```
cd /usr/ports/net/ostinato/
make install clean
```

For other BSD distros, please file a request with your distribution for an Ostinato package.

Till the time a port/package is available, use the [source package](#Source_Package.md) and follow build instructions in [Building from Source](BuildingFromSource.md)

## Linux ##

### Debian ###
Ostinato has a package available in Debian 8 jessie (testing) and of course sid (unstable)

For older Debian releases, use the [OBS Packages](#Other_Linux_Distros.md)

### Ubuntu ###
Ostinato is available in the universe repository for Ubuntu 13.10 and later -
```
sudo apt-get install ostinato
```

For older Ubuntu release, use the [OBS Packages](#Other_Linux_Distros.md)

### Gentoo ###
Ostinato has an official package on Portage -
```
emerge ostinato
```

### Arch ###
A PKGBUILD is available in [AUR](http://aur.archlinux.org/packages.php?ID=42119) maintained by Morfeo. See [Installation Instructions](https://wiki.archlinux.org/index.php/AUR_User_Guidelines#Installing_packages).

To see Ostinato in the official Arch _community_ repository, add your [vote](http://aur.archlinux.org/packages.php?ID=42119)!

### Other Linux Distros ###
**For Fedora / RedHat / CentOS / ScientificLinux / openSUSE / Mandriva / Ubuntu (older releases) / Debian (older releases):**
Binary packages for these distributions are built using [Open Build Service (OBS)](http://openbuildservice.org/) - an excellent software/service that provides packaging support for several Linux distributions. Ostinato packages are built using [openSUSE's OBS public instance](https://build.opensuse.org/) infrastructure

Go to [Ostinato Packages](http://software.opensuse.org/download.html?project=home:pstavirs:ostinato&package=ostinato), select your distro and follow the provided instructions.

**NOTE**: RedHat 6 users, please select CentOS 6 or Scientific Linux (SL) 6 - both these distros are binary compatible with RedHat 6

If you don't see a package for your Linux distribution, check if your distribution is supported by the [OpenSUSE's OBS instance](https://build.opensuse.org/project/list_public). If it is supported, send an email to the Ostinato mailing list (ostinato at googlegroups dot com) to request a Ostinato package. Otherwise, please file a request with your distribution for an Ostinato package. Till the time one is available, use the [source package](#SourcePackage.md) and follow build instructions in [Building from Source](BuildingFromSource.md)

# Source Package #
The source package is available at [Bintray](https://bintray.com/pstavirs/ostinato). Build instructions (for all platforms) are [here](BuildingFromSource.md).

[Older releases](http://code.google.com/p/ostinato/downloads/list?can=1) are still available.

# Python Binding Package #
The python binding package is available at [PyPi](https://pypi.python.org/pypi/python-ostinato). See PythonScripting for installation and usage instructions

# vDrone Appliance #
This VM appliance contains just the drone server which is auto-started at boot. The appliance is distributed in ovf/ova format which is supported by all the popular hypervisors including KVM, VMware and VirtualBox. Run the Ostinato client on any PC/Mac desktop and connect to the appliance.

Download the images from [SUSE Gallery](https://susestudio.com/a/t53CgG/vdrone) (you may need to create an account or use an existing OpenID).

# Live CD/USB #
Converts any PC to a dedicated traffic generator by using the Live CD/USB. Because there is only a minimal OS and there are no competing applications for CPU and memory, running Ostinato as a Live CD/USB will achieve higher traffic rates than running the Ostinato application in a traditional OS.

Note - this distribution includes both the Ostinato client and Drone server and runs a Linux Kernel with minimal X and a bare-bones window manager.

Download the images from [SUSE Gallery](http://susegallery.com/a/t53CgG/ostinato-live) (you may need to create an account or use an existing OpenID).