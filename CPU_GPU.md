- [cpu核心数与线程数](#cpu核心数与线程数)
- [GPU](#gpu)

## cpu核心数与线程数
[参考](https://zhuanlan.zhihu.com/p/86855590)

**物理cpu数 physical cpu**
- 指主板上实际插入的 cpu 硬件个数（socket）。（但是这一概念经常被泛泛的说成是 cpu 数，这很容易导致与 core 数，processor 数等概念混淆，所以此处强调是物理 cpu 数）。
- 由于在主板上引入多个 cpu 插槽需要更复杂的硬件支持（连接不同插槽的 cpu 到内存和其他资源），通常只会在服务器上才这样做。在家用电脑中，一般主板上只会有一个 cpu 插槽。

**核心 core**
- 一开始，每个物理 cpu 上只有一个核心（a single core），对操作系统而言，也就是同一时刻只能运行一个进程/线程。 为了提高性能，cpu 厂商开始在单个物理 cpu 上增加核心（实实在在的硬件存在），也就出现了双核心 cpu（dual-core cpu）以及多核心 cpu（multiple cores），这样一个双核心 cpu 就是同一时刻能够运行两个进程/线程的。

**同时多线程技术 simultaneous multithreading 和 超线程技术 hyper–threading/HT**
- 本质一样，是为了提高单个 core 同一时刻能够执行的多线程数的技术（充分利用单个 core 的计算能力，尽量让其“一刻也不得闲”）。
- simultaneous multithreading 缩写是 SMT，AMD 和其他 cpu 厂商的称呼。 hyper–threading 是 Intel 的称呼，可以认为 hyper–threading 是 SMT 的一种具体技术实现。
- 在类似技术下，产生了如下等价术语：
  - 虚拟 core： virtual core
  - 逻辑 processer： logical processor
  - 线程：thread
- 所以可以这样说：某款采用 SMT 技术的 4 核心 AMD cpu 提供了 8 线程同时执行的能力；某款采用 HT 技术的 2 核心 Intel cpu 提供了 4 线程同时执行的能力。

**linux系统查看cpu信息**
- 查看物理cpu数： `cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l`
- 查看每个物理 cpu 中 核心数(core 数)：`cat /proc/cpuinfo | grep "cpu cores" | uniq`
- 查看总的逻辑 cpu 数（processor 数）：`cat /proc/cpuinfo| grep "processor"| wc -l`
- 查看 cpu 型号：`cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c`
- 判断 cpu 是否 64 位：检查 cpuinfo 中的 flags 区段，看是否有 lm （long mode） 标识
- `lscpu` 命令可以同时看到上述信息

**x86与x86_64**
- x86
  - 泛指一系列基于 Intel 8086 且向后兼容的中央处理器指令集架构。
  - x86 的 32 位架构一般又被称作 IA-32，全名为 “Intel Architecture, 32-bit”。
  - 值得注意的是，Intel 也推出过 IA-64 架构，虽然名字上与 “IA-32” 相似，但两者完全不兼容，并不属于x86指令集架构家族。
- x86-64
  - 又称 x64，即英文词 **64**-bit e**x**tended，64 位拓展 的简写，是 x86 架构的 64 位拓展，向后兼容于 16 位及 32 位的 x86 架构。
  - 不同厂商有不同的称呼：
    - x64 于 1999 年由 AMD 设计，AMD 首次公开 64 位集以扩展给 x86，称为 “AMD64”
    - 其后也为 Intel 所采用，Intel 称为 “Intel 64”，在之前曾使用过“Clackamas Technology” (CT)、“IA-32e”及“EM64T”
    - 苹果公司和 RPM 包管理员称为 “x86-64” 或 “x86_64”
    - 甲骨文公司及 Microsoft 称为 “x64”
    - BSD 家族及其他 Linux 发行版则使用 “amd64”，32 位版本则称为 “i386”（或 i486/586/686）
    - Arch Linux及其派生发行版用x86_64称呼此64位架构

**多线程程序线程数**
为了让我们的多线程程序更好的利用 cpu 资源，我们通常会先了解机器拥有的 processor 数，有若干手段可以获取这一信息：
- cpuinfo 中查看：比如上文中的 `cat /proc/cpuinfo | grep "processor" | wc -l`
- top 命令查看：cpu0,cpu1,...
- 编程：比如在 Java 中用 `Runtime.getRuntime().availableProcessors()`
具体在多线程程序中设置线程数多大，对计算密集型的程序有的建议是 processor count + 1，有的建议是 processor count 的 1.5 倍，都是经验值，实测为准。

总的逻辑 cpu 数 = 物理 cpu 数 * 每颗物理 cpu 的核心数 * 每个核心的超线程数

**进程与线程**

最基本的关系：进程包含线程。线程是操作系统最小调度单元，一个进程至少有一个线程用于任务，如果一个进程里开多线程，实际上仍然是串行，只不过利用一些技术比如时间片之类的去频繁切换进程，让人误以为在并行。如果开多个进程才是真正意义上的并行。

**CPU的核心数和线程数量是什么关系**
简单点说吧，处理器核数（包含超线程数）是一种执行资源，资源数量就是核数个数。应用程序的线程数就是服务请求数。而操作系统的作用是如何用有限的资源来服务好应用程序的请求，这就是进程调度的功能。
一般情况下，服务请求线程会“相对公平”地分配到核上运行，并且在时间片上轮流使用的，即并发执行（不一定是并行执行）
比如系统有4个核，如果：
1. 只有3个线程，那就分配到3个核上运行
2. 只有8个线程，那就每个核分配两个线程运行
3. 只有10个线程，那就有些核跑3个线程，有些核跑2个线程

相对，这不是绝对的，要看应用程序使用的是什么调度策略，OS是尽量让每个线程的平均等待时间最小化。
如果系统只有4个核，大数值计算的应用，到底是拆成4个线程，还是8个线程，还是12个线程，这些在并行框架里面都有分析，要看你的具体应用计算特征才能知道哪种拆分性能高。

## GPU
[参考](https://zhuanlan.zhihu.com/p/91334380)
**GPU型号含义**
- 显卡： 简单理解这个就是GPU，尤其指NVIDIA公司生产的GPU系列，因为后面介绍的cuda,cudnn都是NVIDIA公司针对自身的GPU独家设计的
- 显卡驱动：很明显就是字面意思，通常指NVIDIA Driver，其实它就是一个驱动软件，而前面的显卡就是硬件。
- gpu架构：Tesla、Fermi、Kepler、Maxwell、Pascal、Volta、Turing、Ampere、Hopper、ADA
- 芯片型号：GT200、GK210、GM104、GF104等
- 显卡系列：GeForce、Quadro、Tesla
- GeForce显卡型号：G/GS、GT、GTS、GTX、RTX

gpu架构指的是硬件的设计方式，例如流处理器簇中有多少个core、是否有L1 or L2缓存、是否有双精度计算单元等等。每一代的架构是一种思想，如何去更好完成并行的思想

芯片就是对上述gpu架构思想的实现，例如芯片型号GT200中第二个字母代表是哪一代架构，有时会有100和200代的芯片，它们基本设计思路是跟这一代的架构一致，只是在细节上做了一些改变，例如GK210比GK110的寄存器就多一倍。有时候一张显卡里面可能有两张芯片，Tesla k80用了两块GK210芯片。这里第一代的gpu架构的命名也是Tesla，但现在基本已经没有这种设计的卡了，下文如果提到了会用Tesla架构和Tesla系列来进行区分。

而显卡系列在本质上并没有什么区别，只是NVIDIA希望区分成三种选择，GeFore用于家庭娱乐，Quadro用于工作站，而Tesla系列用于服务器。Tesla的k型号卡为了高性能科学计算而设计，比较突出的优点是双精度浮点运算能力高并且支持ECC内存，但是双精度能力好在深度学习训练上并没有什么卵用，所以Tesla系列又推出了M型号来做专门的训练深度学习网络的显卡。需要注意的是Tesla系列没有显示输出接口，它专注于数据计算而不是图形显示。

**CUDA**
看了很多答案，有人说CUDA就是一门编程语言，像C,C++,python 一样，也有人说CUDA是API。CUDA英文全称是Compute Unified Device Architecture，是显卡厂商NVIDIA推出的运算平台。 CUDA™是一种由NVIDIA推出的通用并行计算架构，该架构使GPU能够解决复杂的计算问题。按照官方的说法是，CUDA是一个并行计算平台和编程模型，能够使得使用GPU进行通用计算变得简单和优雅。

**CUDNN**
这个其实就是一个专门为深度学习计算设计的软件库，里面提供了很多专门的计算函数，如卷积等。很多其他的软件库和中间件，包括实现c++ STL的thrust、实现gpu版本blas的cublas、实现快速傅里叶变换的cuFFT、实现稀疏矩阵运算操作的cuSparse以及实现深度学习网络加速的cuDNN等等

**CUDA Toolkit**
CUDA Toolkit由以下组件组成：
- Compiler: CUDA-C和CUDA-C++编译器NVCC位于bin/目录中。它建立在NVVM优化器之上，而NVVM优化器本身构建在LLVM编译器基础结构之上。因此开发人员可以使用nvm/目录下的Compiler SDK来直接针对NVVM进行开发。
- Tools: 提供一些像profiler,debuggers等工具，这些工具可以从bin/目录中获取
- Libraries: 下面列出的部分科学库和实用程序库可以在lib/目录中使用(Windows上的DLL位于bin/中)，它们的接口在include/目录中可获取。
  - cudart: CUDA Runtime
  - cudadevrt: CUDA device runtime
  - cupti: CUDA profiling tools interface
  - nvml: NVIDIA management library
  - nvrtc: CUDA runtime compilation
  - cublas: BLAS (Basic Linear Algebra Subprograms，基础线性代数程序集)
  - cublas_device: BLAS kernel interface
  - ...

- CUDA Samples: 演示如何使用各种CUDA和library API的代码示例。可在Linux和Mac上的samples/目录中获得，Windows上的路径是C：\ProgramData\NVIDIA Corporation\CUDA Samples中。在Linux和Mac上，samples/目录是只读的，如果要对它们进行修改，则必须将这些示例复制到另一个位置。
- CUDA Driver: 运行CUDA应用程序需要系统至少有一个具有CUDA功能的GPU和与CUDA工具包兼容的驱动程序。每个版本的CUDA工具包都对应一个最低版本的CUDA Driver，也就是说如果你安装的CUDA Driver版本比官方推荐的还低，那么很可能会无法正常运行。CUDA Driver是向后兼容的，这意味着根据CUDA的特定版本编译的应用程序将继续在后续发布的Driver上也能继续工作。通常为了方便，在安装CUDA Toolkit的时候会默认安装CUDA Driver。在开发阶段可以选择默认安装Driver，但是对于像Tesla GPU这样的商用情况时，建议在官方安装最新版本的Driver。

**nvcc**
nvcc其实就是CUDA的编译器,可以从CUDA Toolkit的/bin目录中获取,类似于gcc就是c语言的编译器。由于程序是要经过编译器编程成可执行的二进制文件，而cuda程序有两种代码，一种是运行在cpu上的host代码，一种是运行在gpu上的device代码，所以nvcc编译器要保证两部分代码能够编译成二进制文件在不同的机器上执行。

**nvidia-smi**
nvidia-smi全称是NVIDIA System Management Interface ，它是一个基于前面介绍过的NVIDIA Management Library(NVML)构建的命令行实用工具，旨在帮助管理和监控NVIDIA GPU设备。