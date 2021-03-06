CMake SNAP
===================
Snap makes building software with C/C++/Python/Java targets easier. With Snap you can...
* Write short and readable CMakeLists.txt files
* Use a package in a new target just by listing the package name (a URI formatted string). 
* All dependent packages are imported and built when required.

Example Usage
-----------
````cmake
# Example of a header only C++ library package
CPP_LIBRARY(
  NAME      headerlib
  HEADERS   handy_header_lib.hpp
  LIB_TYPE  HEADER
)           

# Example of a C++ binary that uses both a standard and header only library  
CPP_BINARY(
  NAME      hello_snap
  SOURCES   hello_snap.cpp   
  PACKAGES  //examples/cpp/base64:base64  # a static lib  
            //examples/cpp:headerlib  # a header only lib                          
)        
```

````cmake
# Example of a static C++ library package
CPP_LIBRARY(
  NAME      base64 
  SOURCES   cdecode.cpp
            cencode.cpp 
  HEADERS   cdecode.h
            cencode.h
            decode.h
            encode.h
  LIB_TYPE  STATIC
)

# Example of a unit test for the static library
CPP_BINARY(
  NAME      base64_test
  SOURCES   base64_test.cpp   
  PACKAGES  //examples/cpp/base64:base64                        
  TEST_SIZE small
)  
````

````cmake
# Builds a protcol buffer library for C++ and python
PROTO_LIBRARY(
  NAME  myproto
  PROTO myproto.proto  
)
````


````cmake
# Build a C++ library and generate bindings to use library directly in python
CPP_LIBRARY(
  NAME     breaking_news
  SOURCES  breaking_news.cpp
  HEADERS  breaking_news.h
  PACKAGES //examples:myproto  # a proto library        
  LIB_TYPE STATIC_AND_SHARED
  SWIG_PY  py_breaking_news.i
)

# Example python code that uses the C++ library above passing protocol buffers as input and outputs to methods
PY_BINARY(
  NAME      test_breaking_news
  SOURCES   test_breaking_news.py
  PACKAGES  //examples/py:py_breaking_news  # a c++ library with python bindings  
  TEST_SIZE small            
)
````


Installation
---------------

1. Get the code.
    * git clone git://github.com/cmakesnap/snap.git 
    * https://github.com/cmakesnap/snap/archive/master.zip
2. Run the ./install.py script in the project root to install dependencies (cmake, protobuf, swig)
3. Add the following snippet to your ~/.bashrc 

   ````bash
   # Tell CMake about the snap extensions
   export cmakesnap_DIR=<snap_dir>
   
   # Adds handy command to toggle between corresponding locations in source and
   # and build directory tree with the command 'snap-toggle'
   alias snap-toggle='cd `${cmakesnap_DIR}/internal/util/snap-toggle.py`'
   ````

4. Run the ./run_tests.sh to build the example projects (start here for examples)


Getting Started
-------
* See the examples directory

Currently supported targets: 
* CPP_BINARY - *Option: Mark as a "test" to be run in the project test suite* 
* CPP_LIBRARY - *Option to generate SWIG python bindings*
* PROTO_LIBRARY - *Generates C++, Python, and Java libraries for a .proto file (and helps easily pass protobufs between c++ and python code)
* PYTHON_BINARY - 
* PYTHON_LIBRARY - 
* LOCAL_RESOURCE - *A set of files required by a target at runtime (html templates, config files, etc.).*
* REMOTE_RESOURCE - *A url that should be downloaded and untared to provide files required by a target at runtime . Useful for large binary data files that are needed for unit tests but don't place nice with git.*


Design
---------------
Snap aims to make building software from reusable components as easy as snapping
together Lego building blocks.  Traditional build tools require verbose 
specifications of system installed and custom library resources and their 
dependencies which requires significant overhead for each component reused and
make it more difficult for others to build the same software in a different 
computer environment.  Snap allows users to write very concise specifications 
of the desired build targets and reuse components via a simple unique package 
name scheme.  Snap makes it easier to build all of a target’s dependencies from 
source distributed with the project which eliminates many problems caused by 
variations in system installed libraries among various operating systems.  
This scheme also enables “as-static-as-possible-linking” which is required when 
compiling c/c++ binaries on one system which must be distributed and reliably 
run in another environment.  This is a basic requirement for distributed 
computing which can be difficult to achieve with conventional build tools.  Snap
is built as a set of macros for the CMake build system which is widely used in 
open source community for its speed, portability, and ease of use.

Contact
------
*Author: Kyle Heath (cmakesnap [at] gmail)*

[![Build Status](https://travis-ci.org/cmakesnap/snap.png)](https://travis-ci.org/cmakesnap/snap)
