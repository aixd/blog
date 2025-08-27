+++
title = 'Games101 Hw'
date = 2024-10-14T03:44:52+08:00
draft = false
+++

## pa0

下载虚拟机硬盘要下5个多g, 还是百度网盘, 下不了一点, 直接wsl走起.

```bash
sudo apt install libopencv-dev libeigen3-dev
```

下载opencv和eigen3依赖.

在host windows上启动vscode, remote-ssh插件连接wsl.

至于窗口界面, 我看别人有用**VcXsrv**弄到windows上显示的, 我目前是在用wsl自己显示, 可能是因为我搞了个ppa更新了opengl版本, 不保证稳定性和帧数什么的[Kiss-Mesa Fresh ： Kiske --- kisak-mesa fresh : kisak (launchpad.net)](https://launchpad.net/~kisak/+archive/ubuntu/kisak-mesa)

## pa1

反正也是正式开搞了.

clangd插件报错 `'opencv2/opencv.hpp' file not found`的话, 可以

```bash
sudo ln -s /usr/include/opencv4/opencv2 /usr/include/opencv2
```

但是我不建议破坏原有的文件结构, 多一事不如少一事, 咱们先试试build

```
sorick@LAPTOP:~/projects/c-workspace/games101/pa1$ mkdir build && cd build
sorick@LAPTOP:~/projects/c-workspace/games101/pa1/build$ cmake ..
-- The C compiler identification is GNU 9.4.0
-- The CXX compiler identification is GNU 9.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found OpenCV: /usr (found version "4.2.0")
-- Configuring done
-- Generating done
-- Build files have been written to: /home/sorick/projects/c-workspace/games101/pa1/build
sorick@LAPTOP:~/projects/c-workspace/games101/pa1/build$ make
Scanning dependencies of target Rasterizer
[ 25%] Building CXX object CMakeFiles/Rasterizer.dir/main.cpp.o
[ 50%] Building CXX object CMakeFiles/Rasterizer.dir/rasterizer.cpp.o
[ 75%] Building CXX object CMakeFiles/Rasterizer.dir/Triangle.cpp.o
[100%] Linking CXX executable Rasterizer
[100%] Built target Rasterizer
sorick@LAPTOP:~/projects/c-workspace/games101/pa1/build$ ls
CMakeCache.txt  CMakeFiles  Makefile  Rasterizer  cmake_install.cmake
sorick@LAPTOP:~/projects/c-workspace/games101/pa1/build$ ./Rasterizer
frame count: 0
```

运行起来也是能正常找到的, 那就是clangd的问题了. 在 `.clangd`文件中添加

```
CompileFlags:
  Add: [-I, "/usr/include/opencv4", otherFlags...]
```

把 `otherFlags...` 换成它应该是的东西, 比如 `-xc++, -std=c++20,` 之类的.

然后ctrl+shift+p restart clangd server, 即可正常找到opencv2了.

然后一开始犯了个错, 其实应该用l = -r, b = -t的, 但是写矩阵的时候搞错了, 最后改过来就正常了.
