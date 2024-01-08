# macOS-resources
macOS resources needed for cross compile

## Qt5

Since cross compile environment is using Ubuntu 22.04 which contain Qt5 tools for 5.15.3 we compile exactly same version for macOS
```
tar xvfz qt-everywhere-opensource-src-5.15.3.tar.xz
cd qt-everywhere-src-5.15.3
```

For using with MacOSX12.3.sdk patch qtbase/src/plugins/platforms/cocoa/qiosurfacegraphicsbuffer.h by adding
```
#include <CoreGraphics/CGColorSpace.h>
```

For x64:

```
./configure \
    -release \
    -prefix ~/Qt5.15.3 \
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
    -skip qtwebengine

make
make install
tar cvfz Qt5.15.3-x86_64.tar.gz Qt5.15.3/
```

For arm64:

```
./configure \
    -release \
    -prefix ~/Qt5.15.3 \
    -nomake examples \
    -nomake tests \
    QMAKE_APPLE_DEVICE_ARCHS=arm64 \
    -opensource -confirm-license \
    -system-zlib \
    -qt-libpng \
    -qt-libjpeg \
    -qt-freetype \
    -qt-pcre \
    -dbus-runtime \
    -skip qt3d \
    -skip qtwebengine

make
make install
tar cvfz Qt5.15.3-arm64.tar.gz Qt5.15.3/
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
  gtk-osx-application-gtk2 @3.0.1_2 (active)
  gtk2 @2.24.33_3+quartz (active)
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
sudo port install gtk2 gtk-osx-application-gtk2
sudo port install realpath
sudo port install tk
sudo port install libusb libftdi1 hidapi
sudo port install gmp
sudo port install eigen3
sudo port install curl -http2
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
