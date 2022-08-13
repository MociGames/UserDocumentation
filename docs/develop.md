# 墨瓷开发要求与文档。

---

### 版本 2019.9.14
更新内容：增加了颜色测试页面，修改了一点小错误

旧版本内容：
2019.8.23: GUI的基本用法

## 简介

  InkKettle，是我们的第一个服务端。基于git-Paper，并在此基础上而外增加和删减内容，来配合我们的实际要求。
  Common，是与InkKettle配套的子母插件。
  PermDog,是我们自行开发的权限组插件。
  IG，全称锦衣卫，是反作弊系统。
  Yamen，衙门，封禁管理系统。

  注意，本页面一些超链接只有Moci开发组用户才能访问。

---

### 内容

### 代码规范

* 包、类名规范
 -  包名统一采用cn.moci.*，便于构建器收取。导用的APIs独立内置。
 -  类名开头大写，每个单词字母开头大写([详见驼峰命名法](https://baike.baidu.com/item/%E9%AA%86%E9%A9%BC%E5%91%BD%E5%90%8D%E6%B3%95/7794053?fr=aladdin))。所有的事件、管理器、核心独立一个包。
* 方法名规范
 - 布尔方法建议统一 is、can、has开头,部分需要使用get，如
  `u.getVersionManager().isOnlineMode();`
  `u.getProfileManager().getSettings(SettingsType.hidePlayer);`
 - String、int、Double、Object 统一get开头， 如 `u.getProfileManager().getLanguage();`
 - 新建、移除一个Object 建议统一使用 create 和 remove 开头，如
 `u.getDataManager().createHandler(“Mode”, “NONE”);   `
 `u.getDataManager().removeHandler(“Mode”);`
 - 修改一个Object建议统一使用 modify开头，如
 `u.getDataManager().modifyData(“Point”, “66”);`

### 功能

## User

#### 重要问题提前说。
User的数据加载是在PlayerJoinEvent之后的。因此当使用PlayerJoinEvent时，User数据未加载完成。
若需要在玩家加入时使用数据，请监听`UserLoadedEvent`

注意，储存重要信息时，若需要使用玩家名，务必使用`user.getRealName()`。

**请不要在玩家离线后异步获取Settings、Handler等**，因为这些数据在PlayerQuitEvent执行完后就被清理了，异步获取只会得到null
如有需要，请在主线程获取，再放到异步线程处理得到的信息

#### InkID
InkID是每个用户的唯一区别码，建议InkID作为数据存储的Key。
但需要注意的是，InkID不被推荐用于存储临时的数据，因为只有当用户数据加载完毕后才可以正常读取InkID(在Handler的init()函数被执行时，inkID是被加载好的请放心食用)。
获取方法：`player.getUser().getInkID();`

#### User Handler

使用UserHandler和数据库可以很方便地记录战绩。 参考 `cn.moci.arenas.ffa.handlers.FFAStats` 。

每个用户都可以为他添加新的Handler，这个可以用来存储数据，或是写一个`? extends AbstractUserHandler`的类去实现多个功能(参考 `cn.moci.kettle.managers.user.RankManager`)。
在玩家服务器的时候注册这个Handler
```java
@EventHandler
public void onUserLoaded(UserLoadedEvent event){
	event.getUser().registerHandler(MyHandler.class);
}
```
通过`MyHandler myHandler = user.getHandler(MyHandler.class);`即可获取该Handler
在玩家离开服务器时取消注册
```java
@EventHandler
public void onQuit(PlayerQuitEvent event){
	event.getPlayer().getUser().unregisterHandler(MyHandler.class);
}
```
AbstractUserHandler也有回调函数哦
```java
public class MyHandler extends AbstractUserHandler{
	@Override
	public void init(){//当Handler被加载时调用，可以用来加载战绩啥的
		User user = getUser();//获取加载这个Handler的User
		//do something
	}
	
	@Override
	public void onDisable(){}//当Handler被移除时调用，可以用来保存战绩。
	//注意只有自己removeHandler之后才会调用，玩家下线虽然会清理掉该Handler但不会调用
}
```

除此之外还可以储存其他信息
```java
user.registerHandler("MyPlugin_Paukour_Time",0);
int i = (int)user.getHandler("MyPlugin_Paukour_Time");
user.replaceHandler("MyPlugin_Paukour_Time",1);
user.removeHandler("MyPlugin_Paukour_Time");
```
**注意这种Handler不要和已经注册过的AbstractUserHandler类名相同**，建议加个插件名前缀
为了防止Handler名写错，还可以专门用一个类储存这些String，如
```java
public class Handlers{
	public static final String paukour_time = "MyPlugin_Paukour_Time";
}
//使用
user.registerHandler(Handlers.paukour_time,0);
```

#### 用户设定系统

可以得到用户的每一个细微设定，自行探索不做叙述。



## 多语言系统
如何创建一条多语言消息和使用多语言消息参考 `cn.moci.purchase.PurchaseMessages`。
用起来的效果就像是这样 `u.sendText(PurchaseMessages.payURL).commit();` ，会自动读取用户语言并给他发送消息。**注意，一定要加**`.commit()`**！！！,否则不会发送！**

所发送的Text的声明：
`public cn.moci.kettle.language.Text(String root,String address,String Default);`
其中，`String root`是在语言目录中创建的一个新目录的名词，为保证不与其他目录冲突，一般是插件名。
`String address`是这条信息在yml文件中的位置
温馨提示，根据yaml的语法，不要出现有一个“Test.Message: xxx”和一个“Test.Message.info: xxx”具体原因配置过插件config的都应该知道吧。
`String Default`是这条信息第一次被创建时默认的文字，此时所有语言文件都是这一条默认信息。
举个例子，`Text mes = new Text("MyPlugin","Test.Message","&7这是一条测试消息");`
当这条信息被注册的时候，就会在`(在config写的语言文件路径)/Myplugin/`这个目录下创建zh_cn.yml、en_us.yml等多个文件，其中每一个文件的内容都是相同的（因为还没有被人工翻译），除了zh_tw.yml被初步地转化了部分简体中文为繁体中文，但是繁体中文还是需要人工检测语法和修改补充错漏的信息。
打开其中一个（以zh_cn为例），就会是这样的：
```yaml
Test:
  Message: "&7这是一条测试消息"
```
另外，符号&会自动转换为[Minecraft颜色符号](https://minecraft-zh.gamepedia.com/%E6%A0%B7%E5%BC%8F%E4%BB%A3%E7%A0%81)§，如果需要显示&这个符号的话，请用&&
你可以在[这里](color.md)测试你的文字

插入一条无关的java知识：为什么`String Default`中的`Default`首字母大写了？
标识符不是首字母一般小写吗？因为default是java中的关键字，标识符不能是关键字。

墨瓷的语言文件的创建是自动的，你只需要在代码中注册即可。
**一定记得在加载插件的时候注册**，否则的话虽然也会自动注册，但是消息什么时候用到就什么时候注册，导致不会生成完整的语言文件直到所有消息被使用。
使用 `cn.moci.kettle.language.LanguageManager.registerAllTranslater(Class<? extends AbstractedTranslator> c);` 进行注册。
请区分registerTranslater()和registerAllTranslater()，前者只会注册语言类下的所有Text和MultiText，后者在前者的基础上还会寻找语言类中的内部类(没错可以无限嵌套类来分类各种类型的消息)。
如：
```java
//MyMessages.java
public class MyMessages extends AbstractedTranslator{
	public static String root = "MyPluginName";//为了节约资源，我们定义一个全局变量root，把所有root都填写成这个变量，这样就不会创建一堆相同的String浪费内存了。
	public static Text mes = new Text(root,"Test.Message","&7这是一条测试消息");//注意加上public static
}

//MyPlugin.java
public class MyPlugin extends JavaPlugin{
	@Override
	public void onEnable(){
		LanguageManager.registerAllTranslater(MyMessages.class);
	}
}
```
有一些非常大众化的消息已经在Kettle中注册了（如保存、取消、返回等等），为了节约资源，你可以使用
```
cn.moci.kettle.language.KettleTranslator.xxx
KettleTranslator.defaultMessages.noPermission
KettleTranslator.gui.save
```
等多种已经写好的消息，具体可以看KettleTranslator的源码，如果你有新的主意可以联系Kettle开发者进行补充。这只是优化资源、节省翻译时间的一种方式。你也可以不这么做，这是没关系的。

除了向玩家发送消息以外，你也可以获取翻译后的消息，使用方法如下：
`String mes = user.sendText(MyMessage.mes).buildString();`
或者使用更方便更偷懒的方式：
`Team team = new Team(MyMessage.defaultTeamName.translate(user));`

有些时候我们需要在消息中嵌入*变量*，Kettle多语言系统考虑到了这点，我们可以使用如下代码替换其中的“变量”，使这条消息变成一个“动态”的消息，而不是一成不变的“静态消息”：
```java
//Message.java
public class Message extends AbstractedMessage{
	String root = "MyPlugin";
	public static Text giveCoins = new Text(root,"Test.Message.GiveCoins","&7你从&f %(Player)  &7获得了&f&l %(Coins) &e金币。");
}
//Test.java
public class Test{
	public void xxx(Player p,Player target,int coins){
		p.getUser().sendText(Message.giveCoins).replace("%(Player)",target.getName()).replace("%(Coins)",coins).commit();//一定不要忘了commit()！一定不要忘了commit()！一定不要忘了commit()！
		//得到String的方法：
		String str = p.getUser().sendText(Message.giveCoins).replace("%(Player)",target.getName()).replace("%(Coins)",coins).buildString();
	}
}
```

上方代码使用了*%(Player)*和*%(Coins)*来代表要替换的变量，在实际开发中也建议使用*%()*的形式，防止出现像下面的问题：
```java
	Text giveCoins = new Text(root,"Test.Message.GiveCoins","&7你获得了&f&l Coins &eCoins。");//假设Coins是2019，那么替换Coins出来的是 &7你获得了&f&l 2019 &e2019。
```

我们在开发GUI时翻译物品的Lore时遇到了点麻烦，Lore换行不支持转移字符\\n，而Lore是一个List<String>
为了在一行代码中解决，把List换成了String[]，所以就有了这个：
```java
	public static MultiText lore = new MultiText(root,"GUI.Item.Lore",new String[]{
		"&f第一行", //注意物品的lore默认是紫色(&5)
		"&f第二行 %(Test)"
		});

	使用方法：
	ItemStack is = xxx;
	ItemMeta im = is.getItemMeta();
	im.setLore(Message.lore.replace("%(Test)","被替换的信息").formatMutiText(user));
```



## ItemStackFactory
这个功能异常好用，特别是当你要做GUI的时候。
注意是`cn.moci.kettle.methods.ItemStackFactory`，而不是Bukkit的那个
```java
// 实例地址 cn.moci.menus.pages.games.MainMenu
ItemStack lobbyItem = new ItemStackFactory(Material.BED, 物品数量, 物品Data)
				.setLore(Messages.item_Lobby_lore.formatMutiText(u)/*设置lore，利用多语言直接生成每个用户的语言的文字List*/)
				.setDisplayName(Messages.item_Lobby_name.translate(u)/*设置物品名，也是利用多语言实现。*/)
				.toItemStack();
```

LSeng给出的实例(涉及内容：语言系统、GUI、ItemStackFactory)
```java
gui.setItem(0, new GUIItem(new ItemStackFactory(Material.WOOL,1,14)//没错data不需要强制转换成short了,方便吧
	.setDisplayName(user.sendText(Messages.itemName.translate(user)).replace("%(Player)",user.getPlayer().getName()).buildString())
	.setLore(Messages.itemLore.formatMultiText(user))
	.addLore(enableXXX ? KettleTranslator.gui.enable.translate(user) : KettleTranslator.gui.disable.translate(user))//在以上Lore基础上再额外添加一个启用或不启用
	.addEnchant(Enchantment.DAMAGE_ALL, 1)//随便加一个附魔效果
	.addFlag(ItemFlag.HIDE_ENCHANTS)//隐藏掉Lore中关于附魔的信息，配合上一行代码可以做到一个附魔效果的ui
	//同理，像药水效果、不可损坏等信息都可以用ItemFlag隐藏
	.toItemStack()){
		@Override
		public void ClickAction(ClickType type, User u) {//如果玩家点击了这个ui回调的函数(其实不止点击，只要跟这个物品有关如对着他按Q都会回调)
			if(type == ClickType.LEFT || type == ClickType.SHIFT_LEFT){//如果玩家是左键或shift+左键了它
				//do something
			}//更多方式(如双击。按q扔出等)请看org.bukkit.event.inventory.ClickType
		}
	});
```
有了这些东西，我们完全可以把UI设计、翻译、点击事件一气呵成写成一行，而不必拆分成很多代码。
不过为了增加阅读性，我们最好根据主次、层次把一行代码分成几行。
如果上述代码不是使用了KettleAPI是怎样的呢（滑稽）

<details>
<summary><mark><font color=darkred>点击查看原生Bukkit做法(辣眼警告)</font></mark></summary>
<pre>
	public class GUIInBukkit implements Listener{
		Map&lt;Player,Inventory&gt; openGUIs = new HashMap();
		public void openGUI(Player p){
			Inventory gui = Bukkit.createInventory(null, 9, "翻译你\*呢(huaji)");//创建GUI
			ItemStack ui = new ItemStack(Material.WOOL,1,(short)14);//虽然ItemStackFactory方便，但还是建议看一下原版的ItemStack创建方法
			ItemMeta im = ui.getItemMeta();
			im.setDisplayName("翻译你\*呢(huaji)");
			List&lt;String&gt; lore = new ArrayList();
			lore.add("§7这颜色符号真难打");
			im.setLore(lore);
			im.addEnchantment(Enchantment.DAMAGE_ALL, 1);
			im.addItemFlag(ItemFlag.HIDE_ENCHANTS);
			ui.setItemMeta(im);//一定要记得把Meta绑到ItemStack中
			gui.setItem(0,ui);
			p.openInventory(gui);
			openGUIs.put(p,gui);//记录玩家打开的这个GUI
		}
		@EventHandler
		public void onClick(InventoryClickEvent event){
			if (!(event.getWhoClicked() instanceof Player))
				return;
			Player p = (Player) event.getWhoClicked();
			if (event.getSlot() != -999){//点击的不是GUI外面
				Inventory gui = openGUIs.get(p);
				if(event.getInventory.equal(gui)){//玩家打开的是GUI，而不是箱子
					if(event.getSlot() == 0){//如果gui中ui改了位置，那么这里也要改成相应的slot，很麻烦
						event.setCancelled(true);//阻止玩家拿起物品
						if(event.getClick() == ClickType.LEFT || event.getClick() == ClickType.SHIFT_LEFT){
							//do something
						}
					}
				}
			}
		}	
		@EventHandler
		public void onClose(InventoryCloseEvent e){
			if (!(event.getWhoClicked() instanceof Player)) 
				return;
			Player p = (Player) event.getWhoClicked();
			if(openGUIs.contain(p)) openGUIs.remove(p);//关掉GUI时从map中清理掉GUI
		}
	}
</pre>
</details>

还是建议看一下了解一下Bukkit中GUI的原理，代码中给了注释方便理解，也可以在看完下一节后再返回来看。
解释完这个方便的ui制作神器(也可以用来制作物品)，接下来来看上面超纲的，也是Kettle的重头戏——GUI系统



## GUI系统
墨瓷GUI的API **方便炸了，强烈推荐** ！！！
看到原生Bukkit GUI制作方法，是不是觉得做一个那么简单的GUI要用那么多代码很麻烦呢？
看了上节GUI搭配ItemStackFactory(以下简称ISF)的使用，是不是觉得写一个GUI只需一行或几行代码非常地舒适呢？
下面开始Kettle重头戏
Show Time
<br>

首先放出实例代码的参考，这些是墨瓷真实用到的代码，可以在游戏中体验。实例代码有助于更好的理解和使用这个API
参考[`cn.moci.menus.pages.games.MainMenu`](https://git.carmwork.com/MociDev/Common/src/master/Menus/src/main/java/cn/moci/menus/pages/games/MainMenu.java)(一般用法)，看看用API写多方便吧！
[`cn.moci.dr.gui.EditKitGUIArenaProgramEdit`](https://git.carmwork.com/MociDev/DragonRichi/src/master/src/main/java/cn/moci/dr/gui/GUIArenaProgramEdit.java)(神仙级用法)

###基础
首先我们要知道关于Minecraft原版容器页面的知识。
####小箱子大箱子？弱爆了
我们可以把带有格子的可以放物品的容器看作是一个GUI。
看到带有格子的容器，首先想到的最常用的当然就是箱子了。箱子有27格的三行的小箱子，有54格的六行的大箱子，但实际上bukkit可以显示1到6行。除此之外，漏斗、发射器等页面都是带有格子的容器，都可以作为GUI。
[点我查看Kettle支持的标准GUI](https://git.carmwork.com/MociDev/InkKettle/src/master/Kettle-Server/src/main/java/cn/moci/kettle/gui/GUIType.java)
注意不是所有GUIType中的参数都支持，比如CREATIVE会报错，CANCEL不是GUI为不打开等。
在容器中，每个格子都有自己的slot标记自己是第几个格子，从左到右从上到下第一个格子slot为0，第二个slot为1（就像数组那样）。所以，在54格大箱子页面中，第一行最后一个slot为8，第二行第一个slot为9，最后一行最后一个为53。
请注意，一般打开容器页面后，除了容器有格子，自己背包也有格子。默认下只判断容器的格子，如果要判断自己背包的格子后面再讲。注意自己背包的格子也是从0开始(到35或者包括装备到39)，所以0既可以代表背包中第一个格子也可以代表GUI中第一个格子，但要注意背包和容器是两个GUI，默认情况下编辑的是容器GUI。
除此之外还有一个特殊的slot为-999，这个代表容器之外的（就像你用鼠标点一个物品然后在容器外点一下扔掉物品一样(不是Q键扔掉物品)，扔掉物品这一操作就是点击了slot -999）

####第一个GUI
首先要大家注意一点墨瓷团队也经常犯的错误，由于犯错几率很大，所以写在前面。
写一个非静态GUI(每次打开都需要重新生成，比如说不同玩家打开看到的可能不一样)的时候建议先这样：
```java
GUI gui = new GUI(...);

//TODO 根据xxx_todo.txt的要求，这个GUI需要...

gui.openGUI(user);
```
先写上面代码，特别是`gui.openGUI(user);`，防止忘了打开GUI造成GUI不显示又没报错的尴尬情况。
这个GUI打开之后默认取消所有跟这个容器相关的Bukkit点击事件，除非在有人打开GUI的时候重载插件，否则不会出现GUI刷东西的问题。
声明一个GUI
`GUI gui = new GUI(GUIType type,String name);`或`GUI gui = new GUI(GUIType type,Text name);`
不建议使用后者，因为后者无法进行replace操作，所以要翻译的话前者Text buildString就行了。
为了方便讲GUI的知识，这里就不用翻译了。
```java
GUI gui = new GUI(GUIType.ONEBYNINE,"我的GUI");
gui.openGUI(user);
```
上面的代码就已经可以打开一个正常的GUI了，如果这个GUI是静态的(不会因人而异)，可以一GUI多用。
```java
public class MyGUI{
	public static GUI gui;
	static{
		gui = new GUI(GUIType.ONEBYNINE,"公共厕所");
		//完善GUI
	}
	public static void open(User user){
		gui.openGUI(user);
	}
}
```
不过很多情况下GUI并不是单一的，它可以因人而异，比如说翻译。
那直接每次打开都调用一个函数重新创建一个重新编辑GUI就行了，GUI是不费时且会自动回收的，不用担心性能问题。如果在其中自己要进行耗时操作，请在耗时操作那异步进行（不要异步打开GUI，Bukkit不允许，也不要再异步加载数据然后在异步线程外打开GUI(异步线程还在运行中)，这样GUI未加载完就被打开了）
创建完了GUI，肯定不能让里面空荡荡的是吧，接下来我们就放点东西进去。

####向GUI中添加Item
`GUI::setItem(int slot, GUIItem item);`
其中 slot为存放GUI Item的位置，GUIItem的构建函数如下
`public GUIItem(ItemStack ui);`
所以，配合ItemStackFactory我们可以这样做:
```java
gui.setItem(0,//第一行第一个
		new GUIItem(
			new ItemStackFactory(Material.DIAMOND_SWORD)//显示一个钻石剑
				.setDisplayName("大保健")
				.setLore("&f这里是注释")
				.toItemStack()//因为ItemStackFactory不是ItemStack，所以要转为ItemStack
			)
		);
```
这样就向GUI中第一行第一个添加了一把钻石剑，利用ISF可以把ItemStack的Meta设置整合到一行代码中，满足了绝大部分需求。

####给Item添加点击事件
GUI只能看不能~~操~~肯定是不够的。
GUIItem中有一个空函数ClickAction(别问我为什么名字那么奇怪以及首字母大写)，我们只需在创建GUIItem时改写这个函数。这个函数为
`GUIItem::ClickAction(ClickType type, User u);`
其中`ClickType type`是玩家点击的方式的枚举，可以用来判断玩家是左键、右键、双击还是什么玩意。具体可以看[这里](https://git.carmwork.com/MociDev/InkKettle/src/master/Kettle-Server/src/main/java/org/bukkit/event/inventory/ClickType.java)
`User u`为点击GUI的玩家，可以用来做一GUI多人用的功能。
下面给出实例
```java
gui.setItem(0, new GUIItem(new ItemStackFactory(Material.DIAMOND_SWORD)  //技巧：如果是Material.AIR，就是看不见但可以响应点击事件的Item
		.setDisplayName("Hello World")
		.toItemStack()){
		@Override
		public void ClickAction(ClickType type, User u){
			u.getPlayer().sendMessage("HelloWorld");
			u.getPlayer().playSound(u.getPlayer().getLocation(), Sound.CHICKEN_EGG_POP, 1, 1);//播放一个在玩家中心发出来的(可以调整位置以做到立体声效果)，只有该玩家能听得到的鸡下蛋的声音。在实际GUI开发中建议加上各种点击声音以提高玩家体验。
		}
	});
```

####批量添加相同的Item
有时候我们要在GUI中添加很多相同的Item，比如说：
---------------分界线(huaji)---------------
这时我们就要用到批量添加Item：`gui.setItem(GUIItem item, int... slot);`
实例
```java
gui.setItem(new GUIItem(new ItemStackFactory(Material.STAINED_GLASS_PANE)
	.setDisplayName("分界线")
	.toItemStack()),
	9,10,11,12,13,14,15,16,17);//在GUI第二行全部放上白色染色玻璃
```

####让GUI动起来
能不能让GUI在打开后不重新创建并重新打开也能修改GUI的内容呢？答案是可以的。(但是如果要修改标题、GUIType的话，受Bukkit限制还是得重新创建)
在打开GUI后依然可以使用`GUI::setItem()`，但是所做的修改玩家是看不到的。我们还需要一个操作玩家才能看到，那就是`GUI::updateView()`。下面给出实例
```java
gui.setItem(0, new GUIItem(new ItemStackFactory(Material.DIAMOND_SWORD)
		.setDisplayName("点我呀")
		.toItemStack()){
		@Override
		public void ClickAction(ClickType type, User u){
			gui.setItem(1, new GUIItem(new ItemStackFactory(Material.GOLD_SWORD)
				.setDisplayName("你好呀")
				.toItemStack()){
				@Override
				public void ClickAction(ClickType type, User u){
					u.getPlayer().closeInventory();//关掉GUI
				}
			});
			gui.updateView();//更新GUI
			u.getPlayer().playSound(u.getPlayer().getLocation(), Sound.ORB_PICKUP, 1, 1);//叮~
		}
	});
```

上面这些已经足够使用了？不，现在才开始进入正题，开始体验Kettle GUI的强大吧。

###自动生成GUI！真正实现用鼠标做GUI！
还没做好(虽然可以用了)

###内置API
考虑到GUI开发时的多种情况，Kettle提供了一些GUI的子类来更方便地实现某些动态GUI。
####翻页GUI API
原来我们使用CommonPagedGUI，但是为了做到翻页很麻烦、并且还要学两种方法来适应两种情况。
为此，Kettle新版本增加了AutoPagedGUI来更方便地开发翻页GUI。CommonPagedGUI唯一的使用放在了KettleDebug中，在这里就不教了（没必要学）
AutoPagedGUI的构造函数比GUI多出了两个参数
`public AutoPagedGUI(GUIType type, Text/String name, int a, int b);`
其中，a和b是在GUI容器中选取的两个slot构成一个矩形（想象一下MC中圈地两点成体，不过这里是矩形）
比如说11和33，在大箱子页面中就是

|傻|逼|表|格|要|加|这|个|头|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|0|1|2|3|4|5|6|7|8|
|9|10|**11**|**12**|**13**|**14**|**15**|16|17|
|18|19|**20**|**21**|**22**|**23**|**24**|25|26|
|27|28|**29**|**30**|**31**|**32**|**33**|34|35|
|36|37|38|39|40|41|42|43|44|
|45|46|47|48|49|50|51|52|53|

矩形不好看？没关系，我们还提供了不规则形状构造函数
`public AutoPagedGUI(GUIType type, Text/String name, int[] range);`
其中，range是储存范围slot的数组。
如上面的11到33就是`new int[]{11,12,13,14,15,20,21,22,23,24,29,30,31,32,33}`
这个范围就是储存Item的范围

接着我们需要设置翻页按钮的位置(否则没地方点)
`gui.setLastPageSlot(int slot);`
`gui.setNextPageSlot(int slot);`
设置完了之后打开gui会自动向指定slot添加翻页按钮，按钮的ui默认已经设计好了，如果需要更改(不建议更改)，可以如下操作
上一页`gui.setLastPageUI(ItemStack ui)`
下一页`gui.setNextPageUI(ItemStack ui)`
已经到首页了`gui.setFirstPageUI(ItemStack ui)`
已经到尾页了`gui.setEndPageUI(ItemStack ui)`
温馨提示：`KettleTranslator.gui`中有*上一页*、*下一页*等翻译条目

设置完后我们还可以gui.setItem再修饰一下GUI，但是要注意不要设置在储存范围以内(因为会被覆盖)
通过`gui.add(GUIItem item);`即可将储存范围内添加Item
下面用一个例子把上面的综合起来
将mc中所有可在Bukkit中使用的声音放到GUI中，每个Item显示它的Bukkit声音名，点击可以播放该声音
```java
public static void open(User user){
	AutoPagedGUI gui = new AutoPagedGUI(GUIType.SIXBYNINE,"天天冻听",0,44);
	gui.setLastPageSlot(45);
	gui.setNextPageSlot(53);
	for (Sound s : Sound.values()) {
		gui.addItem(new GUIItem(new ItemStackFactory(Material.NOTE_BLOCK)
	        .setDisplayName(s.name())
	        .toItemStack()) {
	            @Override
	            public void ClickAction(ClickType type, User u) {
	                u.getPlayer().playSound(p.getLocation(), s, 1f, 1.0f);
	            }
	        });
	}
	gui.openGUI(user);
}
```
如果你觉得翻页太直接了，做一些成就系统、氪金物品一按就过不装逼的话，Kettle还提供了RollingAutoPagedGUI，页变成了行，也就是说点击下一页只会往下滚一行。
不过鉴于滚动模式的特殊性，滚动GUI只能是矩形范围。
RollingAutoPagedGUI的配置和使用和AutoPagedGUI无太大区别，在这里就不复读了。

####可选择购买方式的~~骗钱~~购买页面
构造函数`public Transaction(User user, GUIItem item, String title)`或`public Transaction(User user, GUIItem item, String title, boolean buyAndUse)`
其中，`User user`为购买者,`GUIItem item`为购买的物品的显示ui，`String title`为GUI的标题,`boolean buyAndUse`为是否开启购买并使用选项
然后改写`boolean Transaction::buy(String choice)`函数
其中，返回值boolean为是否购买成功，参数String choice为玩家选择的货币选项
若开启了buyAndUse选项，还要再改写函数`Transaction::buyAndUse()`

接着添加货币选项
`Transaction::addVaultChoice(String choice, String name);`
其中`String choice`为货币标识，`String name`为ui显示的标题

打开GUI`Transaction::open();`

下面给出实例
```java
ItemStack item = ItemStackFactory(Material.DIAMOND_SWORD)
        .setDisplayName("LSeng's ultimate super motherf*cker 32k")
        .addLore("&7Sharpness enchantment.level.32787");
        .addEnchant(Enchantment.LUCK,1)
        .addFlag(ItemFlag.HIDE_ENCHANTS)
        .toItemStack();
Transaction t = new Transaction(user,new GUIItem(target.toItemStack()),text.translate(user),true) {
    @Override
    public boolean buy(String choice) {
        if(choice.equals("Coins")){//选择了用金币购买
            if(user.getSettings().getVaultManager().getCoins()<1500){//金币不够
                user.sendText(KettleTranslator.dontHaveEnoughMoney).commit();
                user.getPlayer().closeInventory();
                user.getPlayer().playSound(user.getPlayer().getLocation(),Sound.NOTE_BASS,0.5f,1);
                return false;//购买失败
            }else{
                user.getSettings().getVaultManager().removeCoins(1500);//扣钱
            }
        }else{//选择了用点券购买
            if(user.getSettings().getVaultManager().getCredit()<500){//点券不够
                user.sendText(KettleTranslator.dontHaveEnoughMoney).commit();
                user.getPlayer().closeInventory();
                user.getPlayer().playSound(user.getPlayer().getLocation(),Sound.NOTE_BASS,0.5f,1);
                return false;//购买失败
            }else{
                user.getSettings().getVaultManager().removeCredit(500);//扣钱
            }
        }
        user.getPlayer().getInventory().add(item);
        user.getPlayer().closeInventory();
        user.sendText(KettleTranslator.buySuccessfully).replace("%(Item)","LSeng的32k大保健");
        return true;
    }

    @Override
    public void buyAndUse(String choice){
        if(buy(choice)){//buy()的boolean返回值是这样用的
            //umm似乎这个商品没有必要开buyAndUse，但是没关系，知道怎么用就行啦
            user.getPlayer().sendMessage("您正在使用这把剑");
        }
    }
};
t.addVaultChoice("Coins","500 &e金币")
 .addVaultChoice("Credit","500 &6点券");
t.open();
```

####抽个奖呗
由于抽奖GUI被移出了Kettle，所以需要导入Purchase-1.0-SNAPSHOT.jar包。[下载地址](https://jenkins.carmwork.com/job/Common/)
构造函数`public MysteryCases(String/Text name, ItemStack display)`
其中，`String/Text name`为GUI标题，`ItemStack display`为该抽奖箱显示的ui，其他构造函数中的`int typeID`是便于Purchase用反射获取的一个ID，对我们的开发无用，不要填，不然会因无法获取id出现奇怪的bug。

通过`MysteryCases::add(GUIItem item, int chance);`添加物品
其中`GUIItem item`是用了自定义事件的奖品ui，关于自定义事件会在后面讲到。
`int chance`是抽到该奖品的权重，抽到该奖品的概率为 奖品权重/所有奖品总权重
要注意，虽然点击确定抽奖后有抽奖动画，但是当点下确定抽奖时，结果就已经出来了，奖品就已经发放了。至于为什么，请继续往下看
添加的`GUIItem item`要改写下面两个自定义事件
`GUIItem::customAction(User user)`当玩家点击了确定抽奖并抽到这个奖品时调用，这个时候就可以在数据库中移除该玩家的该抽奖箱，并给物品了（怕抽到一半玩家关掉了GUI或者掉线什么的）
`GUIItem::customAction()`如果抽奖动画已经结束了，玩家还没关掉GUI，这时会调用这个函数，此时就可以给玩家发送抽到的是什么东西的消息了。

或者你也可以这样，只重写GUIItem::customAction()，也就是抽奖动画结束后再发放奖品、从数据库中移除该抽奖箱，不过有玩家拼手速关掉重开的风险

下面给出实例
```java
MysteryCases gui = new MysteryCases("沙雕杯纪念箱", new ItemStackFactory(Material.ENDER_CHEST)
				.setDisplayName("沙雕杯纪念箱")
				.addLore("&7谨以此箱纪念 沙雕杯 比赛冠军")
				.toItemStack());

gui.add(new GUIItem(new ItemStackFactory(Material.GOLD_INGOT)
        .setDisplayName("1000金币")
        .toItemStack()){
    @Override
    public void customAction(User user) {
        user.getSettings().getVaultManager().addCoins(1000);
    }

    @Override
    public void customAction() {
        user.getPlayer().sendMessage("你抽到了1000金币");
    }
},19);

gui.add(new GUIItem(new ItemStackFactory(Material.DIAMOND)
        .setDisplayName("SSR")
        .toItemStack()){
    @Override
    public void customAction(User user) {
        user.getSettings().getVaultManager().addCoins(9999);
    }

    @Override
    public void customAction() {
        user.getPlayer().sendMessage("你抽到了SSR");
    }
},1);

//总权重：20，所以1000金币抽到的概率为19/20=95%，SSR抽到的概率为1/20=5%

gui.openGUI(user);
```


####命个名
有时候我们需要让用户输入一段文字，常用的方法有：聊天框输入、牌子输入、铁砧输入、书籍编辑等。
其中最好用的方法应该是铁砧，因为聊天框输入有太多的风险(除非要输入大量的文字)，牌子输入有四行且没装中文修复不能ctrl c ctrl v，书籍就更恐怖了有几十页，且为了支持多版本还得为不同版本写判断(因为不同版本的书实际有区别)
而铁砧改名既直观又可以输入较多的文字(30个字符)，获取起来方便，安全性高，所以说铁砧好。
由于该功能是利用发包实现的，所以比较底层，用起来也比较“底层化”
构造函数`public AnvilGUI(Player player, AnvilClickEventHandler handler)`
其中`Player player`为打开GUI的玩家, `AnvilClickEventHandler handler`为监听器
需要改写其中的函数`AnvilClickEventHandler::onAnvilClick(AnvilClickEvent paramAnvilClickEvent)`
通过`AnvilGUI::setSlot(AnvilSlot slot, ItemStack item)`为铁砧页面添加物品
通过`AnvilGUI::open()`打开GUI
不多说，直接上代码，一看就能懂，一懂就会改，~~一改就会废~~
```java
AnvilGUI agui = new AnvilGUI(u.getPlayer(), new AnvilClickEventHandler() {
    @Override
    public void onAnvilClick(AnvilClickEvent paramAnvilClickEvent) {
        if (paramAnvilClickEvent.getSlot() == AAnvilGUI.AnvilSlot.OUTPUT) {//点击了输出的格子
            String message = paramAnvilClickEvent.getName();
            u.getPlayer().sendMessage("你输入的是:" + message);

            paramAnvilClickEvent.close();
        }
    }
});
agui.setSlot(AAnvilGUI.AnvilSlot.INPUT_LEFT, new ItemStackFactory(Material.NAME_TAG)
        .setDisplayName("在这里修改")//给输入物品中左边的改名字后，这个名字会显示在铁砧改名那里
        .toItemStack());
agui.open();
```

###解除GUI的封印
####不要回答，不要回答，不要回答
####给GUI立个Flag
####自定义点击？？？
####自定义GUI事件
####关不掉的GUI

同样我们在做动态GUI的时候一般用到线程，玩家突然关闭GUI后可以清理线程。

####“生的”点击

###制作属于自己的GUI API

## 记分板系统及显示相关
积分榜除了在玩家右边显示一个标准记分板以外，Minecraft原版还可以在玩家头上的名字前后、下方，tab列表等显示一定的信息。
一个标准的记分板带有一个标题，最多15行每行32个字符的信息。
在用Bukkit制作记分板的时候经常会遇到很多问题，比如更新记分板的时候闪烁、条目重复、条目消失、排序错乱、只能显示16个字符、功能不够强大、网络占有量大等问题。于是Kettle针对这些问题制作了一个强大的记分板系统，这个记分板还可以方便地被用户配置（如开关记分板等），利用bukkit的team score发包解决闪烁和字符长度问题，利用条目重复检验避免网络资源的浪费。


###记分板
参考 `cn.moci.lobby.methods.ScoreboardThread` 。就是给每个用户创建一个不断更新的积分榜。积分榜更新不会闪，重复的内容本API会自动不更新(以免浪费网络资源，墨瓷最缺的不是性能，而是比性能贵几百倍的网络！！！)，所以不要额外的单个更新某一行，直接开线程一直刷新就可以了。(当然也建议减少重复，节省少量性能上的浪费)

###记分板事件
###记分板风格
###修改玩家头上的名字
###玩家头上名字下的信息
###轮不到Kettle出手的tab列表



## 自定义击退系统(墨瓷原创算法)
```java
public static void setKb(User u, Modes m) {
	KnockbackManager kbm = u.getSettings().getKnockbackManager();
	kbm.setKnockback(double motX, double motY, double motZ, double airMotX, double airMotY, double airMotZ, double random);
	//也可以这样单独修改数值或修改高级数值
	kbm.motX = 0.875;
	kbm.shorten = 0.9;
}
```
下面对KnockbackManager的几个参数进行讲解(语文书警告)
####motX 地面X轴偏移倍数
当玩家在地面时原版x轴击退值与该值的乘积,该值一般与 *地面Z轴偏移倍数* 相差不大
####motY 地面Y轴偏移倍数
当玩家在地面时原版x轴击退值与该值的乘积,数值越大玩家离开地面的高度越高
####motZ 地面Z轴偏移倍数
当玩家在地面时原版x轴击退值与该值的乘积,该值一般与 *地面X轴偏移倍数* 相差不大
####airMotX 空中X轴偏移倍数
当玩家在空中时原版x轴击退值与该值的乘积,该值一般与 *空中Z轴偏移倍数* 相差不大
####airMotY 空中Y轴偏移倍数
当玩家在空中时原版x轴击退值与该值的乘积,数值越大玩家腾空的高度越高
####airMotZ 空中Z轴偏移倍数
当玩家在空中时原版x轴击退值与该值的乘积,该值一般与 *空中X轴偏移倍数* 相差不大
####random 随机偏移量
在以上参数标准下再向正负方向偏移的随机值,该参数使每次相同的击退条件下有一点点随机的差异。
过小会使击退显得过硬，过大会使击退呈现出“乱飘”的情况
<br>
除此之外KnockbackManager还有四个参数，有点难解释
####selfMin 己方最小连击缩距次数
在自己受到几次连击后开始缩短击退距离,该参数可以在双方开打后缩短击退距离，以更好地连击而不至于击退很远
该参数需要配合 *对方最小连击缩距次数* 参数一起使用
该参数不能大于 *己方最大连击缩距次数*
####targetMin 对方最小连击缩距次数
在对方受到几次连击后开始缩短击退距离,该参数可以在双方开打后缩短击退距离，以更好地连击而不至于击退很远
该参数需要配合 *己方最小连击缩距次数* 参数一起使用
####selfMax 己方最大连击缩距次数
在自己受到几次连击后取消缩短击退距离,该参数可以在自己被暴打后让自己被击退很远而不至于继续被暴打
该参数不能小于 *己方最小连击缩距次数*
####shorten 缩距倍数
以上条件符合后，缩短击退距离的倍数
把该参数调为1以取消这四个参数的作用
<br>
对以上参数的体验请到[DragonRichi自定义模式](https://dragonrichi.pro/还没做)中尝试，其中提供了GUI方便调整


## 聊天系统
如果想在玩家聊天信息中在名字前面添加一个前缀
```java
    @EventHandler
    public void onChat(AsyncPlayerChatEvent e) {
        e.addPrefix(FFAManager.getFFAStats("§f比如这个前缀§7", -100);
//后面的数字是优先度，由于一些情况下不止一个前缀，所以要一个值来分前缀的先后
    }
```
在Kettle中默认添加了两个前缀，其中：
玩家的rank前缀优先度是**0**，玩家的自定义前缀优先度是**-50**
前缀会从小到大排列，如上面就是自定义前缀在前，rank前缀在后

Kettle有自动将符号&转为Minecraft颜色符号§，但前提是玩家拥有在聊天中使用的颜色。

## 经济系统
目前全平台通用的是Coins和Credit。
你也可以自行用UserHandler写新的货币哦。
通过`user.getSettings().getVaultManager()`即可对货币进行管理
*注意*，如果奖励货币需要对Rank进行判断翻倍给的话（最好做一下，氪金嘛），无需自行判断
下面给出用KettleAPI的处理方式
```java
	int coins = p.getSettings().getVaultManager().addCoinsConsiderRank(100);//得到的coins是根据rank翻倍后的，如Wither Rank得到的是200
	double times = p.getSettings().getVaultManager().getTimesRankIncrease();//如果需要知道可以翻几倍可以用这个函数(用来提示玩家或者做自己的货币) 顺便教一下语言系统还可以这样玩
	if(times == 1)
		p.getUser().sendText(Message.giveCoins).replace("%(Coins)",String.valueOf(coins)).commit();
	else
		p.sendMessage(p.getUser().sendText(Message.giveCoins).replace("%(Coins)",String.valueOf(coins)).buildString() + p.getUser().sendText(Message.times).replace("%(Time)",times).buildString);
		//或者不翻译版本 p.sendMessage("你获得了"+coins+"金币 ("+times+"倍)");
```




## 为你的游戏加上Kettle跨服特效包(恰饭警告)
Kettle准备了一些效果来改善游戏的视觉体验，这些往往可以跨服使用，往往可以~~骗钱~~恰饭，但是内容繁多，自己写的话很耗时费力。在Kettle只需几行代码即可在自己的游戏中加入这些效果。
### 杀敌消息(使用用户的特殊武器作为武器消息)
### 玻璃边界

##其他不是很常用的大系统
### 社交系统

### Commons Core里的内容
由于这些不常用的内容特别大，所以移除出了Kettle，如果需要用到的话需要获取这些独立的库，[点击这里下载](https://jenkins.carmwork.com/job/Common)
这些库也是Kettle插件，在使用这些库的时候要放进plugin里加载这些插件，并在自己插件的plugin.yml中声明依赖
```yaml
name: MyPlugin
depend: [A,B,C...]
```
#### PermDog权限系统
导入PermDog-1.0-SNAPSHOT.jar包并在plugin.yml中添加依赖"PermDog"
#### IG反作弊系统
#### DNF插件安装系统
我暂时没做好。

### 数据库功能

##其他方便的小功能

### Kettle类
`Kettle::getVersionFromProtocolNumber(int number)`由protocol号码判断版本号
`Kettle::account`获取离线玩家数据(从墨瓷获取inkID、Email、玩家是否是正版，从mojang获取不一定进过墨瓷的正版玩家信息等)
如`Kettle.account.getName(inkID);`


### 获得每个服务器玩家的数量

请不要使用BCChannel去获得玩家数量，而是通过Common里 `PlayerCounterRunnable.get("SERVERNAME");` 获得。

### SkullManager(可以获取一定皮肤的头颅)

/kettlegui kettle 打开。

### 打开内购系统的方法

### Team功能










---

以上，是墨瓷的开发要求与文档。
编写 @cam @LSeng.
允许墨瓷成员和玩家提供修改意见。

