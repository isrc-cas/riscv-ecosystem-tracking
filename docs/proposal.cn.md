# RISC-V 软件生态状态跟踪查询服务

## 背景、动机、目标

经过十年的萌芽和发展，围绕着 RISC-V 的指令集生态环境已经得到了极大的关注。在MCU领域，RISC-V已经是事实上的未来标准。近几年来，RISC-V生态开始朝着桌面办公、AIoT、高性能计算（HPC）、云服务、智能移动端（手机、平板、电视）等领域扩张。RISC-V国际基金会（简称RVI）维护了两个列表：一个是基于RISC-V开源指令集的CPU列表，另一个是适配了RISC-V指令集架构或平台标准的软件列表。两个列表都在比较活跃的更新中。

riscv-software-list 目前的问题在于，仅仅提供了一个带有 category 分类的单层次二元状态，这样的数据站在2021年来看已经不够准确。例如 OpenJDK 的支持早在2016年就完成了纯解释器模式的 Zero 后端的移植，并被列到了RVI的 riscv-software-list 中。但是直到最近华为BishengJDK团队开源了 OpenJDK/HotSpot 的 RV64GC 后端实现，Java 的生态才真正有可能在 RISC-V 平台上得到生产环境可以考虑的性能（速度相差20倍）。单层二元状态列表在RISC-V早期提供了重要的帮助和指引作用，但是在2021年，随着各个领域的软件生态开始认真地考虑RISC-V平台，目前的列表已经无法满足信息的需要了。

也就是说，如果一个垂直领域的厂商，希望能够考虑使用了RISC-V作为其硬件平台，需要考虑的软件移植、适配等工作量和成熟度的时候，需要一个该客户领域对应的软件栈的全景图，进行信息的展示，能够直观的看到，哪些部分的支持已经成熟稳定，哪些部分还是缺失的，需要更多的开源或商业贡献。

这就是本项目的发起的契机。我们希望通过本项目，为AIoT、手机和智能平板、Linux桌面办公、Linux服务器、高性能计算、深度学习等领域的厂商提供更为丰富的情报的帮助。同时，能够让有志于为RISC-V开源软件生态做出贡献的志愿者和爱好者，能够通过此项目，更加快速准确的找到自己感兴趣、技能匹配的开源项目进行贡献。

本项目本身采用 CC-BY-SA 协议，代码部分采用 Apache 协议，保持 PLCT 实验室以及 RVI 一贯的自由、开源精神。欢迎加入到本项目并参与共建。

## 总体设计

跟踪数据库包含四层：第一层是领域的区分图，第二层是每个领域的重要的软件或框架的架构（层次）图；第三层是每个组成部分的同类型软件的状态；最后一层是具体的软件支持情况。

不同层次之间提供一个web形式交互的呈现，可以通过点击对应的图形实现层次之间的跳转。

### 第一层：领域划分

目前细分为以下领域（视角）：
- Linux Desktop： 基于类似 Hifive Unmatched 这样的硬件，让程序开发人员可以使用 RISC-V 平台进行日常的办公、开发工作。能够达到跟 x86、Arm64 同样的开发人员体验。
- Server：关注目前主流的运行在 Linux/*BSD 上运行的服务器软件。让 RISC-V 平台可以搭建网站、小型数据库等各类网络服务。关注内容包括 Apache、MySQL、Tomcat、Hadoop、Spark、Redis、PHP、Golang、RoR 等。
- HPC：关注高性能计算领域所有主流软件在 RISC-V 平台上的移植和性能现状。预期是全球 HPC TOP100 在2025年左右会出现以 RISC-V 作为主控CPU的超算，能够进行大气模拟、石油开采、蛋白质分析等科学计算任务。常见关注的由 OpenMPI、FORTRAN、Linpack 等。
- AOSP：安卓系统是目前智能移动端的事实标准。将AOSP移植到 RISC-V 平台之后，会立即使智慧屏和楼宇广告等细分市场向 RISC-V 厂商打开。目前主要跟踪PLCT实验室在 AOSP for RISC-V 移植工作上的进展（这是目前唯一活跃的 AOSP 移植项目）。
- AIoT：我还不知道 AIoT 的具体技术定义什么，跟 IoT 的区别在什么地方。不过，这个词汇作为商业/业务的使用已经很普及了。目前我主要是跟 RT-Thread 在进行 IoT/AIoT 方面的沟通，由 RT-Thread 提供业务场景的信息。包括用于嵌入式的 JS引擎、WASM引擎等。
- AI / Deep Learning：到2021年，AI已经从学术创新阶段演变为技术落地应用的阶段。虽然现在的新技术宣传没有2016-2019年左右频繁，在各个业务领域已经开始逐步推广和使用。RISC-V 在深度学习加速和人工智能计算上有着自己的优势。是本项目的关注方向。包括 Tensorflow、TVM、MXNet、PyTorch、OpenCV 等。
- Embedded / MCU：是目前 RISC-V 应用最为成熟的领域。多数没有 MMU，不需要运行 Linux，而是运行类似 RT-Thread、VxWorks 等系统。这个领域已经有大量的现状跟踪，并且完成度已经很高，不是本项目的跟踪重点。

如何添加新的领域（视角）：

请在本repo开一个issue，标题前缀 `[Proposal][Level 1]` 写上
1. 你想要关注的领域以及理由
1. 你个人或者团队可以投入的、用于以后维护该领域的时间资源
1. 推荐的跟这个领域有关的公司、团队、开源项目、开发者。

### 第二层：（每个领域的）总体架构

第二层的示例可以参考 AOSP 的架构：
https://developer.android.com/guide/platform

![Android Software Stack](https://developer.android.com/guide/platform/images/android-stack_2x.png)

以 AOSP 为例，可以分为 Linux Kernel、HAL、C/C++ Libs，Android Runtime、Java Framework、System Services；同时，也包含了 AndroidStudio、ADB、Kotlin 等开发工具、调试工具、编程语言等组成。

每个领域都有一个类似上图（链接）所示 Android Software Stack 的架构形式。需要注意的是在本项目中，将相同功能的软件合并为同一类在第二层呈现，例如 Clang/LLVM 与 GNU GCC 就归类于 C/C++ Toolchain，而其细分放在第三层中进行区分。第三层类似于 riscv-software-list 中的 category 的进一步细分。

### 第三层：同类型软件的对比

不同的开源项目可能归属于同一个类别，例如 Clang/LLVM 跟 GNU GCC 就同时属于 C/C++ Toolchain。在本项目中，将  C/C++ Toolchain 作为一个概念提出出来，作为第三层的一个跟踪对象。在这个概念下，列出所有可以使用（选型）的软件。类似于 class 和 object 的关系。

主要注意同一个开源项目有可能归属于多种类型，例如 Clang/LLVM 就同时归属于 C/C++ Toolchain、FORTRAN Compiler（Flang）以及 linux host debugger（lldb）等类别。所以也可以理解为（Java中的） interface 和 object 之间的关系。

### 第四层：具体的开源项目或商业软件

第四层是具体的开源项目（或商业软件）。提供具体的支持信息和更新历史、已知bugs、维护者信息等。

以 V8 for RISC-V 为例，跟踪系统提供以下信息：
- 总体支持情况概述。由人工确认和编写。
- 参考使用的技术文档、demo、视频等资料。
- 源代码地址信息。从何处可以获得源代码。
- issues、bug reports 等信息。
- 主要的维护人员信息。
- 是否有 upstream、upstream 状态等。
- 最新版本对于 RV64G、RV32G、C、B、P、V 等的支持状态。
- 性能信息的跟踪。
- 依赖的软件包信息。
- 下游软件信息，例如 NodeJS 依赖于 V8，但是并不是依赖于 V8 master branch，而是依赖于某个特定版本，存在版本信息。

### 支持检索的字段

除了关键字搜索，提供以下属性字段用于细分：
- 参与开发和维护的开发者或公司、团队。
- RISC-V 的某个特定的指令扩展，例如 V、B、P 等。
- TBD

## 本项目跟踪、维护的数据

本项目目前采用人工静态方式维护，数据通过文本文件方式存放在仓库中，使用 git 仓库方式管理。避免使用数据库等复杂形式。

### 范围及表示

以 V8 for RISC-V 为例，跟踪系统提供以下信息：
- 总体支持情况概述。由人工确认和编写。
- 参考使用的技术文档、demo、视频等资料。
- 源代码地址信息。从何处可以获得源代码。
- issues、bug reports 等信息。
- 主要的维护人员信息。
- 是否有 upstream、upstream 状态等。
- 最新版本对于 RV64G、RV32G、C、B、P、V 等的支持状态。
- 性能信息的跟踪。
- 依赖的软件包信息。
- 下游软件信息，例如 NodeJS 依赖于 V8，但是并不是依赖于 V8 master branch，而是依赖于某个特定版本，存在版本信息。

### 存储及分享

- 采用人类可读性高的文本文件方式存储，默认使用JSON方式，方便JS直接读取和处理。
- 提供 CSV 格式导出和交换格式。为每个概念实体添加唯一ID，方便导入到数据库中的时候建立主键和外键。
- 支持新属性的添加。本项目的数据，会不断的添加新的被跟踪的客体，以及为客体添加新的属性字段。

### 呈现及交互

- 提供基于HTML5的 Web 交互方式；支持动态交互，在不同层次之间进行转换
- 支持 GraphViz/dot 格式导出。
- 提供一个快捷的更新和报错方式，在看到信息过时或错误的时候可以随手报错。

## 如何参与贡献

本项目由 吴伟 利用业务时间维护，作为 PLCT Lab 的一个 side project。
如果你对这个项目感兴趣，可以给我发邮件： `吴伟 <wuwei2016@iscas.ac.cn>`

### Web 前端实习生招募

如项目的计划所言，我们需要一个支持动态交互的网页来呈现。
没有后台，直接读取静态文件即可获得呈现需要的数据。适合在校大学生作为自己的短期（1-2个月）的项目。
欢迎联系我报名。报名方式可以参考：
[PLCT 实习生招聘](https://github.com/isrc-cas/PLCT-Weekly/blob/master/interns.md)
