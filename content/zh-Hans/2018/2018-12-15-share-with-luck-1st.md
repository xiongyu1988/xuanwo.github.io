---
categories: Daily
date: 2018-12-15T06:05:00Z
tags:
- Workflowy
- Enpass
series: "Share with luck"
title: 随缘分享第 1 期
url: /2018/12/15/share-with-luck-1st/
---

每周分享实在做不到，但是随缘分享还是可以的，内容大体上会跟之前的动态有些交叉~

<!--more-->

## Workflowy 复活

Workflowy 自从有一个 PM 和设计师之后，感觉整个产品复活了。前端使用 React + TypeScript 彻底重写，有了全新的设计，清理掉了一大堆旧代码，甚至开始提供 [Extension API](https://workflowy.com/s/workflowy-extension-api/6ziauXitmUj7idN2) 了！

读着他们的博文 [New Year, New Code](https://blog.workflowy.com/2018/12/11/new-year-new-code/)，我感到非常激动且开心。激动于一些期待已久的功能终于可以实现，开心于一个快十年的老产品还能够这样焕发生机。

Android 和 iOS 的 App 也都重写了，[每日推送的摘要](https://blog.workflowy.com/2018/12/14/better-daily-digests-a-splash-of-color-in-your-inbox/) 也支持了类似于 git diff 一样的效果，欢迎各位用过或者没有用过 Workflowy 的童鞋来体验一下，有余力有需求的话也可以买个 WorkFlowy Pro 以最实际的方式支持开发者~

## Enpass 6 体验

最近正好有人请求打包 enpass-beta-bin，于是我就把这个包加到了 archcn 源里面，并将自己本地和 iOS 上的 enpass 都升级了一下。

新的版本最大的变化就是支持多个 Vault：每个 Vault 彼此之间完全独立，可以设置不一样的 Master Password。Vault 的同步也是分开的，以 Google Drive 为例，一个 Google 帐号只能同步一个 Vault。同步的实现机制也有变化，原来是单纯的把加密之后的文件同步到一个文件夹下面，现在开始使用各个网盘提供的 App Folders 功能，现在用户以及其他应用已经不能直接访问到同步的文件。可能会有些不太方便，但是更加安全，我还是比较欣赏这个改动的。

功能上还有的变化是支持自定义类型和模板，这是用户一直想要的功能，原来受限于软件架构问题无法实现，现在在重写之后的 Enpass 6 里终于有了。Enpass 6 还将只有浏览器有的登录助手带到了桌面上，现在点击图标默认会出现这个助手而不是完整的应用界面。这个改动我不太喜欢，背后的逻辑可能是认为用户的查询操作更加频繁，因此需要一个精简版的入口，但是从我的实际体验上来看，我打开 Enpass 就是想要增加或修改条目，查询基本上只会在浏览器端登录的时候做。

对浏览器的支持也重新实现了，现在浏览器插件和主程序之间首次通信会进行一次认证，大体上类似于蓝牙链接一样，主程序这边要生成一个随机的 6 位数字，浏览器插件需要输入一样的数字之后才能连接。这大大提升了浏览器插件这一端的安全性：原有的实现依赖于校验浏览器本身是否可信，在 Linux 上这个功能完全不可用，现在的这种方式可以防止奇怪的浏览器或者插件访问我们敏感的数据了~

除了功能之外的大变化就是 UI 和交互了，整体上变得更加时髦了，虽然现在用起来感觉不是非常好用 - -!，相信后续还是会不断改善。最起码现在我用 1password 的朋友们看了新界面之后会说一句不丑，比原来的好多了（。

老毛病还是有：对 CJK 字符的支持没有，应该是没有带上 fcitx 的 qt 插件，这个已经跟上游反馈了，或许下次（?）更新就会修好吧。然后因为是 Beta 版本，所以体验上还不是非常好，很多细节的地方都打磨的不太精致。

此外在 Archlinux 上配置同步的话可能会遇到一些问题：配置的时候会打开对应网盘的登录界面，进行一次标准的 OAuth2 认证流程，然后会访问一个 `enpassauth://` 的链接，但是此时 `xdg-open` 会打开一个全新的 enpass 实例，导致之前的那个 enpass 没有办法收到这个回调。

跟 Enpass 反馈之后他们表示这是一个实现上的问题，之后会予以修复。一个可行的 workaround 是在命令行使用 `xdg-open enpassauth://` 打开 enpass 并配置同步，此时 enpass 可以正确的响应这个回调。

Enpass 6 目前还处在 Beta 的阶段，感兴趣的童鞋可以体验一下，在 Archlinux 上使用有问题的话可以反馈给我~

> 安装 Enpass 6 第一次启动会自动升级原有的数据，此升级不可逆，如果还想回去的话，记得备份好数据。

- 用 Arch 的同学可以直接安装： `pacman -S enpass-beta-bin`
- 用 iOS 的同学可以通过 TestFlight 来直接参与测试：<https://testflight.apple.com/join/hIec0gli>
- 还没有用过的同学可以看看这篇[安利文](https://xuanwo.io/2017/11/26/enpass-intro/) 了解一下

## Game

- 最近想完成全弓箭收集的成就，大概保持着每天做半把的节奏，现在做到了尸套龙弓（然后发现自己没钱了），尸套龙还是比较好打，带上龙封力的武器，出门带上闪光弹，路上拣一点松明弹和可燃石基本上没啥问题，只要注意不要被秒
- 有天晚上怎么都连不上集会所，于是玩了一会儿会免的重力异想世界，头有点晕 - -

## Travel

- 下周要去日本了，第一次出国，有点紧张

## Miscellaneous

- 现在大家好像都在倡导搁置 code style 的争议，gofmt 和 rustfmt 都是已经成为了各自社区的主流，js 这边也有一个类似的项目： <https://prettier.io/>
- 最近学到一个新词：`event storming`，好像一般翻译成事件风暴，用于动态业务流程的分析，<https://www.eventstorming.com/>
- [OpenMessaging](http://openmessaging.cloud/)，名字非常酷炫，是一个阿里牵头搞的云原生，厂商独立的分布式消息规范，schema 我不是非常喜欢，这么搞还不如发 HTTP/2 的包呢
- 隐私在当下成为了一个越来越重要的话题，这里 <https://ssd.eff.org/en> 给出了一些保护自己隐私的方案和实践
- [loki](https://github.com/grafana/loki) 是 grafana 推出的一个类似于 Prometheus 的日志收集工具，主打的特性是简单便宜，支持从 Prometheus 无缝迁移，提供了对 k8s pod 日志的良好支持，会自动抓取和索引相关的标签，grafana 中会提供原生的支持
- [starlark](https://github.com/bazelbuild/starlark) 是一个形似于 Python 的配置语言，而 [starlark-go](https://github.com/google/starlark-go) 是由 Google 的工作人员推出的 go 实现，用于他们自己的编译工具 bazel
