+++
title = 'Siv3D Talk 1'
date = 2024-09-09T06:32:56+08:00
draft = false
+++

前几天在飞鱼子的推荐下知道了[Maxwell&#39;s Puzzling Demon](https://store.steampowered.com/app/2770160) (下文简称 MPD) 所使用的引擎 [Siv3D](https://siv3d.github.io/en-us/)（下文简称 siv），不过在上手过程中遇到了不少麻烦。

## 从 Visual Studio 到 Windows Subsystem Linux

siv 官方在 Windows 平台所选用的工具链是 VS，这玩意下过的都知道有多占空间，因此我也是直接转身走进 WSL 看看替代解决方式。幸好我用的 Ubuntu 20.04 LTS 是官方少数支持的 Linux 版本，不然我又得转身回到 VS 了。于是我按指示 git clone 下来了项目，看看能不能运行起来。

## 衫裤跑路，再探再报

因为记录不及时，我已经不是很确定当时为什么会写下 `rm -rf /`了，大概是为了清除 build 文件夹的内容，又忘了正确写法（`rm -rf ./`）。

好在我不是 root 状态下运行的，最后只有~被删的只剩安装到 cmake 上的 siv 了，有权限的文件夹都被删差不多了，剩下的都是没权限动的文件夹，好在没多少有权限的，算是可喜可贺。

然后又重新 clone 了一遍，重走配置路。

最后运行又出意外了：

> Vertex shader compilation failed: 0:5(10): error: GLSL 4.10 is not supported. Supported versions are: 1.10, 1.20, 1.30, 1.40, 1.50, 3.30, 1.00 ES, and 3.00 ES

STFW 也没找到什么好用的解决方案，郁闷完了。

## 从 WSL 回到 VS

和 siv 分开了一段时间，看到了这篇[[万字长文]Visual Studio Code 配置 C/C++ 开发环境的最佳实践(VSCode + Clangd + XMake) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/398790625)，于是配了个 VS Build Tools 作为工具链的一环，也把 msys2 解放出来（其实从装 Scoop 之后就没怎么用过 msys2 了，可以卸载了已经）。

装好后想起了阔别几日的 siv，壮志未酬之下也是开始了新一轮折腾。

这一下把 xmake makefile cmake 的写法都看了个遍，但是一番各种 make 之后发现其中的 link 过程应该是比较不简单的，也就是说没法光靠蒙就 make 出来了。

无奈之下下载了 vs，确实很简单就能跑起来了，打开模板即可。

但是vs还是太占空间了，如果有办法用vs build tools就搞定的话说什么也不用vs了。但是stfw半天没找到办法。

## 又从 VS 回到 WSL

VS build tools 那边想不出办法，回头看看 WSL。

继续 STFW 找到了关键，他指出[WSL doesn&#39;t support any version of OpenGL newer than 3.x](https://github.com/alecjacobson/computer-graphics-shader-pipeline/issues/47#issuecomment-550646871)，继续 STFW 发现好像有可能是真的，因为 d3d11 到 opengl 的一些支持问题。但是也看到有人提出了一些解法[Can wsl2 support a higher version of OpenGL? · microsoft/WSL · Discussion #6154 (github.com)](https://github.com/microsoft/WSL/discussions/6154)

然后又在上一个里面找到了这个能用的 ppa [kisak-mesa fresh : kisak (launchpad.net)](https://launchpad.net/~kisak/+archive/ubuntu/kisak-mesa)，装上后也是成功的运行起来了。

## 又又从 WSL 回到 VS

vs 只要打开模板就能启动让我意识到了模板才是关键，于是我下载了模板并解压，结合微软官网的教程（[演练：使用 MSBuild 创建 Visual Studio C++ 项目 | Microsoft Learn](https://learn.microsoft.com/zh-cn/cpp/build/walkthrough-using-msbuild-to-create-a-visual-cpp-project?view=msvc-170)，以及等等等等）发现 vcxproj 里就包含了编译信息，遂转回VSCode，成功运行。

目前仍存在的问题：Clangd对Siv3d.hpp的补全完全无能为力，所以其实和VSCode关系不大了，完全是在用VS Build Tools撑着。不过换用Cpp扩展能跑起来，但写着体验不如VS（补全和跳转比Clangd慢太多了），可能一时半会是卸不掉VS了。

顺便CMake和XMake使用VS Build Tools的工具链生成出来的都是sln文件（Solution在VS叫解决方案，也很无厘头），还是要msbuild才生成可执行文件，不能一步到位也挺让人恼火的。

结论就是要玩siv3d最好是直接上VS，VS Build Tools本身的定位很微妙。

## Update 10/6：一个月后

一个月过去，我的cpp水平不说突飞猛进吧，也可以说是完全没动。但是最后终于是找到很有可能的导致clangd用不了的原因了，记录一下。

一句话：**Siv3D用的这个功能Clang没有实现。**

就是这样。
