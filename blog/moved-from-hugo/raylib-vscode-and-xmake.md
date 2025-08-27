+++
title = 'Raylib, VSCode and Xmake'
date = 2024-09-22T06:13:33+08:00
draft = false
+++

## 前言

最近感觉要写的博客太多了，玩了很多东西，再不写就来不及了。

顺便把前面的博客也修了一下，把链接里的内容尽量转移到了博客里，以免其他网站挂掉导致博客内容失效。

## raylib & xrepo

raylib是个专精2d的游戏功能库，其实和引擎还不是很一样，更多是提供了一些用于游戏制作的功能，而不是直接就能拼搭开始写游戏。

用我们这套就不用专门下载raylib了，使用xrepo下载即可，但是xrepo的下载在国内不是很行，查阅doc [使用远程包 - xmake](https://xmake.io/#/zh-cn/package/remote_package?id=%e9%95%9c%e5%83%8f%e4%bb%a3%e7%90%86) 后发现用下面这个切内置镜像源。

```bash
$ xmake g --proxy_pac=github_mirror.lua
```

还是跑不通，一看发现他用的镜像 `hub.nuaa.cf` 挂了

```lua
import("net.fasturl")

function mirror(url)
    local configs = {}
    local proxyurls = {"hub.nuaa.cf"}
    fasturl.add(proxyurls)
    proxyurls = fasturl.sort(proxyurls)
    if #proxyurls > 0 then
        return url:replace("/github.com/", "/" .. proxyurls[1] .. "/", {plain = true})
    end
end
```

我们只好自己写一个pac.lua了，windows用户在 `C:\Users\用户名\AppData\Local\.xmake` 创建新文件pac.lua，ubuntu在 `/home/sorick/.xmake` ，别的系统自己看  `xmake g --help` 里 `--proxy_pac=` 下的目录。

```lua
function main(url, host)
    if host:find("github.com") then
        return true
    end
end

function mirror(url)
    return "https://ghp.ci/" .. url
end
```

这个是按照官方文档写的，非github链接还没有尝试会不会不走代理，不过xmake建了个repo存了绝大部分包了，非github包也可以尝试去手动下载。

## vscode & xmake

使用xmake新建项目 `xmake create 项目名` ，然后修改xmake.lua

```lua
add_rules("mode.debug", "mode.release")

add_requires("raylib")

target("hello-xmake-raylib")
    set_kind("binary")
    add_files("src/*.cpp")
    add_packages("raylib")
    set_languages("cxx14")

```

这里cxx14单纯只是因为msvc只支持c++14 17 20，23不知道是否支持，如果你用别的 Toolchain，也可以随意更改。

然后在src/main.cpp下面就可以开始写raylib了！

如果没有自动生成 `compile_commands.json`，我们可以在vscode中使用 `>XMake: UpdateIntellisense` 命令手动触发生成，或者在命令行中输入以下命令来生成。

```text
xmake project -k compile_commands build
```

以上方法来自[【XMake】02 如何优雅的编写C++代码 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/655452448#:~:text=%E5%85%B6%E4%B8%ADC/C++%E6%8F%92%E4%BB%B6%E4%BD%BF%E5%BE%97vscode%E6%8B%A5%E6%9C%89%E4%BA%86%E8%B0%83%E8%AF%95C++%E4%BB%A3%E7%A0%81%E7%9A%84%E8%83%BD%E5%8A%9B%EF%BC%8Cclangd%E6%8F%90%E4%BE%9B%E4%BA%86%EF%BC%88%E7%9B%B8%E6%AF%94%E4%BA%8E%E5%BE%AE%E8%BD%AF%E7%9A%84IntelliSense%EF%BC%89%E6%9B%B4%E5%A5%BD%E7%9A%84%E4%BB%A3%E7%A0%81%E6%A3%80%E6%9F%A5%E5%92%8C%E8%A1%A5%E5%85%A8%E3%80%82%E5%AE%89%E8%A3%85clangd%E4%B9%8B%E5%90%8E%E4%BC%9A%E6%8F%90%E7%A4%BA%E6%98%AF%E5%90%A6%E7%A6%81%E7%94%A8IntelliSense%EF%BC%8C%E9%80%89%E6%8B%A9%E7%A6%81%E7%94%A8%E5%8D%B3%E5%8F%AF%E3%80%82) ，未确认，因为我的能正常生成。

## 附录：clangd

要用clangd记得在.vscode/settings.json下面

```json
{ "clangd.arguments": ["--compile-commands-dir=.vscode"] }
```

## 结尾

用xmake感觉就是能把很多东西都简化了，比如cmake的compile_commands在msvc不会生成，比如xmake的doc很清晰，只能说用xmake就完事了。
