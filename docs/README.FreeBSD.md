![Manitux Logo](resources/banner_slim.png)

# FreeBSD build guide
This guide has been tested with FreeBSD 12.1 x86_64. Please read it in full before you proceed to familiarize yourself with the build procedure.

Several other distributions have **[specific build guides](README.md)** and a general **[Linux build guide](README.Linux.md)** is also available.

## Table of Contents
1. **[Document conventions](#1-document-conventions)**
2. **[Get the source code](#2-get-the-source-code)**
3. **[Install the required packages](#3-install-the-required-packages)**  
  3.1. **[Build missing dependencies](#31-build-missing-dependencies)**
4. **[Build Manitux](#4-build-manitux)**  
  4.1. **[Configure build](#41-configure-build)**  
  4.2. **[Build](#42-build)**
5. **[Build binary add-ons](#5-build-binary-add-ons)**
6. **[Run Manitux](#6-run-manitux)**
7. **[Uninstall Manitux](#7-uninstall-manitux)**
8. **[Test suite](#8-test-suite)**

## 1. Document conventions
This guide assumes you are using `terminal`, also known as `console`, `command-line` or simply `cli`. Commands need to be run at the terminal, one at a time and in the provided order.

This is a comment that provides context:
```
this is a command
this is another command
and yet another one
```

**Example:** Clone Manitux's current master branch:
```
git clone https://github.com/xbmc/xbmc manitux
```

Commands that contain strings enclosed in angle brackets denote something you need to change to suit your needs.
```
git clone -b <branch-name> https://github.com/xbmc/xbmc manitux
```

**Example:** Clone Manitux's current Krypton branch:
```
git clone -b Krypton https://github.com/xbmc/xbmc manitux
```

Several different strategies are used to draw your attention to certain pieces of information. In order of how critical the information is, these items are marked as a note, tip, or warning. For example:
 
> [!NOTE]  
> Linux is user friendly... It's just very particular about who its friends are.

> [!TIP]
> Algorithm is what developers call code they do not want to explain.

> [!WARNING]  
> Developers don't change light bulbs. It's a hardware problem.

**[back to top](#table-of-contents)** | **[back to section top](#1-document-conventions)**

## 2. Get the source code
Make sure `git` is installed:
```
sudo pkg install git
```

Change to your `home` directory:
```
cd $HOME
```

Clone Manitux's current master branch:
```
git clone https://github.com/xbmc/xbmc manitux
```

**[back to top](#table-of-contents)**

## 3. Install the required packages
If you get a `package not found` type of message with the below command, remove the offending package(s) from the install list and reissue the command. Take a note of the missing dependencies and, after a successful step completion, **[build the missing dependencies manually](#31-build-missing-dependencies)**.

> [!NOTE]  
> Manitux requires a compiler with C++17 support, i.e. gcc >= 7 or clang >= 5

Install build dependencies:
```
sudo pkg install autoconf automake avahi-app binutils cmake curl dbus doxygen e2fsprogs-libuuid enca encodings flac flatbuffers font-util fontconfig freetype2 fribidi fstrcmp gawk gettext-tools giflib git glew gmake gmp gnutls googletest gperf gstreamer1-vaapi hal jpeg-turbo libaacs libass libbdplus libbluray libcapn libcdio libcec libedit libfmt libgcrypt libgpg-error libidn libinotify libmicrohttpd libnfs libogg libplist librtmp libtool libudev-devd libva libvdpau libvorbis libxslt lirc lzo2 m4 mesa-libs mysql57-client nasm openjdk8 p8-platform pkgconf python3 rapidjson shairplay sndio sqlite3 swig30 taglib tiff tinyxml tinyxml2 xf86-input-keyboard xf86-input-mouse xorg-server xrandr zip
```

> [!WARNING]  
> Make sure you copy paste the entire line or you might receive an error or miss a few dependencies.

> [!NOTE]  
> For developers and anyone else who builds frequently it is recommended to install `ccache` to expedite subsequent builds of Manitux.

You can install it with:
```
sudo pkg install ccache
```

> [!TIP]
> If you have multiple computers at home, `distcc` will distribute build workloads of C and C++ code across several machines on a network. Team Manitux may not be willing to give support if problems arise using such a build configuration.

You can install it with:
```
sudo pkg install distcc
```

### 3.1. Build missing dependencies
See the general **[Linux build guide](README.Linux.md)** for reference.

**[back to top](#table-of-contents)** | **[back to section top](#3-install-the-required-packages)**

## 4. Build Manitux
### 4.1. Configure build
If you get a `Could NOT find...` error message during CMake configuration step, take a note of the missing dependencies and either install them from repositories (if available) or **[build the missing dependencies manually](#31-build-missing-dependencies)**.

Create an out-of-source build directory:
```
mkdir $HOME/manitux-build
```

Change to build directory:
```
cd $HOME/manitux-build
```

Configure build:
```
cmake ../manitux -DCMAKE_INSTALL_PREFIX=/usr/local
```

### 4.2. Build
```
cmake --build . -- VERBOSE=1 -j$(sysctl hw.ncpu | awk '{print $2}')
```
> [!TIP]
> By adding `-j<number>` to the make command, you can choose how many concurrent jobs will be used and expedite the build process. It is recommended to use `-j$(sysctl hw.ncpu | awk '{print $2}')` to compile on all available processor cores.

After the build process completes successfully you can test your shiny new Manitux build while in the build directory:
```
./manitux-x11
```

If everything was OK during your test you can now install the binaries to their place, in this example */usr/local*.
```
sudo gmake install
```

> [!NOTE]  
> `gmake` stands for *GNU Make*. BSD's own make does not work here.

This will install Manitux in the prefix provided in **[section 4.1](#41-configure-build)**.

> [!TIP]
> To override Manitux's install location, use `DESTDIR=<path>`. For example:

```
sudo gmake install DESTDIR=$HOME/manitux
```

**[back to top](#table-of-contents)** | **[back to section top](#4-build-manitux)**

## 5. Build binary add-ons
You can find a complete list of available binary add-ons **[here](https://github.com/xbmc/repo-binary-addons)**.

Change to Manitux's source code directory:
```
cd $HOME/manitux
```

Build all add-ons:
```
sudo gmake -j$(sysctl hw.ncpu | awk '{print $2}') -C tools/depends/target/binary-addons PREFIX=/usr/local
```

Build specific add-ons:
```
sudo gmake -j$(sysctl hw.ncpu | awk '{print $2}') -C tools/depends/target/binary-addons PREFIX=/usr/local ADDONS="audioencoder.flac pvr.vdr.vnsi audiodecoder.snesapu"
```

Build a specific group of add-ons:
```
sudo gmake -j$(sysctl hw.ncpu | awk '{print $2}') -C tools/depends/target/binary-addons PREFIX=/usr/local ADDONS="pvr.*"
```

Clean-up binary add-ons:
```
sudo gmake -C tools/depends/target/binary-addons clean
```

For additional information on regular expression usage for ADDONS_TO_BUILD, view ADDONS_TO_BUILD section located at [Manitux add-ons CMake based buildsystem](../cmake/addons/README.md)

> [!NOTE]  
> `PREFIX=/usr/local` should match Manitux's `-DCMAKE_INSTALL_PREFIX=` prefix used in **[section 4.1](#41-configure-build)**.

**[back to top](#table-of-contents)**

## 6. Run Manitux
If you chose to install Manitux using `/usr` or `/usr/local` as the `-DCMAKE_INSTALL_PREFIX=`, you can just issue *manitux* in a terminal session.

If you changed `-DCMAKE_INSTALL_PREFIX=` to install Manitux into some non-standard location, you will have to run Manitux directly:
```
<CMAKE_INSTALL_PREFIX>/bin/manitux
```

To run Manitux in *portable* mode (useful for testing):
```
<CMAKE_INSTALL_PREFIX>/bin/manitux -p
```

**[back to top](#table-of-contents)**

## 7. Uninstall Manitux
```
sudo gmake uninstall
```
> [!WARNING]  
> If you reran CMakes' configure step with a different `-DCMAKE_INSTALL_PREFIX=`, you will need to rerun configure with the correct path for this step to work correctly.

If you would like to also remove any settings and third-party addons (skins, scripts, etc.) and Manitux configuration files, you should also run:
```
rm -rf ~/.manitux
```

**[back to top](#table-of-contents)**

## 8. Test suite
Manitux has a test suite which uses the Google C++ Testing Framework. This framework is provided directly in Manitux's source tree.

Build and run Manitux's test suite:
```
gmake check
```

Build Manitux's test suite without running it:
```
gmake manitux-test
```

Run Manitux's test suite manually:
```
./manitux-test
```

Show Manitux's test suite *help* notes:
```
./manitux-test --gtest_help
```

Useful options:
```
--gtest_list_tests
  List the names of all tests instead of running them.
  The name of TEST(Foo, Bar) is "Foo.Bar".

--gtest_filter=POSITIVE_PATTERNS[-NEGATIVE_PATTERNS]
  Run only the tests whose name matches one of the positive patterns but
  none of the negative patterns. '?' matches any single character; '*'
  matches any substring; ':' separates two patterns.
```

**[back to top](#table-of-contents)**

