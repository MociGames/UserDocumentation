# 墨瓷开发 (InkKettle 墨桶)

标签： 墨瓷开发文档 InkKettle Java
**注意： 此文档并非最终开发文档！**

---
##简介
  InkKettle，是我们的第一个服务端。基于git-Paper，并在此基础上而外增加和删减内容，来陪合我们的实际要求。预计开发周期为两个月。
  InkKettle的中文译名墨桶，将作为**墨瓷平台**的主力军。它将内置~KarCore~基础内容，并加以完善。于是，所有的开发者将拥有更高权限和更多的自定义化内容，便于给玩家提供更好的体验。
       
---

## 内容
### 代码规范
* 包、类名规范
 -  包名统一采用cn.moci.*，便于构建器收取。导用的APIs独立内置。
 -  类名开头大写，字母开头大写。所有的事件、管理器、核心独立一个包。
* 方法名规范
 - 布尔方法统一 is、can、has开头,部分需要使用get，如 
  `u.getVersionManager().isOnlineMode();`
  `u.getPrpfileManager().getSettings(SettingsType.hidePlayer);`
 - String、int、Double、Object 统一get开头， 如 `u.getProfileManager().getLanguage();`
 - 新建、移除一个Object 统一使用 create 和 remove 开头，如
 `u.getDataManager().createHandler(“Mode”, “NONE”);   `
 `u.getDataManager().removeHandler(“Mode”);`
 - 修改一个Object统一使用 modify开头，如
 `u.getDataManager().modifyData(“Point”, “66”);`

## 系统
### User (用户) 系统列表
* Managers 管理器
  - [ ] 【！】Data Manager (Handlers,特殊币种,特殊数据)
  - [ ] 【！】Version Manager(Online Mode, GameVersion,Forge Mods,etc)
  - [ ] 【！】Profile Manager(Languages,Settings,etc.)
  - [ ] Permission Manager（内置权限系统）
  - [ ] Vault Manager(Coins,Credit)
  - [ ] Karma Manager(信用系统独立）
  - [ ] Gui Manager(Different GUIs)
  - [ ] Knockback Manager
  - [ ] Message Manager（各消息独立 如分为、游戏信息、 提示信息、公告信息等）
  - [ ] Package Manager （发包管理）
* Events 事件
  - [ ] UserChatEvent
  - [ ] UserDataUpdateEvent
  - [ ] UserLoadEvent
  - [ ] UserUnloadrEvent
  - [ ] UserHandlerUpdateEvent
  - [ ] UserProfileUpdateEvent
  - [ ] UserHackingEvent
  - [ ] UserKnockbackEvent
  - [ ] UserMessageEvent
  - [ ] UserGUIOpenEvent
  - [ ] UserVaultUpdateEvent
  - [ ] UserKarmaUpdataEvent
  - [ ] UserPermissionUpdataEvent

### 服务器系统列表
  - [ ] 修改Plugins指令，实现公开插件。
  - [ ] 修改Stop指令为shutdown，防止误操作。
  - [ ] 修改击退，并读取User被设定的击退数据。该数据需要在每次玩家进入时设置初始值，并可以更改。
  - [ ] 修改Player，增加`player.getUser();`方法。
  - [ ] 创建属于User的类似于UUID的编号，便于管理。
  - [ ] 假人系统。
  - [ ] LogManager，用于存储服务器所有的错误、崩溃信息、玩家指令、攻击、聊天消息到一个可观的文件中。
  - [ ] 测试台。测试过程中不允许没有特定标签的玩家进入。
  - [ ] 黑客专服
  - [ ] ELO匹配系统。

## 详解
注意，详解中的内容**会根据日后实际需要增减和删减**。尽量在第一次开发时考虑到更多情况。
###Data Manager 【！】
DataManager是几乎所有功能的基础。它负责与MySQL、BC的数据交换和更新。
主要的方法有
```java
//仅为方法名，不包含数据名，可修改（我记不住了。）
public void put();
public void search(); //搜寻一条数据，得到值
public void remove(); //一条数据
public void delete(); //删除一个表。

public void loadUserData(); 
public void clearUserData(); //清除一个User在所有数据库的数据，并备份到Backup库。

//必备的事件。
    UserDataUpdateEvent
    UserLoadEvent
    UserUnloadEvent
```
通过以上方法，可为所有的数据操作搭桥，实现简单操作，便于开发。

### Version Manager 【！】
VersionManager负责着玩家关于客户端、版本、正盗版等个人因素的数据。
主要的方法有
```java
//仅为方法名，不包含数据名，可修改（我记不住了。）
public void getClientVersion(); //ClientStats可实现。
public boolean isOnlineMode(); //获得是否为正版
public void getForgeMods();
public boolean isUsingForge(); //清除一个User在所有数据库的数据，并备份到Backup库。
```
VersionManager是墨瓷**最基础的功能之一**。它的稳定性保持这墨瓷玩家的正常加入和数据的保存。

### Profile Manager 【！】
ProfileManager负责这玩家个人信息(包含私人信息)及设定的更改与储存。
其中，关于密码的部分需加密。
主要的方法如下
```java
//仅为方法名，不包含数据名，可修改（我记不住了。）
public boolean getSettings(SettingsType.type); //真的有特殊布尔类型！！
public void updateSettings(SettingsType.type,Boolean vaule);
public String getInformation(UserInformation.TYPE);
public void updateSettings(UserInformation.TYPE,Boolean vaule);
public void getUserType();
//各类型
SettingsType:
    各类设置不列举。
    但我希望这个类型可以让开发者新建！
UserInformation:
    邮箱
    密码 //是用户必须有密码和邮箱！
UserType:
    Player //普通玩家以及未在执法的管理者
    Staff //正在执法的管理者
    Hacker //黑客
    Unlogin //未登录或注册的玩家。同常此类玩家不存在，但留有此列表便于开放测试模式。
//必备的事件。
    UserLoginInEvent
    UserRegisterInEvent
    USerProfileUpdateEvent
```
ProfileManager是新玩家注册、登录的门。
Settings部分可放一放，但UserInformation部分务必迅速。

###Permission Manager
权限管理器，拥有PermissionEx的阉割内容。
增加事件`UserPermissionUpdateEvent`
###Karma、Vault Managers
同KarCore中的方法，增加两个事件。
```Java
  UserVaultUpdateEvent
  UserKarmaUpdataEvent
```

### GUI Manager
不同类型GUI，同KarCore，增加铁砧、发射器、投射器、漏斗、炼药台、附魔台的UI。

### Knockback Manager
KnockbackManager负责Knockback的修改。每个用户在进入服务器时会被定义击退，若不定义则为普通击退。
Knockback是可以修改的。在每一次修改数值时，KocbackManager会读取当前用户的击退数值，并反馈到attack()方法内。

### Message Manager
发送消息。
同KarCore一样。不过，这次的MessageManager需要区别各消息类型发送,并又更方便的方法。

### Package Manager
发包的API归类到这里即可。

---
以上，是Kettle的开发文档。
编写 @cam.
允许墨瓷成员和玩家提供修改意见。