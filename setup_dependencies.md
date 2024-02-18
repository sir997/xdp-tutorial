# 设置依赖

在开始完成此XDP教程中的步骤之前，请阅读此文档并安装所需的软件包。

## 基于libxdp和libbpf

这个XDP教程利用libxdp来加载和管理XDP程序。libxdp库作为XDP项目的一部分进行维护。该教程还利用libbpf来简化BPF程序的开发和加载。libbpf库是内核树的一部分，在tools/lib/bpf目录下，但Facebook工程师在GitHub上维护了一个独立的构建版本，位于https://github.com/libbpf/libbpf。

### libxdp和libbpf作为git子模块

这个仓库使用了[[https://github.com/xdp-project/xdp-tools/][libxdp]]和[[https://github.com/libbpf/libbpf][libbpf]]作为git子模块。

我们将从克隆该仓库开始：

```
git clone --recurse-submodules https://github.com/xdp-project/xdp-tutorial.git
```

注意：如果在之前的步骤中忘记了使用 --recurse-submodules 选项，或者之前已经克隆过而没有包含子模块，你可以使用以下命令：
```
git submodule update --init
```

如果你需要将子模块添加到自己的项目中，你可以使用以下命令：
```
git submodule add https://github.com/xdp-project/xdp-tools/ xdp-tools
git submodule add https://github.com/libbpf/libbpf/ libbpf
```

## 依赖

主要的依赖项包括 =libxdp=、=libbpf=、=llvm=、=clang= 和 =libelf=。LLVM+clang 将我们的受限 C 代码编译成 BPF 字节码，该字节码存储在 ELF 对象文件中（=libelf=），然后由 =libbpf= 通过 =bpf= 系统调用加载到内核中。XDP 程序由 =libxdp= 管理，它实现了 XDP 多分发协议。一些教程还使用 =perf= 实用工具通过跟踪点跟踪内核行为。

此存储库中的 Makefile 将尝试检测您是否缺少某些依赖项，并为您提供一些指导。

- Fedora

在运行 Fedora Linux 发行版的计算机上，安装以下软件包：
```
 $ sudo dnf install clang llvm
 $ sudo dnf install elfutils-libelf-devel libpcap-devel perf glibc-devel.i686
```

还要注意，默认情况下，Fedora 对内核允许的锁定内存量设置了限制，这可能会影响加载 BPF 映射。=testenv.sh= 脚本将为您调整此设置，但如果您没有使用该脚本，则可能会遇到问题。请使用以下命令提高限制：
```
$ ulimit -l 1024
```

请注意，您需要在用于加载程序的 shell 中执行此操作（特别是不能与 =sudo= 一起使用）。

- Debian/Ubuntu

在 Debian 和 Ubuntu 安装中，可以按照以下方式安装依赖项：
```
$ sudo apt install clang llvm libelf-dev libpcap-dev build-essential libc6-dev-i386
```

在 Debian 上安装 'perf' 实用程序，请执行以下命令：
``` 
$ sudo apt install linux-perf
```
在 Ubuntu 上运行以下命令：
```
$ sudo apt install linux-tools-$(uname -r)
```

- openSUSE

在运行 openSUSE 发行版的计算机上，安装以下软件包：
```
$ sudo zypper install clang llvm libelf-devel libpcap-devel perf linux-glibc-devel
```

## 内核头文件依赖

Linux内核提供了许多头文件，通常安装在 =/usr/include/linux= 目录下。不同的Linux发行版通常会提供一个包含这些头文件的软件包。

我们依赖的一些头文件位于内核树中的 include/uapi/linux/ 目录下（例如 include/uapi/linux/bpf.h），但你不应该直接包含这些文件，因为它们在导出/安装到发行版的 =/usr/include/linux= 目录时会经过转换处理。在内核git树中，你可以运行命令：=make headers_install=，它会在 "usr/" 目录下创建大量的头文件。

目前，本教程依赖于你的发行版提供的内核头文件包。我们以后可能会选择部分自行管理这些头文件。

- Fedora
```
$ sudo dnf install kernel-headers
```

- Debian/Ubuntu
```
$ sudo apt install linux-headers-$(uname -r)
```

- openSUSE
```
$ sudo zypper install kernel-devel
```

## 额外工具

=bpftool= 是检查正在系统上运行的 BPF 程序的推荐工具。它还提供了对 eBPF 程序和映射进行简单操作的功能。=bpftool= 是 Linux 内核树的一部分，位于 [[https://github.com/torvalds/linux/tree/master/tools/bpf/bpftool][tools/bpf/bpftool/]]，但一些 Linux 发行版也将该工具打包为软件包。

如果您计划通过数据包处理示例进行工作，则还应安装 tcpdump。

- Fedora
```
$ sudo dnf install bpftool
$ sudo dnf install tcpdump
```

- Ubuntu
```
 $ sudo apt install linux-tools-common linux-tools-generic
 $ sudo apt install tcpdump
```

- Debian
```
 $ sudo apt install bpftool
 $ sudo apt install tcpdump
 ```

- openSUSE
```
 $ sudo zypper install bpftool
 $ sudo zypper install tcpdump
```

## 生成所需的文件

一旦您安装了所需的依赖项，就需要生成必要的文件以进行练习。
首先从存储库的根目录运行 ./configure 确保每个依赖项都已安装。
```
$ ./configure
```

现在运行 make 以生成所有必要的文件：
```
$ make
```