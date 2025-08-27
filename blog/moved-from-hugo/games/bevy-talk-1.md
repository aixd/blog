+++
title = 'Bevy Talk 1'
date = 2024-09-30T01:19:43+08:00
draft = false
+++

Time to forget siv3d，主要是它和clangd的相性似乎真的不怎么样，如果有的选我还是宁愿raylib了。但是C明显还是太古早了，说到新潮，作为新生代的 Rust 和它的 Bevy 就来了。

走教程发现运行一直报错 `Failed to open JSON` ，从隔壁Godot的issue里找到了解法[Vulkan：由于Vulkan ICD文件无效，打印了虚假的“无法打开JSON文件”·问题#56089 · godotengine/godot --- Vulkan: Spurious &#34;Failed to open JSON file&#34; printed due to invalid Vulkan ICD files · Issue #56089 · godotengine/godot (github.com)](https://github.com/godotengine/godot/issues/56089)

删除以下注册表里的所有导致报错的json文件就完事了。

```
\HKEY_LOCAL_MACHINE\SOFTWARE\Khronos\Vulkan\ExplicitLayers\
HKEY_LOCAL_MACHINE\SOFTWARE\Khronos\Vulkan\ImplicitLayers\
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Khronos\Vulkan\ExplicitLayers\
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Khronos\Vulkan\ImplicitLayers\
HKEY_CURRENT_USER\SOFTWARE\Khronos\
```
