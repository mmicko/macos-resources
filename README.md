# macOS-resources
macOS resources needed for cross compile

## Qt5

Since cross compile environment is using Ubuntu 20.04 which contain Qt5 tools for 5.12.8 we compile exactly same version for macOS
```
tar xvfz qt-everywhere-src-5.12.8.tar.xz
cd qt-everywhere-src-5.12.8

./configure \
    -verbose \
    -prefix ~/Qt5.12.8 \
    -release \
    -opensource -confirm-license \
    -system-zlib \
    -qt-libpng \
    -qt-libjpeg \
    -qt-freetype \
    -qt-pcre \
    -nomake examples \
    -nomake tests \
    -dbus-runtime
make
make install
tar cvfz Qt5.12.8.tar.gz Qt5.12.8/
```

For ARM64
TODO: patch fails on one file, but easy to fix manually, update later
```
cd qtbase
patch -p1 < ../../qt-split-arch.patch
cp ../../qcocoahelpers.h  src/plugins/platforms/cocoa/qcocoahelpers.h 
cp ../../qcocoahelpers.mm src/plugins/platforms/cocoa/qcocoahelpers.mm


./configure \
    -verbose \
    -prefix ~/Qt5.12.8 \
    -release \
    -opensource -confirm-license \
    -system-zlib \
    -qt-libpng \
    -qt-libjpeg \
    -qt-freetype \
    -qt-pcre \
    -nomake examples \
    -nomake tests \
    -dbus-runtime \
    -skip qtwebengine \
    -no-assimp
```



## macports
There are some packages that needs to be compiled since there is no binary distribution available on macports, easy way is to generate all
on mac machine and transfer file for cross compile usage, same as with Qt5

Next packages should be installed checked with `port installed requested`
```
  boost @1.71.0_4+no_single (active)
  coreutils @8.32_0 (active)
  eigen3 @3.3.9_0 (active)
  gmp @6.2.1_0 (active)
  gtk2 @2.24.32_0+quartz (active)
  gtk3 @3.24.23_0+quartz (active)
  hidapi @0.9.0_0 (active)
  libftdi1 @1.5_0 (active)
  libusb @1.0.24_0 (active)
  realpath @1.0.1_1 (active)
  tk @8.6.11_0+quartz (active)
```

coreutils package is also need for compilaton if tools are not available already on mac.

in `/opt/local/etc/macports/variants.conf` add `-x11 +no_x11 +quartz` to be default configuration

Provided files are created with next order of commands:
```
sudo port install coreutils
sudo port install boost -no_static +static -python39
sudo port install gtk2
sudo port install gtk3
sudo port install realpath
sudo port install tk
sudo port install libusb libftdi1 hidapi
sudo port install gmp
sudo port install eigen3

sudo port uninstall leaves  (couple of times)
sudo port install curl
```

Package with:
```
cd /opt/local/var/macports/software
tar cvf ~/macports-darwin-x64.tar *
```
