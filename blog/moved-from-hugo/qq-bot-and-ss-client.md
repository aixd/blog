+++
title = 'QQ Bot & SS Client'
date = 2024-09-09T04:26:08+08:00
draft = false
+++

试着搭了个QQ Bot玩，不过因为服务器快到期了，直接在本地跑的，也就没配什么东西，稍微试着连了个bot，然后连上了SS的客户端（这里提问也是非常的没有遵守[提问的智慧](https://github.com/ryanhanwu/How-To-Ask-Questions-The-Smart-Way/blob/main/README-zh_CN.md)和[别像弱智一样提问](https://github.com/tangx/Stop-Ask-Questions-The-Stupid-Ways/blob/master/README.md)，下次一定在提问前就准备好相关信息什么的，不要浪费别人的时间）

## NapCat

[NapNeko/NapCatQQ: 现代化的基于 NTQQ 的 Bot 协议端实现 (github.com)](https://github.com/NapNeko/NapCatQQ)

这部分没什么好说的，作者非常贴心的做了一键脚本，到调配置前都没什么问题。

遵循[botuniverse/onebot-11: OneBot 11 标准 (github.com)](https://github.com/botuniverse/onebot-11)

### 配置

连的bot用的是反向ws，ss客户端用的是正向ws。本地的话正向ws的host就不用写，不知道开在服务器上的话要不要写

## 连接bot

只是测试一下，所以在github上随便找了个bot，用的是[syuchua/QFurina: 一个简单、现代、高性能的QQ机器人后端框架，支持AI对话、绘图、语音、联网等功能 (github.com)](https://github.com/syuchua/QFurina)

连接过程完全没有阻碍，直接过了

## SS的客户端

[Stapxs/Stapxs-QQ-Lite-2.0: 一个兼容 OneBot 协议的非官方网页版 QQ 客户端、electron 客户端。 (github.com)](https://github.com/Stapxs/Stapxs-QQ-Lite-2.0)

连客户端连到玉玉了，一开始以为是access key没填的问题，当然其实不是。

看1.0[Stapxs/Stapxs-QQ-Lite: 一个兼容 oicq-http 的非官方网页版 QQ 客户端 (github.com)](https://github.com/Stapxs/Stapxs-QQ-Lite)的样例填的 `localhost:5700`，但是在2.0我这样输入却连不上了。随后我去询问了一下SS，输入 `ws://localhost:5700`即可连接了。

后来又试了一下，`localhost:5700`在首次尝试居然也能连接，这个真的让我难以理解，于是我看了一下逻辑，直接把代码贴上来吧，后附我的理解。

```javascript
/**
 * 创建 Websocket 连接
 * @param address 地址
 * @param token 密钥
 */
static create(address: string, token?: string, wss: boolean | undefined = undefined) {
    //...

    // Electron 默认使用后端连接模式
    if(runtimeData.tags.isElectron) {
        logger.add(LogType.WS, $t('log_con_backend'))
        const reader = runtimeData.reader
        if(reader) {
            reader.send('onebot:connect', { address: address, token: token })
            return
        }
    }

    // PS：只有在未设定 wss 类型的情况下才认为是首次连接
    if(wss == undefined) retry = 0; else retry ++
    // 最多自动重试连接五次
    if(retry > 5) return

    let url = `ws://${address}?access_token=${token}`
    if(address.startsWith('ws://') || address.startsWith('wss://')) {
        url = `${address}?access_token=${token}`
    } else {
        if(wss == undefined) {
            // 判断连接类型
            if(document.location.protocol == 'https:') {
                // 判断连接 URL 的协议，https 优先尝试 wss
                runtimeData.tags.connectSsl = true
                url = `wss://${address}?access_token=${token}`
            }
        } else {
            if(wss) {
                url = `wss://${address}?access_token=${token}`
            }
        }
    }
  //...
}
```

以下是我也不知道是否正确的理解：每次点击连接会创建一个reader，而点击后的首次创建连接过程会消耗掉这个reader，因此在首次点击连接后，后续的重连尝试就会运行PS之后的代码，从而修改url为 `ws://${address}?access_token=${token}`。但如果不是首次点击连接，那么在PS之后的代码就不会被运行，url也不会被修改，导致没写协议的样例在首次点击连接的情况外无法正确连接。

顺便，其实 ``ws://0.0.0.0:5700``和 ``ws://127.0.0.1:5700``都是能正常连接的，只是不知道是我就是刚好没试到这几个，还是端口被占用之类的神秘问题，总之在问SS之前就是没能成功连接，最后还是要麻烦ss真是不好意思……

写完看了一下，差不多花了两个小时，希望下次写博客能更快……（还有看源码也是）
