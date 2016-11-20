---
layout: post
title: Apache Mesos 在不同平台下的安装
date: 2016-11-20 01:41
author: admin
comments: true
categories: [Mesos]
tags: [Mesos]
---
 
在传统上，物理机和虚拟机是数据中心的典型的计算单元。当应用部署后，这些机器需要安装各种配置工具来对这些应用做管理。机器通常被组织成集群，提供独立的服务，而系统管理员则来监督其日常的日常运作。最终，这些集群达到其最大容量时，需要多机联网来处理负载，这就对集群的扩展带来了挑战。

在2010年，UC Berkeley 大学就对上述问题，提出了解决方案，这就是现在的 Apache Mesos。Mesos 抽象了 CPU、内存、硬盘资源，让数据中心的功能对外就像是一个大的机器。Mesos 创建一个单独的底层集群来提供他们给应用程序所需要的资源，而不会超出虚拟机和操作系统的性能限制。


本节介绍了 Mesos 在不同平台上的安装和使用过程。

<!-- more -->
 
## Apache Mesos 安装、使用

### 1. 下载安装包

下载地址为：<http://mesos.apache.org/downloads/>。本例为 mesos-1.0.1.tar.gz。

### 2. 系统要求

Mesos 运行在 Linux（64 Bit）和 Mac OS X（64 Bit）。如果要从源码来编译 Mesos，则需要 GCC 4.8.1+ 或 Clang 3.5+。

对于 Linux 下完全支持进程隔离，使用的内核必须要大于等于 3.10 版本。

Mesos agent 也能运行在 Windows 平台。若要从源码编译  Mesos agent 需要安装 Visual Studio 2015。

确保您的主机名通过 DNS 或通过 `/etc/hosts` 可解析为允许完全支持 Docker 的主机的网络功能，需要进行一些 Mesos 测试。在不确定的情况下，请验证 `/etc/hosts` 是否包含您的主机名。

#### Ubuntu 14.04

以下是 Ubuntu 14.04 的安装说明。如果您使用的是不同的操作系统，请安装相应的软件包。

```
# Update the packages.
$ sudo apt-get update

# Install a few utility tools.
$ sudo apt-get install -y tar wget git

# Install the latest OpenJDK.
$ sudo apt-get install -y openjdk-7-jdk

# Install autotools (Only necessary if building from git repository).
$ sudo apt-get install -y autoconf libtool

# Install other Mesos dependencies.
$ sudo apt-get -y install build-essential python-dev libcurl4-nss-dev libsasl2-dev libsasl2-modules maven libapr1-dev libsvn-dev
```


#### Mac OS X Yosemite 和 El Capitan


以下是 Mac OS X Yosemite 和 El Capitan 的安装说明。如果您使用的是不同的操作系统，请安装相应的软件包。

```
# Install Command Line Tools.
$ xcode-select --install

# Install Homebrew.
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# Install Java.
$ brew install Caskroom/cask/java

# Install libraries.
$ brew install wget git autoconf automake libtool subversion maven
```

注意：从 Yosemite 升级到 El Capitan，请确保在升级后，重新运行`xcode-select --install`


#### CentOS 6.6

以下是 CentOS 6.6 的安装说明。如果您使用的是不同的操作系统，请安装相应的软件包。

```
# Install a recent kernel for full support of process isolation.
$ sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
$ sudo rpm -Uvh http://www.elrepo.org/elrepo-release-6-6.el6.elrepo.noarch.rpm
$ sudo yum --enablerepo=elrepo-kernel install -y kernel-lt

# Make the just installed kernel the one booted by default, and reboot.
$ sudo sed -i 's/default=1/default=0/g' /boot/grub/grub.conf
$ sudo reboot

# Install a few utility tools. This also forces an update of `nss`,
# which is necessary for the Java bindings to build properly.
$ sudo yum install -y tar wget git which nss

# 'Mesos > 0.21.0' requires a C++ compiler with full C++11 support,
# (e.g. GCC > 4.8) which is available via 'devtoolset-2'.
# Fetch the Scientific Linux CERN devtoolset repo file.
$ sudo wget -O /etc/yum.repos.d/slc6-devtoolset.repo http://linuxsoft.cern.ch/cern/devtoolset/slc6-devtoolset.repo

# Import the CERN GPG key.
$ sudo rpm --import http://linuxsoft.cern.ch/cern/centos/7/os/x86_64/RPM-GPG-KEY-cern

# Fetch the Apache Maven repo file.
$ sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo

# 'Mesos > 0.21.0' requires 'subversion > 1.8' devel package, which is
# not available in the default repositories.
# Create a WANdisco SVN repo file to install the correct version:
$ sudo bash -c 'cat > /etc/yum.repos.d/wandisco-svn.repo <<EOF
[WANdiscoSVN]
name=WANdisco SVN Repo 1.8
enabled=1
baseurl=http://opensource.wandisco.com/centos/6/svn-1.8/RPMS/$basearch/
gpgcheck=1
gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
EOF'

# Install essential development tools.
$ sudo yum groupinstall -y "Development Tools"

# Install 'devtoolset-2-toolchain' which includes GCC 4.8.2 and related packages.
$ sudo yum install -y devtoolset-2-toolchain

# Install other Mesos dependencies.
$ sudo yum install -y apache-maven python-devel java-1.7.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel cyrus-sasl-md5 apr-devel subversion-devel apr-util-devel

# Enter a shell with 'devtoolset-2' enabled.
$ scl enable devtoolset-2 bash
$ g++ --version  # Make sure you've got GCC > 4.8!

# Process isolation is using cgroups that are managed by 'cgconfig'.
# The 'cgconfig' service is not started by default on CentOS 6.6.
# Also the default configuration does not attach the 'perf_event' subsystem.
# To do this, add 'perf_event = /cgroup/perf_event;' to the entries in '/etc/cgconfig.conf'.
$ sudo yum install -y libcgroup
$ sudo service cgconfig start
```


#### CentOS 7.1

以下是 CentOS 7.1 的安装说明。如果您使用的是不同的操作系统，请安装相应的软件包。

```
# Install a few utility tools
$ sudo yum install -y tar wget git

# Fetch the Apache Maven repo file.
$ sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo

# Install the EPEL repo so that we can pull in 'libserf-1' as part of our
# subversion install below.
$ sudo yum install -y epel-release

# 'Mesos > 0.21.0' requires 'subversion > 1.8' devel package,
# which is not available in the default repositories.
# Create a WANdisco SVN repo file to install the correct version:
$ sudo bash -c 'cat > /etc/yum.repos.d/wandisco-svn.repo <<EOF
[WANdiscoSVN]
name=WANdisco SVN Repo 1.9
enabled=1
baseurl=http://opensource.wandisco.com/centos/7/svn-1.9/RPMS/$basearch/
gpgcheck=1
gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
EOF'

# Parts of Mesos require systemd in order to operate. However, Mesos
# only supports versions of systemd that contain the 'Delegate' flag.
# This flag was first introduced in 'systemd version 218', which is
# lower than the default version installed by centos. Luckily, centos
# 7.1 has a patched 'systemd < 218' that contains the 'Delegate' flag.
# Explicity update systemd to this patched version.
$ sudo yum update systemd

# Install essential development tools.
$ sudo yum groupinstall -y "Development Tools"

# Install other Mesos dependencies.
$ sudo yum install -y apache-maven python-devel java-1.8.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel cyrus-sasl-md5 apr-devel subversion-devel apr-util-devel
Windows
Following are the instructions for stock Windows 10 and Windows Server 2012 or newer.

Install the latest version of Visual Studio Community 2015. Make sure to select the Common Tools for Visual C++ and the Windows 10 SDK. Start Visual Studio Community to complete the setup and configuration.
Install CMake 3.5.2 or later. Do not run CMake before finishing the Visual Studio Community setup.
Install Gnu Patch 2.5.9-7 or later.
If building from git, make sure you have Windows-style line endings. i.e. git config core.autocrlf true.
Make sure there are no spaces in your build directory. For example, C:/Program Files (x86)/mesos is an invalid build directory.
Building Mesos (Posix)
# Change working directory.
$ cd mesos

# Bootstrap (Only required if building from git repository).
$ ./bootstrap

# Configure and build.
$ mkdir build
$ cd build
$ ../configure
$ make
In order to speed up the build and reduce verbosity of the logs, you can append -j <number of cores> V=0 to make.

# Run test suite.
$ make check

# Install (Optional).
$ make install
Building Mesos (Windows)
# Start a VS2015 x64 Native Tool command prompt.
# This can be found by opening VS2015 and looking under the "tools"
# menu for "Visual Studio Command Prompt".

# Change working directory.
$ cd mesos

# If you are developing on Windows, we recommend running the bootstrap.
# This requires administrator privileges.
$ .\bootstrap.bat

# Generate the solution and build.
$ mkdir build
$ cd build
$ cmake .. -G "Visual Studio 14 2015 Win64" -DENABLE_LIBEVENT=1

# After generating the Visual Studio solution you can use the IDE to open
# the project and skip the next step. In this case it is recommended to set
# `PreferredToolArchitecture` environment variable to `x64`.
# NOTE: `PreferredToolArchitecture` can be set system-wide via Control Panel.
$ msbuild Mesos.sln /p:PreferredToolArchitecture=x64

# mesos-agent.exe can be found in the <repository>\build\src folder.
$ cd src

# The Windows agent exposes new isolators that must be used as with
# the `--isolation` flag. To get started point the agent to a working
# master, using eiher an IP address or zookeeper information.
$ mesos-agent.exe --master=<master> --work_dir=<work folder> --isolation=windows/cpu,filesystem/windows --launcher_dir=<repository>\build\src
Examples
Mesos comes bundled with example frameworks written in C++, Java and Python. The framework binaries will only be available after running make check, as described in the Building Mesos section above.

# Change into build directory.
$ cd build

# Start mesos master (Ensure work directory exists and has proper permissions).
$ ./bin/mesos-master.sh --ip=127.0.0.1 --work_dir=/var/lib/mesos

# Start mesos agent (Ensure work directory exists and has proper permissions).
$ ./bin/mesos-agent.sh --master=127.0.0.1:5050 --work_dir=/var/lib/mesos

# Visit the mesos web page.
$ http://127.0.0.1:5050

# Run C++ framework (Exits after successfully running some tasks.).
$ ./src/test-framework --master=127.0.0.1:5050

# Run Java framework (Exits after successfully running some tasks.).
$ ./src/examples/java/test-framework 127.0.0.1:5050

# Run Python framework (Exits after successfully running some tasks.).
$ ./src/examples/python/test-framework 127.0.0.1:5050
Note: These examples assume you are running Mesos on your local machine. Following them will not allow you to access the Mesos web page in a production environment (e.g. on AWS). For that you will need to specify the actual IP of your host when launching the Mesos master and ensure your firewall settings allow access to port 5050 from the outside world.
```


#### Windows

以下是 Windows 10 和 Windows Server 2012 及以后版本的安装说明。

（1）安装最新版本的 [Visual Studio Community 2015](https://www.visualstudio.com/post-download-vs?sku=community)。选择  Common Tools for Visual C++ 和  Windows 10 SDK。启动  Visual Studio Community  来进行完整的安装和配置；

（2）安装 [CMake 3.5.2](https://cmake.org/files/v3.5/cmake-3.5.2-win32-x86.msi) 或者更新的版本。确保在  Visual Studio Community 安装完成后再安装 CMake；

（3）安装 [Gnu Patch 2.5.9-7](http://downloads.sourceforge.net/project/gnuwin32/patch/2.5.9-7/patch-2.5.9-7-setup.exe) 或者更新的版本。

（4）若是从 git 来编译，确保按照 Windows 风格的行尾，例如：

```
git config core.autocrlf true
```

（5）确保编译路径中不能有空格，例如，下面是一个非法的路径：

```
C:/Program Files (x86)/mesos 
```

### 3. 编译 Mesos（Posix）

```
# Change working directory.
$ cd mesos

# Bootstrap (Only required if building from git repository).
$ ./bootstrap

# Configure and build.
$ mkdir build
$ cd build
$ ../configure
$ make
```

为了加快编译和减少日志的详细程度，可以追加`-j <number of cores> V=0` 到 `make` 指令后面：

```
# Run test suite.
$ make check

# Install (Optional).
$ make install
```

### 4. 编译 Mesos（Windows）

```
# Start a VS2015 x64 Native Tool command prompt.
# This can be found by opening VS2015 and looking under the "tools"
# menu for "Visual Studio Command Prompt".

# Change working directory.
$ cd mesos

# If you are developing on Windows, we recommend running the bootstrap.
# This requires administrator privileges.
$ .\bootstrap.bat

# Generate the solution and build.
$ mkdir build
$ cd build
$ cmake .. -G "Visual Studio 14 2015 Win64" -DENABLE_LIBEVENT=1

# After generating the Visual Studio solution you can use the IDE to open
# the project and skip the next step. In this case it is recommended to set
# `PreferredToolArchitecture` environment variable to `x64`.
# NOTE: `PreferredToolArchitecture` can be set system-wide via Control Panel.
$ msbuild Mesos.sln /p:PreferredToolArchitecture=x64

# mesos-agent.exe can be found in the <repository>\build\src folder.
$ cd src

# The Windows agent exposes new isolators that must be used as with
# the `--isolation` flag. To get started point the agent to a working
# master, using eiher an IP address or zookeeper information.
$ mesos-agent.exe --master=<master> --work_dir=<work folder> --isolation=windows/cpu,filesystem/windows --launcher_dir=<repository>\build\src
```


### 5. 示例

Mesos 捆绑了用 C++、Java 和 Python 编写的 framework 示例。该 framework 的二进制文件将只在`make check`后出现：

```
# Change into build directory.
$ cd build

# Start mesos master (Ensure work directory exists and has proper permissions).
$ ./bin/mesos-master.sh --ip=127.0.0.1 --work_dir=/var/lib/mesos

# Start mesos agent (Ensure work directory exists and has proper permissions).
$ ./bin/mesos-agent.sh --master=127.0.0.1:5050 --work_dir=/var/lib/mesos

# Visit the mesos web page.
$ http://127.0.0.1:5050

# Run C++ framework (Exits after successfully running some tasks.).
$ ./src/test-framework --master=127.0.0.1:5050

# Run Java framework (Exits after successfully running some tasks.).
$ ./src/examples/java/test-framework 127.0.0.1:5050

# Run Python framework (Exits after successfully running some tasks.).
$ ./src/examples/python/test-framework 127.0.0.1:5050
```

注意：这些示例假设您是在本地机器上运行 Mesos。他们不会允许您在生产环境中（例如，在 AWS 中）访问 Mesos 网页。为此，您将需要启动 Mesos master 时指定主机的实际 IP，并确保您的防火墙设置允许从外部访问端口 5050。

## 参考文献

* http://mesos.apache.org/gettingstarted/