## TBB Termux
The branch contains 
- install target for Makefile
- changes required to correctly detect Clang and build TBB on Termux (Arm)
- Example to integrate TBB with your project using CMake (see example_cmake_extarnal_project/)

#### Build and install to specific location
``
tbb_install_prefix=$HOME/toolchains/tbb make install
``

#### Build on Termux:
TODO: add a -DTERMUX flag to CMake on the command line. Currently the flag is added directly to Makefile.tbb

#### Example to integrate TBB with another project
This is using ExternalProject_Add and take CMake OFFLINE flag to either download the dependency from github or use local version of the repo.
