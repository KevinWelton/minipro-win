# minipro

# This is a clone of https://gitlab.com/DavidGriffith/minipro. It has some small changes to work on Windows with the config files living side-by-side the executable instead of in %ProgramData%.

I have the following environment that allowed me to build without too
much trouble. I'm not claiming this is the best or only way, it isn't.
But it worked for me since I already had the config.
* make (https://github.com/maweil/MakeForWindows/releases). Must be in PATH.
* Strawberry perl (https://strawberryperl.com/). Provides gcc.
* Git bash (https://git-scm.com/). Add the following to `~/.bash_profile` so we use the right perl.exe:
```
# Use Strawberry perl, not the builtin perl.
export PATH=/c/Strawberry/perl/bin:$PATH
```

At this point, you can go to your repo folder in a git bash shell and build via `make`.

Once the build is complete, run `make install` and it will place the artifacts in a folder named `build`. With the changes in this fork, you can keep the xml files right beside the exe.

_Note:_ Some online sources have had success with cygwin. I didn't find it necessary.
With the solution I'm using, you don't need the cygwin dll dependency.

## What I'm using it for
I am using minipro to program my T48 with an AT28C256 as part of BenEater's 6502 project (https://eater.net/6502.) I highly recommend this project. BenEater is a great teacher. If possible, buy the kits directly from him.

## ISSUES
When I program my AT28C256 on the T48, I get an error when verifying... but the chip seems to have been programmed just fine. At least the program I flashed it with runs. For transparency though, I am a n00b with EEPROMs. So it's possible that somethings wrong that just didn't manifest when I ran the code I flashed.

# Hereafter is the readme as it exists in the original repository. I will keep it up to date if I end up merging at a later time.

## Latest "stable" version 0.7

An open source program for controlling the MiniPRO TL866xx series of
chip programmers

This program exists because the manufacturer of the MiniPRO TL866xx
series of chip programmers does not provide a program for use on Linux
or other flavors of Unix.  We who keep this project going prefer a
simple, free, and open-source program that presents a command-line
interface that allows for a GUI front-end if desired.


## Features
* Native support for Linux, BSD, and other flavors of Unix.
* Compatibility with Minipro TL866CS, TL866A, and TL866II+ from
Autoelectric (http://www.autoelectric.cn/en/tl866_main.html)
* Experimental support for Xgecu T48 programmer
* More than 13000 target devices (including AVRs, PICs, various BIOSes
and EEPROMs)
* ZIF40 socket and ISP support
* Vendor-specific MCU configuration bits
* Chip ID verification
* Overcurrent protection
* System testing

## Synopsis

```nohighlight
$ minipro -p ATMEGA48 -w atmega48.bin
$ minipro -p ATMEGA48 -r atmega48.bin
```

## Prerequisites

You'll need some sort of Linux or MacOS machine.  Other Unices may work, 
though that is untested.  You will need version 1.0.16 or greater of libusb.


## Installation on Linux

### Install build dependencies 

#### Debian/Ubuntu
```nohighlight
sudo apt-get install build-essential pkg-config git libusb-1.0-0-dev
```

#### CentOS 7
```nohighlight
sudo yum install gcc make pkgconfig git libusbx-devel
```

#### openSUSE
```nohighlight
sudo zypper install gcc make git-core srecord rpmdevtools libusb-1_0-devel
```

### Checkout source code and compile 
```nohighlight
git clone https://gitlab.com/DavidGriffith/minipro.git
cd minipro
make
sudo make install
```

### Udev configuration (recommended)
If you want to access the programmer as a regular user, you'll have to
configure udev to recognize the programmer and set appropriate access
permissions.

```nohighlight
sudo cp udev/*.rules /etc/udev/rules.d/
sudo udevadm trigger
```
You'll also have to add your regular user to the `plugdev` system
group:
```nohighlight
sudo usermod -a -G plugdev YOUR-USER
```
Note that this change will only become effective after your next
login.

### Bash completion (optional)

Bash users may want to optionally install the provided completion file:
```nohighlight
sudo cp bash_completion.d/minipro /etc/bash_completion.d/
```

### Making a .deb package

Building a Debian package directly from this repository is easy.  Make
sure you have the build dependencies installed as described above.  Be
sure it all builds, then do this:

```nohighlight
sudo apt-get install fakeroot debhelper dpkg-dev
fakeroot dpkg-buildpackage -b -us -uc
```

You should then have a .deb package for you to install with `dpkg -i`. 
Note that the .deb package will already provide the udev and 
bash-completion configurations for you.

### Making a Debian source package for a PPA

The following commands require `git-buildpackage`:

```nohighlight
git archive --format=tar HEAD | tar x && git commit -am "packaging: Makefile substitution"
gbp dch --commit --since=HEAD --upstream-branch=master --dch-opt="-lppa" --dch-opt="-D$(lsb_release -c -s)" debian
gbp buildpackage --git-upstream-tree=SLOPPY --git-export-dir=../build-area -S
```

You can then `dput` the `*.changes` file in `../build-area` to your PPA.

### Making a .rpm package

You can build RPM packages for Fedora, CentOS and openSUSE with the supplied
`rpm/minipro-*.spec` files.

First make sure you have a RPM build environment set up. You need to have
the rpmdevtools package installed and a `rpmbuild` directory tree within
your homedir. Use the `rpmdev-setuptree` command to create the rpmbuild
directory tree if it does not exist yet.

Since minipro does not yet make official releases with version numbers
and tags, it will build the master branch by default.

Then use these commands to download the source tarballs from Gitlab and
build the package for Fedora and CentOS:

```nohighlight
spectool -g -R rpm/minipro-fedora.spec
rpmbuild -ba rpm/minipro-fedora.spec
```

Or for openSUSE:

```nohighlight
rpmdev-spectool -f -g -R rpm/minipro-opensuse.spec
rpmbuild -ba rpm/minipro-opensuse.spec
```

The final RPMs can be found below `~/rpmbuild/RPMS/`

## Installation on macOS

The easiest way to install on macOS is by using brew to install the most recent release:

```nohighlight
brew install minipro
```

At that point, minipro should be installed and ready to use.

If you'd rather compile from source (if, for instance, you need newer code than is in the most recent release), you'll need to do the following to prepare your environment and compile the code.

### Install dependencies
Install `pkg-config` and `libusb` using brew or MacPorts:
```
brew install pkg-config
brew install libusb
brew link libusb
```
or:
```
port install pkgconfig
port install libusb
```
### Checkout source code and compile
```nohighlight
git clone git@gitlab.com:DavidGriffith/minipro.git
cd minipro
make
sudo make install
```
