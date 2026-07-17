# macOS-resources
macOS resources needed for cross compile

## Qt6

Since cross compile environment is using Ubuntu 26.04 which contain Qt6 tools for 6.10.2 we compile exactly same version for macOS

```
brew install cmake ninja
tar xvfz qt-everywhere-src-6.10.2.tar.xz 
cd qt-everywhere-src-6.10.2
```

For x64 (cross compiled on arm64):

```
softwareupdate --install-rosetta --agree-to-license

export CMAKE_OSX_ARCHITECTURES=x86_64

./configure \
    -release \
    -prefix ~/Qt6.10.2 \
    -nomake examples \
    -nomake tests \
    -opensource -confirm-license \
    -system-zlib \
    -qt-libpng \
    -qt-libjpeg \
    -qt-freetype \
    -qt-pcre \
    -dbus-runtime \
    -skip qt3d \
    -skip qtwebengine \
    -skip qtwebview

cmake --build . --parallel
cmake --install .
tar cvfz Qt6.10.2-x86_64.tar.gz Qt6.10.2/
```

For arm64:
add on top of file `qtbase/src/corelib/thread/qyieldcpu.h`
```
#if defined(Q_PROCESSOR_ARM_64)
#include <arm_acle.h>
#endif
```

```
./configure \
    -release \
    -prefix ~/Qt6.10.2 \
    -nomake examples \
    -nomake tests \
    -opensource -confirm-license \
    -system-zlib \
    -qt-libpng \
    -qt-libjpeg \
    -qt-freetype \
    -qt-pcre \
    -dbus-runtime \
    -skip qt3d \
    -skip qtwebengine \
    -skip qtwebview

cmake --build . --parallel
cmake --install .

tar cvfz Qt6.10.2-arm64.tar.gz Qt6.10.2/
```

## macports
There are some packages that needs to be compiled since there is no binary distribution available on macports, easy way is to generate all
on mac machine and transfer file for cross compile usage, same as with Qt5

Next packages should be installed checked with `port installed requested`
```
  boost @1.76_0 (active)
  coreutils @9.4_0 (active)
  curl @8.5.0_0+ssl (active)
  eigen3 @3.4.0_1 (active)
  gtk-osx-application-gtk3 @3.0.1_2 (active)
  gtk3 @3.24.38_0+quartz (active)
  hidapi @0.12.0_0 (active)
  libftdi1 @1.5_2 (active)
  libusb @1.0.26_0 (active)
  realpath @1.0.2_0 (active)
  tk @8.6.13_2+quartz (active)
```

coreutils package is also need for compilaton if tools are not available already on mac.

in `/opt/local/etc/macports/variants.conf` add `-x11 +no_x11 +quartz` to be default configuration

Provided files are created with next order of commands:
```
sudo port install coreutils
sudo port install boost -no_static +static -python311
sudo port install realpath
sudo port install tk
sudo port install libusb libftdi1 hidapi
sudo port install gmp
sudo port install eigen3
sudo port install curl -http2
sudo port install py312-setuptools
sudo port install gtk3 gtk-osx-application-gtk3
```

Package with (for x64):
```
cd /opt/local/var/macports/software
tar cvf ~/macports-darwin-x86_64.tar *
```

Package with (for arm64):
```
cd /opt/local/var/macports/software
tar cvf ~/macports-darwin-arm64.tar *
```
