# 权限狗开发文档

##简介
鉴于墨瓷玩家数据的特殊性，也为了日后更方便的管理，我们需要一个简单的、支持InkID(墨码)的权限组插件。

我们不需要多余的功能。仅用其设置默认组和给特别玩家特别的权限。
由于[Kettle](dev_kettle.md)的高优先度，我们甚至不需要前缀/后缀系统。

本插件的用意在于管理全服的权限。对于个别需要额外权限的服务器，我们则采用[PermissionEx](https://dev.bukkit.org/projects/permissionsex)来作为第二权限组插件。

经过商讨，定名本插件为“**权限狗**(**PermDog**)”

**注意： 此文档并非最终开发文档！**

##功能

###对插件的管理

```
/perm extands add <库名>
/perm extands remove <库名>
/perm extands list

/perm reload
/perm updata
```

###对组的管理

```
//通用指令 
/perm group <组名> [操作] [数值] [库名(默认为@)]
//具体
/perm group <组名> [(null)/user(s)/perm(mission)(s)] //查看数据
/perm group <组名> create //创建一个新的组
/perm group <组名> remove //删除一个组
/perm group <组名> perm(mission)(s) add [权限] //增加一个权限
/perm group <组名> perm(mission)(s) remove [权限] //移除一个权限
/perm group <组名> user(s) add [用户名] //增加一个用户
/perm group <组名> user(s) remove [用户名] //移除一个用户
```

###对用户的管理

```
//通用指令 
/perm user <用户名> [操作] [数值] [库名(默认为@)]
//具体
/perm user <用户名> //查看一个用户的数据
/perm user <用户名> perm(mission)(s) add [权限] //增加一个权限
/perm user <用户名> perm(mission)(s) remove [权限] //移除一个权限
/perm user <用户名> group(s) add [组名] //将用户添加到某个组
/perm user <用户名> group(s) remove [组名] //从一个组中移除
```

##方法
```
PermManager pm = Kettle.getPermDog(); 

pm.update(); //更新所有用户和组的权限数据。

pm.createGroup(String 组名); //新建组
pm.removeGroup(String 组名); //移除组

pm.createTempGroup(String 组名); //新建临时组 (只存储与内存中)
pm.removeTemproup(String 组名); //移除临时组 (只存储与内存中)

//更新一个组的数据。
//如我创建了一个临时的组，现在我想让他更新到数据库中变成正式组，
//那么我就可以使用这个方法。
//或者，我想即时更新一个组的数据(因为权限默认刷新的时间为5分钟)，
//我也可以使用这个方法。
pm.updateGroup(String 组名); 

pm.getGroups(); //返回Group的List<>;
PermissionGroup pg = pm.getGroup(String 组名);
pm.updateUser(User 用户); //更新一个用户的数据
PermissionUser pu = pm.getUser(User u);

pg.getUsers(); //得到组内的所有用户
pg.getPerms(); //得到改组的所有权限
pg.addUser(PermissionUser 用户); //增加一个用户
pg.removeUser(PermissionUser 用户); //移除一个用户
pg.addUsers(List<PermissionUser>); //增加一堆用户
pg.removeUsers(List<PermissionUser>);//移除一堆用户
pg.addPerm(String 权限);//增加一个权限
pg.removePerm(String 权限);//移除一个权限
pg.addPerms(List<String> 权限);//增加一堆权限
pg.removePerms(List<String> 权限);//移除一堆权限

pu.getGroups(); //得到用户所在的所有组
pu.getPerms(); //得到该用户的所有权限
pu.addGroup(pg); //将用户添加到某个组
pu.removeGroup(pg); //从一个组中移除
pu.addPerm(String 权限);//增加一个权限
pu.removePerm(String 权限);//移除一个权限
pu.addPerms(List<String> 权限);//增加一堆权限
pu.removePerms(List<String> 权限);//移除一堆权限
```

##数据库格式
###Users表
| id |  inkid | type |value | base | 
| --- |  --- | --- | --- | --- |
| 1 | 3 |  group | owner| @ |
| 2 | 3 |  perm | moci.owner | @ |
| 3 | 3 |  perm | moci.dnf | @ |

###Group表
| id | groupname | type |value | base | 
| --- | --- | --- | --- | --- |
| 1 | admin | extands | owner | @ |
| 2 | owner | perm | moci.owner | @ |
| 3 | owner | perm | moci.shutdown | @ |

##结语

对外公开，作者@cam。
希望能给那些做权限组插件的中国开发者一点思路。