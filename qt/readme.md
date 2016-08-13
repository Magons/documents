<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

    - [About](#about)
    - [Links](#links)
- [About](#about-1)
- [Build status](#build-status)
- [Configure options sourced in debian/rules via conditional statements](#configure-options-sourced-in-debianrules-via-conditional-statements)
- [Configure options currently disabled](#configure-options-currently-disabled)
- [Disabled installs](#disabled-installs)
- [Build order notes (from README.source)](#build-order-notes-from-readmesource)
  - [Bootstrapping the docs packages](#bootstrapping-the-docs-packages)
- [Symbols](#symbols)
  - [WARNING!](#warning)
  - [Notes](#notes)
  - [Updating Symbols:](#updating-symbols)
  - [Creating Symbols](#creating-symbols)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### About
Some info about Qt

### Links

* [Official](http://qt.io)
* [Building Qt5 from Git](https://wiki.qt.io/Building_Qt_5_from_Git)
* [Configure options](http://doc.qt.io/qt-5/configure-options.html)
* [Creating plugins](http://doc.qt.io/qt-5/plugins-howto.html)
* [QT package / module hierarchy](http://pkg-kde.alioth.debian.org/images/qt5_build_deps.png)
* [QT package deps main page](http://pkg-kde.alioth.debian.org/qt5-build-deps.html)
* [QT deps](http://anonscm.debian.org/cgit/pkg-kde/qt)

# About

Some info about the build work in progress to get Qt 5.6.0 into SteamOS brewmaster. This is mainly for building PlexMediaPlayer.
The debian/ files were sourced from the [Debian Stretch](https://packages.debian.org/sid/qt5-default) set of files. From there, 
these have been tweaked a little to allow building on SteamOS brewmaster (with Jessie/Jessie-Backports enabled).

Please see [configure-test.sh](https://github.com/ProfessorKaos64/LibreGeek-Packaging/blob/brewmaster/qt_5.6.0/configure-test.sh)

The "debian_experimental" folder is for work on trying to build the individual packages of Qt 5.6.0+. The debian folder present installs everything made during the install. Once more is known about the build process, the experimental set can possibly be fixed up.

# Build status

Main package (base): **[done]** - 5.6.1

# Configure options sourced in debian/rules via conditional statements

Checking a build log, there two are added at the end via $(extra_configure_opts) (normally determined via dh/shell checks).

```
-plugin-sql-ibase
-opengl desktop
```

# Configure options currently disabled

Currently disabled options from upstream Debian experimental debian/rules:

```
-plugin-sql-ibase
```
There are issues finding `sqlcli.h` for this piece.

Taken out because the jessie/jessie-backport version is too old

```
-system-harfbuzz
```

# Disabled installs

These installs are currently removed. Perhaps when the issues are resolved in building, they can be added back. This could maybe
occuren when a debian/ file set surfaces under Stretch for 5.6.0~.

```
*ibase.install
```

# Build order notes (from README.source)

Bootstrapping the docs packages
-------------------------------

In Qt 5.6 the qdoc tool was moved to qttools source, so qtbase got a
Build-Depends-Indep on qttools5-dev-tools. Thus you need to do the following
steps if you want to rebuild the whole Qt stack from scratch:

* Build only arch-dependent packages from these sources:
  - qtbase-opensource-src
  - qtxmlpatterns-opensource-src
  - qtdeclarative-opensource-src
  - qtwebkit-opensource-src / qtwebengine
* Build the arch-independent packages (-doc and -doc-html) of the above sources.
* Build the rest of the Qt stack in the usual way.

Note: the docs packages should not be a problem when bootstrapping a new
Debian architecture, because the arch-independent packages are already available
in Debian archive.

# Symbols

## WARNING!

Please advise the below notes are "right" for doing a backport of this package. It is highly urged you read throughly through the "Working with symbols files" link below for complete information.

## Notes

* [Working with symbols files](http://pkg-kde.alioth.debian.org/symbolfiles.html)
* [Pool for qtbase](ftp://ftp.debian.org/debian/pool/main/q/qtbase-opensource-src/)
 * File: "qtbase-opensource-src_5.3.2+dfsg-4.debian.tar.xz"

## Updating Symbols:

1. Import the target (in this case Jessie for the backport) symbols into debian/
2. Attempt the build
3. Take the buildlog and run it thru the utility below (assuming log .build with pbuilder):

One libary
```
pkgkde-symbolshelper patch -p LIB_PKG_NAME -v 5.6.0 < $HOME/build-qtbase-opensource-src/qtbase*.build
```

A batch:
```
pkgkde-symbolshelper batchpatch -v 5.6.0 $HOME/build-qtbase-opensource-src/qtbase*.build
```

## Creating Symbols

You can also create the symbols from each library (e.g. libqt5network5). The below example set processes the two libraries from libqt5network5.

General process:

1. Build the package without symbols, and get the compiled binary (e.g. libqt5network5.deb)
2. cd into the diretory with the build binaries and move all lib*.deb packages into the temporary build dir source tree with debian/
3. Create a temp and symbol_temp directory with `mkdir temp newsymbols`
4. Extract and process each package with `dpkg -x <package> temp/`
5. Update per the directions below (make sure to use the correct path to the libary soname)

You can also use the commong "C10shell" hook to call root inside the pbuilder chroot when the build inevitably fails. You should have the compiled packages ahead of where the symbols were processed. Follow then step 3.

Example:
```
pkgkde-gensymbols -plibqt5concurrent5 -v5.6.0 -Osymbols.amd64 -etemp/usr/lib/x86_64-linux-gnu/libQt5Concurrent.so.*
pkgkde-symbolshelper create -o newsymbols/ibqt5concurrent5.symbols -v 5.6.0 symbols.amd64
rm -f symbols.amd64 && rm -rf temp/*
```

Processing all sonames in an extracted folder can follow a process such as what [Debian documentation](https://www.debian.org/doc/manuals/maint-guide/advanced.en.html) describes. To use the below, move any .symbols install files out of the debian/ directory, build the package, then move them back for the next step.

```
mkdir newsymbols
pkg="libqt5gui5" ver="5.6.0"
dpkg -x ${pkg}_*.deb ${pkg}_${ver}
: > newsymbols/${pkg}.symbols
dpkg-gensymbols -v${ver} -p${pkg} -P${pkg}_${ver} -Onewsymbols/${pkg}.symbols
rm -rf ${pkg}_${ver}/
```

Or...via a [handy script](https://github.com/ProfessorKaos64/LibreGeek-Packaging/blob/brewmaster/tools/create-new-symbols.sh). Please review the script and be careful to backup files/directories beforehand. You have been warned!

Rinse and repeat.
```
