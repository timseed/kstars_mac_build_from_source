# Compile KStars on Mac Silicon from source

I have a Mac M2 Silicon, and I would like to compile kStars from source.
The version of KStars is   3.8.1

## Prerequisits

I am going to assume you are a programmer or at least technical on a pc.... if not, err good luck.

Next, my environment for development is set as the following

- QT Installed as a **NATIVE** package i.e. ~/Dev/QT_Compiler
  - NOT I repeat NOT using brew for provide QT
  - I am using QT 6.10.1 (QT5 is too old imho)
- brew is installed, and I use for general packages, eigen etc. But **NOT** for QT
- openCV was build from source not using brew, as this pulled in QT6 :$
-

## Get kstars Source

A simple git pull will get you the Kstars repo

    cd ~/Dev/C++Astro   #Or where you want to build from 
    git clone https://github.com/KDE/kstars 
    cd kstars   

As the CMake commands can get a little complex - I usually use a small bash script to make this more repeatable

So in the kstars source I do  

    mkdir build
    cd build 

Here, typically you do a

    cmake ../ 

But - please use a script like this (mine is called *t.sh*), but use what ever you can type quickly and easily.
This script is executable i.e. **chmod +x t.sh**

```bash
#/bin/bash
PATH=/Users/tim/Dev/QtCompiler/6.10.1/macos/bin:$PATH

export KF6_PREFIX=$HOME/kf6
QT_ROOT=$(qmake6 -query QT_INSTALL_PREFIX)
echo "QT is based at $QT_ROOT"
rm CMakeCache.txt   #Remove when you are nearly finished
cmake \
  -DCMAKE_PREFIX_PATH="$QT_ROOT;$KF6_PREFIX" \
  -DCMAKE_BUILD_RPATH=$QT_ROOT/lib \
  -DCMAKE_INSTALL_RPATH=$QT_ROOT/lib \
  -DCMAKE_INSTALL_RPATH_USE_LINK_PATH=TRUE \
  -DCMAKE_POSITION_INDEPENDENT_CODE=ON \
  -DAPPLE_SUPPRESS_X11_WARNING=ON \
  -DQt6_DIR=$QT_ROOT/lib/cmake/Qt6 \
  -DUSE_QT6=ON \
  -DBUILD_WITH_QT6=ON \
  -DQT_DEBUG_FIND_PACKAGE=ON \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/usr/local \
  -DBUILD_TESTING=OFF \
  ..
```

This almost 100% will fail, when you 1st try and use it. CMake will say something like

```bash
Missing Package eigen  
```

This is normally expressed that it can not find eigen.cmake-rules

So try to look inside brew

    brew search eigen 

If the package is found ...

     brew install eigen 

I am sorry that I do not have a list of packages I have installed, but as I have already been coding in FITS/Indi etc.... it is probably best you assemble this for yourself.

**Until** you get the **KF6 Framework** not found.

# KF6 Framework

You may have noticed at the top of my build script there was an env setting for KF6,

    export KF6_PREFIX=$HOME/kf6

I tried to get kf6 to install via brew, but I could not get it to work (maybe you can); So I took a *very* deep breath and decided to install KF6 manually !!

## KF6 FrameWork build

I created a fresh folder

    mkdir ~/Dev/KDE 
    cd ~/Dev/KDE 

And now I created another *cmake helper* script (also called *t.sh*)

```t.sh
#!/bin/bash
PATH=/Users/tim/Dev/QtCompiler/6.10.1/macos/bin:$PATH
QT_ROOT=$(qmake6 -query QT_INSTALL_PREFIX)
cmake .. \
  -DCMAKE_PREFIX_PATH=/opt/homebrew/opt/qt \
  -DCMAKE_INSTALL_PREFIX=$HOME/kf6 \
  -DBUILD_TESTING=OFF \
  -DAPPLE_SUPPRESS_X11_WARNING=ON

cmake --build . -j$(sysctl -n hw.ncpu)
cmake --install .
```

The next thing I have eventually worked out if the build order, KStars complained that it needed Kio NewStuff Plotting (and others) So I tried to just build those items, but of course they have pre-requisites. This is the order that I came up with.

So I believe you need to build in this Order.

- kcoreaddons
- kconfig
- ki18n
- kwidgetsaddons
- kcrash
- karchive
- kguiaddons
- kitemviews
- kcodecs
- kcolorscheme
- breeze-icons (CMakeError: Python3 not Python\ 3. Use Virtual Python Env and install lxml)
- kiconthemes
- kconfigwidgets
- kxmlgui
- kcompletion
- solid (brew install bison; Alter path to find new version. Then start build)
- kwindowsystem
- kdocbook (brew install docbook docbook-xsl libxslt fop; Lots of messing around due to languages. I cheated only build english - sorry)
- kbookmarks
- knotifications
- kjobwidgets
- khelp
- kdbusaddons
- kded (Language file issue: All Removed except en_GB)
- kio (Ok Accesstime, ModTime all need #define (**APPLE**) as the Unix time structure is different.)
- knotifyconfig
- kservice
- kPlotting
- kPackage (Removed all Language except En_Gb ... sorry :( ))
- attika (no K at the start)
- kNewStuff

### Building 1 Module

Lets, start at the top  - with kcoreaddons

I go to my KDE build folder (Which I created earlier)

     cd ~/Dev/Dev/KDE 
     git clone https://invent.kde.org/frameworks/kcoreaddons.git 

It is the same *<https://invent.kde.org/frameworks/>* then with the **package** and then *.git* appended.

We enter the folder, and get setup

    cd kcoreaddons
    mkdir build 
    cd build 
    cp ../../t.sh .

So we create a build folder, go into it, then copy the t.sh template - next we execute it

    ./t.sh 

This will perform the cmake, then build (make), and finally install (make install)

For 90% of KF6 Framework this is all you have to do.

## The Problem modules

### breeze-icons

This needed a *fix* (hack?) in the CMakeFiles.txt; I needed it to use a Virtual Python Environment, into which I had installed lxml

- find_package(Python 3 COMPONENTS Interpreter REQUIRED)

- find_package(Python3 COMPONENTS Interpreter REQUIRED)

  This is the change I made.

### solid

The installed version of *bison* is too old by default on a mac, and I needed to force a brew update.

### kdocbook

This was the most difficult to install. Due to the non english languages being built. My solution (I do apoliogize), was to remove all translation files in the source folder.

This also required some more brew packages

    brew install docbook docbook-xsl libxslt fop 

And finally I needed it's utility script updating to

```
PATH=/Users/tim/Dev/QtCompiler/6.10.1/macos/bin:$PATH
export XML2_DIR=$(brew --prefix libxml2)
export XSLT_DIR=$(brew --prefix libxslt)

export LDFLAGS="-L$XML2_DIR/lib -L$XSLT_DIR/lib"
export CPPFLAGS="-I$XML2_DIR/include -I$XSLT_DIR/include"
export PKG_CONFIG_PATH="$XML2_DIR/lib/pkgconfig:$XSLT_DIR/lib/pkgconfig"

QT_ROOT=$(qmake6 -query QT_INSTALL_PREFIX)
cmake .. \
  -DCMAKE_INSTALL_PREFIX=$HOME/kf6 \
  -DCMAKE_PREFIX_PATH="$QT_ROOT;$(brew --prefix extra-cmake-modules)" \
  -DQt6_DIR=$QT_ROOT/lib/cmake/Qt6 \
  -DBUILD_TESTING=OFF \
  -DCMAKE_CXX_FLAGS="-Uisnan -Uisinf -Uisfinite" \
  -DCMAKE_CXX_STANDARD=20 \
  -DCMAKE_CXX_STANDARD_REQUIRED=ON \
  -DCMAKE_CXX_EXTENSIONS=OFF \
  -DAPPLE_SUPPRESS_X11_WARNING=ON

cmake --build . -j$(sysctl -n hw.ncpu)
cmake --install .
```

### kded

Another *language* issue. Solved by removing all language files in the po folder except *en_GB*

### kio

It is sad to see the KF6 framework, not even thinking of DARWIN, it has OS based compiles for WINDOWS and OTHER !!

I had to modify the following files

 modified:   src/core/udsentry.cpp
 modified:   src/kioworkers/file/stat_unix.h
 modified:   src/kioworkers/trash/kio_trash.cpp

All 3 files have the same basic issue ...

The file access structure has a different naming system on Mac, than on linux...

So the changes will be something like this

```C++
+#if defined(__APPLE__)
+  d->insert(UDS_MODIFICATION_TIME_NS_OFFSET,
+            buff.st_mtimespec.tv_nsec / 1000000);
+  d->insert(UDS_ACCESS_TIME_NS_OFFSET, buff.st_atimespec.tv_nsec / 1000000);
+  d->insert(UDS_LOCAL_USER_ID, buff.st_uid);
+  d->insert(UDS_LOCAL_GROUP_ID, buff.st_gid);
+#else
+  d->insert(UDS_MODIFICATION_TIME_NS_OFFSET, buff.st_mtim.tv_nsec / 1000000);
+  d->insert(UDS_ACCESS_TIME_NS_OFFSET, buff.st_atim.tv_nsec / 1000000);
+  d->insert(UDS_LOCAL_USER_ID, buff.st_uid);
+  d->insert(UDS_LOCAL_GROUP_ID, buff.st_gid);
 #endif
```

### kPackage

 Another Language issue - solved the same way.... I only left *en_GB*

 Ok At this point we return to the Original KStars scipt

# KStars Build

With KF6 Framework build we return to the kstars/build directory, and

    rm CMakeCache.txt 
    ./t.sh 
    make -j4
    
    
And we (hopefully) see

```text
tim@Tim-M1 build % make
[  0%] Generating mo...
[  0%] Built target pofiles-b16afee0cf0e73d893e7c15a166a2b46
[  1%] Generating ts...
[  1%] Built target tsfiles-b16afee0cf0e73d893e7c15a166a2b46
[  1%] Built target LibKSDataHandlers_autogen_timestamp_deps
[  1%] Built target LibKSDataHandlers_autogen
[  1%] Built target LibKSDataHandlers
[  1%] Built target htmesh_autogen_timestamp_deps
[  2%] Built target htmesh_autogen
[  4%] Built target htmesh
[  5%] Built target KStarsLib_autogen_timestamp_deps
[  5%] Built target KStarsLib_autogen
[ 99%] Built target KStarsLib
[ 99%] Built target kstars_autogen_timestamp_deps
[ 99%] Built target kstars_autogen
[100%] Built target kstars
```

Well-done KStars built Natively on a Mac.  

## To Run

    ./kstars/KStars.app/Contents/MacOS/kstars
