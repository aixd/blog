+++
title = 'Games101 Appendix'
date = 2024-10-13T17:30:39+08:00
draft = false
+++

### 1

orthographic projection 这块讲的有点急，一开始没说这个立方体其实是场景或者物体切片（clipping planes），也就是把我们想要渲染的 3D 模型卡在这样一个立方体内，然后将其正规化到【-1， 1】中，然后舍弃 z 维度就是正交投影了。
因为自己这块听的比较模糊，而且看弹幕也有不少对这块不清楚的，故作此评论。内容参考<https://en.wikipedia.org/wiki/Orthographic_projection>

### 2

<https://pbr-book.org/4ed/contents>
《基于物理学的渲染》的“圣经”，优秀到麻省理工大学专门出钱买了版权搞了免费在线版。
网上的在线版是完全免费的，但是纸制书要一百多刀。
这本书有一个配套的渲染系统 PBRT(Physically Based Rendering Tutorial)，完美理论加实践。
如果 github 下载太慢了，PBRT 在 GITEE 上有很多备份。

### 3

给入门的同学，同时推荐另一本 Computer Grahics from Scratch（外网有英文原版 pdf）, 比国内其他翻译或编著的计算机图形学入门书籍要好很多，讲得很实在容易理解

### 4

突然对《shader入门精要》里的逐顶点光照和逐像素光照恍然大悟

### 5

shader toy

ray marching

3s逼近
