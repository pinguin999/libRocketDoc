---
layout: page
title: Building With CMake
---

The easiest way to build libRocket on Mac and Linux is via the [CMake](http://cmake.org) build system. You'll first need to download CMake (or install via the package manager of your choice on Linux)

CMake is not a build system itself, it generates Makefiles, Xcode projects and Visual Studio projects among other formats.

### Configuration

Before generating your build files you need to configure CMake. Open a terminal window and move to the Build folder of libRocket and execute the following command.

```
buildbox:Build$ ccmake .
```

NOTE: You need the . to denote the current directory is where the CMakeLists.txt is located.

This will open a a text mode application that lest you choose which parts of libRocket you want to build and how you want to build it. Before you can alter any options you'll need to press C so that CMake can scan your system configuration. Once its complete you will see a list of options. The most interesting options are most likely

* *BUILD_PYTHON_BINDINGS* - Build the required bindings for python support. You'll need Boost::Python and Python installed.
* *BUILD_SAMPLES* - Should the samples be built
* *BUILD_SHARED_LIBS* - Build as .so/.dylib as apposed to a .a file 

Make your selection and press C again so that CMake can recalculate build settings based on your selection. Once CMake is happy you'll be able to press G to generate the build configuration and exit.

### Building

At this point you should be back at the terminal and your Makefile will have been created. You can now build libRocket by executing make.

```
buildbox:Build$ make -j 3
```

NOTE: The -j parameter specifies how many jobs to execute in parallel, you should normally set this to number of cores in your machine + 1.

Once the build is complete, check out the samples.
