# DRAGONRICHI的所有功能

## 语言-Language
[English](richiall_en.md)
[中文](richiall_cn.md)

## 写在前面
如果您发现有遗漏的功能，请联系作者添加(毕竟功能真的太多了xD)
如果您对其中的功能感到疑惑，可以到最下方相关链接的DRAGONRICHI玩法去感受玩家是怎样体验服务器的，或者DRAGONRICHI(Admin)去感受用DragonRichi制作游戏的方便。

## 对战
1.Duel(1v1)，2 v 2，1 v N，Party vs Party，Party FFA, NvNvNvN, N Teams。可在地图编辑中对每个队伍设置进行单独调整。
2.Unranked，Ranked，FFA，以及可以通过自定义GUI将其混杂在一起，也可以通过自定义GUI把不同类型的游戏分开。
3.残血红色边界，在玩家残血的时候显示出一个靠近边界时的效果，只有在1.8以上的服务端(和客户端)才能看到。通过编辑config.yml可以开关该功能和调整阈值。
4.三局两胜、五局三胜(适用于节奏快、偶然性强的模式)、标准模式(一般情况或者节奏很慢的模式)
5.末影珍珠冷却，可在config调整冷却时间。
6.可限制游戏场数上限，拥有某自定义权限可无视(VIP)。其中，单挑时只要一方有权限即可。
7.MiniGames玩法自定义。可以制作Skywars、Bedwars、Horse、Spleef，甚至原创您自己的游戏玩法！
8.FFA，见下方FFA

## 其他模式
1.令专业PVP玩家沉迷的修炼模式(可用于练习落地水，搭路等)
2.观战，按观战人数从少到多排列(热度排行？)
3.利用自定义MiniGames和计划任务+活动去制作属于您的游戏模式和活动。

## 竞技场项目设置
1.**全GUI编辑**！除了一条指令用于创建/打开项目。
2.全GUI编辑装备和默认药水效果。
防未保存关闭，避免编辑到一半不小心关闭导致的进度丢失。
快速附魔和自定义药水，在编辑状态下鼠标对着需要自定义附魔或自定义药水的物品按下Shift+右键即可，无需退出编辑状态或输入指令。
3.可更改显示名称。
4.可更改伤害间隔，以制作Combo模式。
5.可更改有效地图列表，玩家加入游戏后会随机选择有效地图列表中的一个。详见**地图系统**
6.可更改是否自然回血(饱食度充足时消耗饱食度回血)，以制作UHC类游戏。
7.可更改建筑权限，以制作BuildUHC、Skywars等需要建筑、破坏方块的游戏。可选择是否只能破坏玩家放置的方块(防地图本身被破坏)。
8.可更改最高血量。
9.可更改击退，由Moci原创的击退系统，拥有11个参数供您调教，详情请见DragonRichi(Admin)。您可以选择我们帮您调教好的击退预设，以快速配置您的模式。您也可以自己保存预设以供以后使用。
10.备份或复制。您可以将您的模式复制一份出来单独作修改或当作备份。
11.细节设置。巨量细节设置选项，为您的自定义游戏画龙点睛。由于细节设置内容较多，详情请见DragonRichi(Admin)。
12.队伍设置。支持Duel(1v1)，2 v 2，1 v N，Party vs Party，Party FFA, NvNvNvN, N Teams。其中NvNvNvN, N Teams您可以设置队伍最多玩家数量限制、队伍数量限制等。
13.可自定义不同模式中的计分板。计分板可以是动态的，脱离传统计分板插件无法在不同情况下显示不同内容的问题。
这里的动态不是指计分板上某一行的内容可变，而是行数都可以变化，在不同模式不同情况下显示不同计分板，如丢末影珍珠后插入一行末影珍珠CD时间。详情请见DragonRichi(Admin)。
<font style="color: red">14.**玩法设置。您可以通过该选项制作Skywars、BedWars、决一死战、战墙等游戏。事实上，只要您脑洞够大，可以用该选项做出您自己都没有见过的属于您的原创游戏。通过其他自定义选项，您甚至可以将这个竞技场插件改造成小游戏插件，让人看不出这是由DragonRichi制作的自定义游戏。详情请见DragonRichi(Admin)**</font>

## 地图系统
1.您是否曾经在使用其他竞技场插件时，出现一场游戏过后游戏场地恢复失败导致的满目疮痍，或者场地不够造成两次游戏在同个场地进行的场地冲突的情景？DragonRichi的游戏场地采用独立地图模式，从原理上杜绝了此类现象的发生。
2.预生成世界。牺牲内存以使世界瞬间生成，默认关闭(如果地图和玩家较少可以在config中开启此项以提高体验)
3.可编辑世界图标，以更好地分辨不同的世界。
4.可设置哪些地图是自定义模式可以使用的。
5.配合玩法设置以制作属于您自己的原创游戏。

## FFA
1.**全GUI的编辑**！除了一条指令*/ffaadmin*用于打开GUI。
2.和竞技场项目一样的全GUI装备编辑，防未保存关闭，药水效果以及shift+右键自定义附魔或自定义药水。
3.可创建无数个出生点，并通过/guiadmin edit ffa以自定义GUI顺序和在自定义GUI中绑定至点击事件。
4.可创建无数个自定义保护区域，通过点击选择两点并用粒子效果圈出区域，一目了然。玩家在保护区域内无法攻击玩家和被玩家攻击，当进入战斗状态时无法进入保护区(会被弹开)，并可在计分板上显示战斗状态的时间，可在config中自定义战斗状态的时间。在Lobby中的玩家走出保护区域会自动进入FFA模式(可在config中开关)。
5.可创建无数个自定义补给箱，GUI支持(1~6)*9。
6.可创建无数个可建筑区域，区域内的补给箱不会被破坏。
7.所有区域可相交。
8.自定义掉落物品限制(如防丢剑)
9.丢弃烦人的玻璃瓶时自动移除
10.自定义默认药水效果、KB。

## 自定义模式
1.超级强大的玩家自定义游戏。同*竞技场项目设置*一样，玩家可以在全GUI的编辑界面中非常迅速地编辑自己的自定义游戏，可自定义的功能和上文所提到的差不多。
2.玩家可以创建多个自定义模式，可设置不同权限玩家可以创建的自定义游戏数量(为您带来更多VIP收入)。
3.在自定义模式中玩家可以使用DR原创专属的"WorldEdit"。没错，您可以让玩家更方便地建设地图，又不用担心熊孩子用WE毁服。且DR专属WE会用粒子效果显示出作用范围，更加直观。
4.无需担心玩家用TNT、沙子、掉落物品、巨量生物、密集高频红石等卡服，DragonRichi会自动清除卡服因素(但不会影响玩家正常使用这些东西，小面积高频红石等不卡服的东西不会被清除)。
5.您可以单独设定不同权限玩家拥有的自定义权限，以获得更多的赞助收入，且无需给不同Group添加一堆权限，仅需在配置文件中调整不同功能需要的权限，如：CanUseCustomGame: "dr.vip"。于是您可以制作出如下表的分级自定义权限：

| |自定义模式数量|自然回血设置|编辑开场药水效果|游戏中建筑|调整最高血量|Combo|编辑击退|Pot模式|Team模式|<font color=orange>发行游戏</font>|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|<font color=gray>普通玩家</font>|1|<font color=green>**√**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|
|<font color=# aaa>VIP</font>|2|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|
|<font color=green>**VIP+**</font>|3|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|
|<font color=red>**MVP**</font>|4|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|
|<font color=purple>**MVP+**</font>|5|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|<font color=red>**×**</font>|
|<font color=gray>**MVP++**</font>|6|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=red>**×**</font>|
|<font color=orange>**MVP+++**</font>|9|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|<font color=green>**√**</font>|

6.更多自定义模式相关内容请见DragonRichi(Admin)以及DragonRichi玩法

## 录制系统
1.不仅可以记录下玩家的移动轨迹，还能记录下每时每刻背包的物品，玩家的ping和cps，玩家的攻击距离等等。
2.玩家可以把录像作为举报玩家的证据，方便管理员调查黑客。
3.您可以右键于录像中的一名玩家中，切换到该玩家，重温当时的激烈场面。不同于观察者模式，您能看到该名玩家的手和背包。
4.您可以暂停画面，精细调整到达哪一帧，以及变速观看。
5.玩家可以创建一个摄像机，使用关键帧设置移动路径，以制作动画。
6.原创编码，用非常小的占用空间记录巨量的内容。

## 自定义GUI
1.您可以自定义Unranked、Ranked、FFA的GUI和大厅背包GUI。同样，编辑这些GUI的过程也是全GUI的！完全脱离代码的苦恼。
2.您可以创建自定义GUI，以满足您的需求。
3.除大厅背包GUI，您可以自由调整GUI的行数和页数。
4.GUI中的UI可以绑定点击事件。您可以选择一些自动事件UI拖拽到您的GUI中，会自动帮您绑定事件，提高效率。
5.您可以配置让玩家在一个GUI中打开另一个GUI时无闪烁(在使用其他GUI插件的时候闪晕了吧)。

## 玩家可设置
1.支持多语言，可自定义任何数量的语言
2.编辑装备顺序，可设置不同权限拥有的存档数(最多5个)

## 其他功能
1.自定义进入服务器时的title
2.不同模式下的玩家在tab列表中互相隐藏
3.自定义tab列表顶端和底端的信息

## 相关链接
[DRAGONRICHI玩法](richi.md)
[DRAGONRICHI(Admin)](richiadmin_cn.md)

<!-- 参考https://www.spigotmc.org/resources/strikepractice-%E2%80%93-1v1-2v2-pvp-bots-tournaments-parties-kit-editor-gui-best-of-rounds-and-more.46906/ -->