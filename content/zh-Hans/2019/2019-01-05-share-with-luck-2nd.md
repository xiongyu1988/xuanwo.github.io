---
categories: Daily
date: 2019-01-05T05:00:00Z
tags:
- Linux
- Mac
- Archlinux
- Database
series: "Share with luck"
title: 随缘分享第 2 期
url: /2019/01/05/share-with-luck-2nd/
---

万万没有想到我也有一周双更的时候（，素材攒的太多了，赶紧分享一下~

<!--more-->

## 今天你翻车了吗？

### fwupd 翻车

说起来用 Arch 也有一段时间了，大的翻车没遇到过，小的翻车倒是经常遇到。1 月 2 号晚上睡前日常更新，发现更新了 fwupd 1.2.2 之后就顺手试了一下，结果发现命令行直接 timeout 了，想着可能是我升级之后还没有重启服务，然后 `systemd restart fwupd` 结果报错了，于是查了下日志：

```bash
-- Unit fwupd.service has begun starting up.
Jan 02 00:09:21 thinkpad-x1-carbon fwupd[7066]: Failed to get PCR0s: missing executable tpm2_pcrlist in PATH
Jan 02 00:09:21 thinkpad-x1-carbon fwupd[7066]: Failed to load engine: Failed to prepare SQL: no such column: protocol
Jan 02 00:09:21 thinkpad-x1-carbon systemd[1]: fwupd.service: Main process exited, code=exited, status=1/FAILURE
Jan 02 00:09:21 thinkpad-x1-carbon systemd[1]: fwupd.service: Failed with result 'exit-code'.
Jan 02 00:09:21 thinkpad-x1-carbon systemd[1]: Failed to start Firmware update daemon.
-- Subject: Unit fwupd.service has failed
```

emmmmm，`no such column: protocol` ？这看着是 DB 没做兼容啊，估计是上游翻车了，在 archcn 的群里问了一声没什么回应，于是先睡了，明天再说。

第二天早上看了下，已经有人在 arch 这边提了 BUG 了：[FS#61241 - fwupdmgr Failed to activate service](https://bugs.archlinux.org/task/61241?project=5&string=fwupd)，但是我感觉应该不是打包的问题，于是直接给上游提了个 BUG：[After upgrade to 1.2.2, fwupd failed to start for SQL error](https://github.com/hughsie/fwupd/issues/909)。作者 [hughsie](https://github.com/hughsie) 是红帽的员工，维护着不少知名的开源项目。

响应速度还算挺快的，考虑我们中间还隔着时差，按照他的要求提供了 `pending.db` 之后没多久就加了个修复的 [commit](https://github.com/hughsie/fwupd/commit/48106951c814f0bee201845fc1cc9cbb0f143faf)，自行打包了一个 fwupd 测试了一下，确认工作正常并回复了作者。历时大概三天左右，这个 BUG 被修复了。

> 说起来，现在嵌入式 DB 领域好像除了 SQLite 之外就没有一个能打的支持 SQL 的选手了啊？

### gitea 升级翻车

> 改配置已经很糟心了，这 gitea 甚至还要改运行的用户 = =，我觉得这样不行

一波升级之后，gitea 刷了一大波说明出来：

```bash
warning: /etc/gitea/app.ini installed as /etc/gitea/app.ini.pacnew

gitea now uses its own user/group instead of the git ones.
Ownership of /var/lib/gitea tree has been changed accordingly.
You need to update your /etc/gitea/app.ini file to change the
RUN_USER variable at the top.

Next step is fixing the SSH configuration. If you have:
    AllowUsers git
in /etc/ssh/sshd_config, you need to change that to gitea.
Then restart sshd.service.
You will need to tell all your users that they have to change
git@ to gitea@ in their repos clones using git remote set-url.

If you use a PostgreSQL DB upon Unix socket, you need to fix
your [database] configuration and switch USER to gitea.
Then, you need to change it also in /var/lib/data/pg_hba.conf.
Finally, as postgres user, run psql and inside it type:
    ALTER USER git RENAME TO gitea;
    \q
Then restart postgresql.service.

In all cases, you then need to reload systemd units and
restart gitea.service.
```

按照要求改了配置，重启了服务。服务是好了- -，但是所有 clone 的 git url 都要改了，有点麻烦。感觉这个变动就很坑了，作为一个后端服务，内部的配置改了就改了，也就是维护起来麻烦一点，但是对外（对用户）暴露的东西不能乱动。得亏我这个 gitea 就自己用用，要是真的有别人在用的话，还得群发个邮件让他们把本地的 remote URL 都改一遍，坑，gitea 好感度减 10。

### APFS 翻车

在一个风平浪静的早晨，惠老师突然在水群 Po 了一张图：

![](rm-failed.jpg)

这是啥操作，删除文件提示没空间？

![](open-terminal-failed.jpg)

Terminal 也打不开了，2333。

后来了解情况的群友解释了一下原因，其实这是两个问题：

第一个是采用 CoW 的文件系统固有的问题。我们都知道 CoW 的基本原理是在对数据进行修改的时候，不会直接在原来的数据位置上进行操作，而是重新找个位置修改。那么在磁盘已经完全满了之后就可能会出现问题，问题的起因有两种说法：第一是在删除的时候需要在一个空闲的位置写入一个新的标记，表示这个文件被删除了；第二是文件系统之前有 snapshot，被删除的文件还在被别的 snapshot 引用着，所以不能直接删除，需要加一个标记。所以类似机制的文件系统都可能会出现这样的问题，包括 ZFS 和 Btrfs。

第二个是 macOS 在磁盘系统全满的情况下的行为是不可预测的行为，没人知道会发生什么。所以会出现 Terminal 打不开，Finder 打开直接卡住这种事情。

最后惠老师进入恢复模式，执行了硬盘急救，最后多了一丢丢，大概 500MB 的空间，终于可以把文件删掉了（

## 无意义以及其存在的意义

![](conway-eeeee.png)

前一段时间疯狂刷屏的 [e98e](https://github.com/eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee/eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee) 在整个项目被 Archive 进入 read-only 之后热潮消退了。在最开始的时候我非常反感，感觉自己受到了精神污染，甚至想要举报一波，然而并没有找到合适的理由。现在回过头来想一想这个事情，感觉心情非常复杂，总是会去想是不是自己做错了什么，或者说当我在一个公共场合（比如 Telegram 的群中）轻蔑的说某样事情 **无聊**，**没意义** 的时候，我是不是真的明白我在说什么。

这个问题我琢磨了很久，最后感觉它是由一组复杂的问题组成的：

- 对一个事务的意义判断究竟是谁来做的？
- 无意义的事务有没有存在的意义？
- ......

不知道是不是受到教育环境的影响，我总是会倾向于去做 `有意义` 的事情。那 `有意义` 又是意味着什么呢，大多数时候它要 `有价值`，而更大多数时候可以概括为 `有用`：

- 玩游戏，就不能浪费时间，玩 1 分钟就要获得 1 分钟的快乐
- 做开源项目就得要出名，要有 Star，要有人关注，要有人用在生产环境上
- ......

所以说，世界上到底有没有没意义的事情的呢？可能只存在**我认为**没意义的事情吧。

这些问题我可能是想不清楚了，只能一直这么纠结下去，以后遇到所谓的没意义的东西，我大概会选择保持沉默吧：“我觉得你做的事情没意义，但是我尊重你做下去的权利”。

## 有啥有意思的东西嘛？

2018 年最后入手的东西是 OnePlus 6T 和一把宜家的转椅。

这么多年过去了，安卓的变化比我想象的还要大，特别是在一加 Oxygen OS 的支持下，感觉到了前所未有的舒畅。虽然国内的生态一如既往的糟糕，但是大多数应用都能用，某些实在流氓或者 Google Play 中没有的应用也直接扔到了 Work Profile 里面。现在基本上是晚上充满电上床看小说，然后第二天直接带去上班，到晚上回家还能有 40% ~ 50% 的电量，已经完全够用了，And 快充也非常给力。用了几个星期，完全不怀念我的 iPhone。

之前用的转椅是前同事留在房子里面的，一直凑或用，但是有一天实在忍不了了，决定换一把新的转椅。在朋友 P 的安利下入了宜家的 [MARKUS 马库斯](https://www.ikea.cn/cn/zh/catalog/products/50137208/) 转椅，深灰色那款只要 999 元，加上运费总共 1068 元。花了点时间拼了起来，一坐上去整个人就不想起来了，实在是太舒服了。腰部和头部还有手臂都被正好支撑起来，终于不会写着代码感觉自己整个脖子都僵住了。

这个椅子支持上下大概 10 cm，向后 30 度左右的调节。配合自己之前买的显示器支架，可以舒舒服服的躺着看动漫，打游戏，生活美滋滋。如果有想换椅子的同学无比留意下~

最近还看到一个好玩的 issue： https://github.com/antirez/redis/issues/3909 ，一个人不小心发了个 issue，然后解释道：“Cat walked over the keyboard sorry guys.”，引发了一场评论区的狂欢。我觉得最好玩的回复是项目的作者回复的：

> Observing the keys pressed we can assume the cat was diagonally exploring the keyboard, probably with the front paws oriented towards the "/=-[" part of the keyboard (right/bottom) since there is more variability there, compared to the (maybe) rear paw staying near the number "3" (and the other rear paw probably outside the keyboard, in the upper part). However the size of the cat should be very small in that case, or the size of the keyboard should be very large, if we are not willing to assume a reduced-length cat because of attack position or alike.
>
> Btw if you could report the `INFO` output of your Redis instance, we may be able to perform more educated guesses about the conditions of your cat.

集段子与安利于一身，是值得我学习的楷模~

## 最近又看了啥项目？

首先简单介绍一下这个 `好玩` 和 `有用`：

`好玩`：是说 **我** 觉得这个项目很好玩，有意思
`有用`：是说 **我** 觉得这个项目能派上用场

如你所见，只有三个组合，因为又不好玩又没用的项目写它干啥呢？

### 好玩又有用

#### Vegeta

https://github.com/tsenart/vegeta

打开这个项目的时候被正面冲过来的贝吉塔吓了一跳，当时就记住了它，然后才反应过来项目名字就是用的贝吉塔的英文：Vegeta。大概寓意着这个项目跟超级塞亚人一样强悍吧，哈哈。

这是一个用来做 HTTP 负载测试的库和工具，既可以在代码中调用，也可以直接作为工具使用。项目维护的还不错，以后会考虑用它。

#### NATS

https://nats.io/

NATS 是一个云原生消息系统，是一个 CNCF 项目。它的消息格式很有意思，是一个类似于 Redis 的 text based protocol，我个人很喜欢，简单才是美啊。大概也得益于这么简单的 Protocol 设计，它的吞吐量很大，按照项目给出的评测，它的吞吐量是 Kafka 的两倍，RabbitMQ 的十倍。

项目的设计目标给的非常清晰：

- Highly performant (fast)
- Always on and available (dial tone)
- Extremely lightweight (small footprint)
- Support for multiple qualities of service (including guaranteed “at-least-once” delivery with NATS Streaming)
- Support for various messaging models and use cases (flexible)

如果有合适的场景，我大概会考虑玩一玩~

#### uncaptcha2

https://github.com/ecthros/uncaptcha2

这是一个今天才看到的段子，思路很有意思，就是输出 Google reCAPTCHA 的语音验证码，然后用 Google 的语音转文字 API 来做识别，然后再填回去，据说识别率还非常不错。

哈哈哈，这个真的是现实版的以子之矛，攻子之盾了。

#### badger

https://github.com/dgraph-io/badger

这是一个很早之前看过的项目了，但是有两个比较有意思的事情：

一个是有奖征集数据丢失的场景：[Prove that Badger loses data](https://github.com/dgraph-io/badger/issues/601)，只要你能够按照要求给出可复现数据丢失的步骤，就能获得 $1337 。感兴趣的童鞋可以去试试。

另一个是 badger 增加了一组 [Jepsen-style bank test](https://github.com/dgraph-io/badger/pull/577)，会在他们内部的 CI 平台上运行，可以看[现场直播](https://teamcity.dgraph.io/viewType.html?buildTypeId=Badger_BankTest)。[Jepsen](https://jepsen.io/) 我是在看 PingCAP 的相关技术分享时候看到的，Jepsen 是一个分布式系统验证的框架，已经成功发现了很多系统的 BUG。

之前有个项目技术选型的时候没用 Badger，之后大概可以考虑一下。

BTW，他们的项目管理似乎有些问题，依赖管理没做，完全靠 go get，Makefile 也没有，新人想要参与开发感觉是一脸懵逼。Windows 平台上的 CI 一直都是挂的，我还提了个 PR 修了其中的一个：[test: Fix TestDropReadOnly failed on windows](https://github.com/dgraph-io/badger/pull/661)，在我这个 PR 之后他们又 Fix 了不少 windows 上的测试，估计再过一段时间就能恢复正常了。他们是怎么忍下去的- -，我完全接受不了一个项目 Master 分支上的 CI 都是挂的，那还不如去掉呢。。。

### 有用不好玩

#### gradle

Adaptable, fast automation for all https://gradle.org

以前只是听做 Java 开发同事天天吐槽 gradle，以为只是 Java 生态下的一个工具，定位类似于 npm for node.js，但是今天看了项目才知道原来它的目标是构建自动化并且要支持多语言的开发。

这个自动化跟我想要的不太一样。

#### cloudevents

https://cloudevents.io/

有一个定义云消息的规范，还是基于 JSON 的，看起来大概是这个样子：

```json
{
    "specversion" : "0.2",
    "type" : "com.github.pull.create",
    "source" : "https://github.com/cloudevents/spec/pull/123",
    "id" : "A234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleextension2" : {
        "othervalue": 5
    },
    "contenttype" : "text/xml",
    "data" : "<much wow=\"xml\"/>"
}
```

无爱。

### 好玩但没用

#### lsix

https://github.com/hackerb9/lsix

一个为图片设计的 ls，emmm，感觉没有啥特别的用处，虽然确实挺好玩的就是了。

#### ungoogled-chromium

https://github.com/Eloston/ungoogled-chromium

清真版本的 Chromium，去掉了 Google 相关的代码，据说非常清真，但是我是 Google 的粉丝，甚至电脑上用的就是 Chrome - -，因此就是围观一下。

#### citybound

https://github.com/citybound/citybound

一个开源版本的城市模拟，感觉还比较早起，只有一些雏形，还玩不起来，还需要再观察观察。引擎本身是用 rust 写的，前端很有意思，用的是一个叫做 [webflow](https://webflow.com/) 产品，交互比较酷炫。可能我这样的前端白痴也有救了？

## 说点坑爹事情大家开心开心

偶然发现Chrome 砍掉了 `chrome://net-internals/#events` 这个功能。

**坑爹啊！**

> The net-internals events viewer and related functionality has been removed. Please use chrome://net-export to save netlogs and the external catapult netlog_viewer to view them.

现在想要查看 Chrome 的网络交互细节只能打开 `chrome://net-export` 点击开始导出，操作完了再结束导出，最后用一个外部工具导入再查看了。

Hugging you, Google!

---

以上就是本期随缘分享的全部内容~

