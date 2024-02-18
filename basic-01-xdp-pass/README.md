# Tutorial: Basic01 - 加载你的第一个BPF程序

欢迎来到这个 XDP 教程的第一步。

XDP 的编程语言是 eBPF（扩展伯克利数据包过滤器），我们将简称为 BPF。因此，本教程也适用于学习如何编写其他 BPF 程序；但主要重点是用于 XDP 钩子的 BPF 程序。在本课程和接下来的几节课中，我们将重点介绍 BPF 的基础知识，以便快速上手；随后的课程将在此基础上教您如何使用 XDP 进行数据包处理。

由于这是第一课，我们将以轻松的方式开始，实际上不包括任何作业。只需阅读下面的文本，并确保您能够加载程序，并理解其中的内容。

* 目录 [TOC]

* 第一步：设置依赖

有许多设置依赖项，需要按照这个 git 仓库中的源代码才能进行编译。如果您还没有阅读和完成 [[file:../setup_dependencies.org]] 指南，请先进行阅读。