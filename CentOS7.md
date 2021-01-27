# Remote Provisioning Client (RPC)

RPC is an application which enables remote capabilities for AMT, such as as device activation. To accomplish this, RPC communicates with the RPS (Remote Provisioning Server).

The steps below assume the following directory structure where **rpc** is the clone of this repository, **vcpkg** is a clone of the VCPKG tool source and **build** is the RPC build directory. Both vcpkg and build directories will be created in later steps.

```
\rpc
  |__vcpkg
  |__build
```

# Linux

Steps below are for CentOS7.

**The "export PATH=..." (for CMake and Git), and "scl enable devtoolset-7 bash" (for GCC) must be executed in in the Terminal you are building from; i.e. these are temporary changes which only affect the current Terminal session.**

## Dependencies

### CMake
Download cmake-3.10.2-Linux-x86_64.sh from https://github.com/Kitware/CMake/releases/tag/v3.10.2.
    
```
./cmake-3.10.2-Linux-x86_64.sh
export PATH=/home/user/Downloads/cmake-3.10.2-Linux-x86_64/bin:$PATH
```

### GCC
Update GCC toolchain.

```
sudo yum install centos-release-scl
sudo yum install devtoolset-7
scl enable devtoolset-7 bash
```

### Git
Build Git source control system. The make install command will install Git binaries to the ~/bin directory.
```
sudo yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-CPAN perl-devel
git clone -b v2.28.0 https://github.com/git/git.git
make
make install
export PATH=/home/user/bin:$PATH
```

## Build C++ REST SDK

Using a Terminal window with the PATH and devtoolset enabled per the Dependencies.

```
git clone -b 2020.11-1 https://github.com/microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install cpprestsdk[websockets]
```

## Build RPC

Using a Terminal window with the PATH and devtoolset enabled per the Dependencies.

```
mkdir build
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=/rpc/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release -DNO_SELECT=ON ..
cmake --build .
```

To build debug:
```
cmake -DCMAKE_TOOLCHAIN_FILE=/rpc/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Debug -DNO_SELECT=ON ..
cmake --build .
```

## Run RPC

Open a Terminal window.

```
cd build
sudo ./rpc --url wss://localhost:8080 --cmd "-t activate --profile profile1"
```

Use --help for more options.
