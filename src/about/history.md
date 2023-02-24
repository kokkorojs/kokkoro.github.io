# 黑历史

::: info
也不知道为什么，只是想讲给你们听听，篇幅可能比较长，文笔也比较差  
和项目文档没什么关系，如果想打发时间...可以来看看，不知道算不算是黑历史 233
:::

当我第一次想着要写机器人框架的时候，其实我是拒绝的。因为我觉得，你不能叫我写我马上就写，第一我要试一下，我又不想说一个框架写完以后用上好多轮子，那个代码 duang，start 很多，很亮，很闪，结果出来用户一定会骂我，根本没有这种代码，证明上面 star 是假的。

## 机器人的由来

最开始的想法其实很单纯，因为 pcr 认识了许多有趣的人，用 [酷Q](https://cqp.cc) 随便做了一个扭蛋十连、排名播报和 wiki 查询的功能，想着活跃下群里的气氛，也能给大家带来便利，却没想到好评如潮 ww

后来发生的事情大家也都知道，酷Q 跑路 ~~(日你妈退钱，我上个月刚续的一年)~~ ，导致 bot 无法正常使用，当时 bot 在群里使用需求还是比较大的，所以就萌生出了更换框架的想法。

## 框架选型

当时主流的 pcrbot 框架只有 [yobot](https://github.com/yuudi/yobot)、[hoshino](https://github.com/Ice-Cirno/HoshinoBot) 以及 [kkl](https://github.com/Joenothing-lst/kkl-Android-2.0) 这三者，还没有出现 xcw 等各种缝合的作品。正好自己又会编程，所以我没有去租用，将目光放到了 kkl 身上，什么？你问我为什么会选择后者？~~(我才不是萝莉控，那可是我妈！)~~

项目部署的过程不算特别复杂，只是比较繁琐，在搭建好 Python 环境和 CQHTTP 后，我发现... kkl 的作者已经不维护了，文档无法访问，作者个人博客都打不开了（摔

当时留给我的时间已经不多了，bot 也已经停用了好一段时间，我临时选择了 Mirai + 酷Q Native 的方案运行原生插件。

## 小插曲

虽然使用 mirai 暂时解决了问题，但也只能保持基本的可用，若要保持长久的可维护性比较困难，特别是会战相关功能，就在这时，[hoshino_xcw](https://github.com/pcrbot/hoshino_xcw) 发布了。

她整合和 hoshino 与 yobot 的各类功能，弥补了双方的短板，更重要的是，还提供了一键包 [xcwbot-installer](https://github.com/pcrbot/xcwbot-installer)，能完全靠脚本自动化部署，避免了许许多多的坑，为我提供了极大的便利。

后来...因为一键包门槛极低，有服务器就能几分钟部署好 bot，有些人利用一键包出租盈利，还跑到作者群里骑脸嘲讽，作者删库了，一键包也完成了它的历史使命。

## 梦的开始

在后来的日子里，也不知道怎么去形容，感觉自己一直都是处于一种很神奇的，白天上班，晚上学 Python，xcw 是基于 nonebot 开发，群友包括我自己想添加一些新功能或者修复 bug，都是直接在服务器上修改源码，有一种白天上班晚上继续上班的错觉（

咱一个小前端能有啥坏心思呢，py 开发插件对于我来说效率还是不及 js，所以在一时兴起下，[yumemi](https://github.com/dcyuki/yumemi_bot) 就此诞生惹（啪叽啪叽）

「まるで、夢みたい」，这是我接触 pcr 以来最大的感触，虽然有过争吵和一些不愉快，曾经的自己也只是一个副会，但后来会长跑路将公会托付于我，虽然比较累，但是和大家的日常，每天都很开心，我从未有过后悔。但是...好想再和会长一起，打一次狂暴牛啊。

## 为什么不是...

既然选择了 js 作为主要开发语言，为什么要去造轮子？

当时我翻遍了 Github 所有的仓库，一点都不夸张，就是所有仓库，也全部都用过。js/ts 的 bot 框架只有 [el-bot](https://github.com/YunYouJun/el-bot) 和 [koishi](https://github.com/koishijs/koishi) 这两个。

最开始其实我是基于 koishi 去开发插件的，虽然文档不是特别完善，但实现基础的消息收发还是没什么难度。

本来做了一个简单的十连扭蛋插件，结果某次更新后本地连项目都跑不起来了，当时在 koishi 开发群问了下，管理的原话是 “因为文档不完善，现在都是面向群主编程，群里最起码有一半人现在都跑不起来项目” ~~(梦梦直呼我太菜了)~~

所以我把目光放到了 el-bot 身上，但因为文档同样不完善，而 el-bot 又是基于 [mirai-ts](https://github.com/YunYouJun/mirai-ts) 开发的，所以我就选择了使用 mirai-ts。

~~经历了这么多，但凡有一个失误可能就没这个项目了~~

## 架构探索

这里介绍了 kokkoro 现在的框架架构是如何一点点演变的，如果你想要自研框架，我的设计思路也许能给你带来一点小小的帮助。

### yumemi v0.1 ~ v0.5

在 yumemi 初期，所有插件指令都是在根目录下的 `command.yml` 文件配置，格式为 `[插件名]: [指令名]: [匹配规则]`，其中自定义规则支持正则与字符串

```yaml
test:
  print: ^(打印|输出)\s?.+$
```

#### 插件示例

在项目启动后，会监听 `bot` 下的 `message` 事件，若有与 `command` 规则相匹配的消息就会将 `ctx`、`bot` 直接传入该插件内部，而插件需要默认导出一个函数

```javascript
// plugins/test/index.js
module.exports = (ctx, bot) => {
  // 例如收到了 "打印 测试语句" 消息，这里的 command 就是 "print"
  const { command, raw_message } = ctx;

  switch (command) {
    case 'print':
      const message = raw_message.replace(/打印|输出/, '').trim();

      ctx.reply(message);
      break;
  }
}
```

#### 设计初衷

因为 yumemi 最初是为了满足自用，而且也没有开源，所以在设计上并未考虑后续的扩展性，所有插件直接存放在仓库 `plugins` 文件夹内，没有版本管理的概念。直接将 `command.yml` 抽离出来的好处是任何插件指令可以随时修改，方便自定义。

#### 问题

如果是作为个人使用那么该设计模式是极其便利的，因为所有指令规则都是在同一个配置文件内管理，插件随改随更新，没有过多地约束，自由度高，是完全满足自用的。

后续因为有开源的打算，所以这种模式行不通，首先开源后插件就需要有版本管理的概念，会上传至 `npm` 平台，这样一来 `command.yml` 就需要手动更新，用户层面来讲步骤极其繁琐。

其次是为了省去编译的步骤，项目全部使用 js 进行开发，从第三方的角度来说开发体验较差，也不利于后续的维护。

### yumemi v0.6

后来在参考了 [abot](https://github.com/takayama-lily/abot) 项目后我使用 ts 重构了 yumemi，此前插件是完全基于 message 消息驱动的，现在则是完全基于事件驱动，支持自定义事件监听处理。

#### 插件示例

那个时候的 yumemi 与 abot 在插件机制上极其相似，不同的是 yumemi 有着一套插件权限管理以及指令机制，还优化了登录流程和 bot 多账号管理。

```typescript
function listener(this: Bot, ctx) {
  command(ctx, () => {
    ctx.reply('hello world');
  });
}

function enable(bot) {
  bot.on('message.private', listener);
}

function disable(bot) {
  bot.off('message.private', listener);
}

export {
  enable, disable,
}
```

#### 设计初衷

为什么要参考 abot？

因为此前 bot 通信使用的是 mirai-ts，为了部署一个 bot 服务还需要启动 mirai 服务，过程相当繁琐。就在这时 oicq 发布了，一个完全基于 node 环境的协议库，不用安装任何第三方应用。在此十分感谢 [takayama-lily](https://github.com/takayama-lily)，没有这个项目也就不会有现在的 kokkoro。

而 abot 也是那个时候的产物，同样出自高山之手，为了能更好的契合 oicq，所以我阅读了相关源码。除了 abot 以外，这 3 年时间我应该算是在闭门造车，没有参考过任何其它项目，这也许不是什么好习惯。

#### 问题

虽然基于事件驱动让插件开发有了更多的可能性，但是有一个非常致命的问题，消息匹配机制。

在 yumemi 早期，可以根据 `command.yml` 来区分用户想要执行哪段指令，这一点在 v0.6 也得到了继承，引入 `command` 函数后传入回调就可以执行相对应的指令。

可这样并不算特别优雅，指令集应该是在 `plugin` 中定义，而不是在配置文件里，每个用户的配置文件都不一样，安装的插件也不一样，无法做到统一。

其次是事件需要手动 `off`，对于开发而言，重复写一套只是方法名不一样的函数，并不方便。发布 v0.6 的时候 yumemi 已经开源了，但是因为插件都是高度耦合的，所以没有使用 package 管理。想要更新包就只能 pull 整个 git 仓库，于是就产生了重构的打算。

### kokkoro v0.1

::: warning TODO
不定期更新
:::
