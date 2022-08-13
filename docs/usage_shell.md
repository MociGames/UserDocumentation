# Shell 插件

本插件已发布到 [MCBBS](https://www.mcbbs.net/forum.php?mod=viewthread&tid=799034) 。

## 简介
这是一个在游戏中执行终端命令的插件，经测试支持**Windows**和**Linux**。
*(PS:我没有MacOS的工作站，所以无法测试MacOS的兼容性。但理论上是支持的。)*
实现方法相当简单，但我不知道为什么国内论坛上没有同款插件。
方法可能与某些已经发布的(但我并没找到)插件雷同，但我可以承诺本插件由我个人编写，**无任何抄袭内容**。

另需注意的是，**JVM无视权限**。所以使用本插件的话一定要注意安全。且不要乱给权限，否则换来的可能是“**rm -rf**”。


## 指令和权限
```
/run <指令>  
默认权限 moci.shell (可在Config中修改)
在终端中执行一条指令。
```

## 配置文件
```
#使用/run命令的权限。
permission: moci.shell
#是否只允许后台输入。
onlyCONSOLE: false

#是否输出结果到后台。
output: true

```

## 实际使用
本插件按照拓扑服需求所写。
以下为他们的使用方法。
```
//自动备份world并打包
run zip -i backups/world.zip world/* world/*/*
```
当然，你也可以直接运行**.sh**文件。
```
//例如： 我在服务器根目录下创建了 scripts的文件夹，并写了一个叫做backup.sh的可执行文件。
run bash ./scripts/backup.sh
```


## 源代码
本插件**开源**。
您可以访问我们的 [**Git**(/Share/Shell)](https://git.mocimc.cn/share/Shell) 来查看本插件源码。
这里也直接放出关于执行命令的代码，方便您使用。
```
    /**
     * 使用异步执行命令。
     *
     * @param cmd 所要执行的命令。
     */
    public static void run(String cmd) {
        new BukkitRunnable() {
            @Override
            public void run() {
                exec(cmd);
                cancel();
            }
        }.runTaskAsynchronously(Main.getInstance());
    }

    /**
     * 执行命令。
     *
     * @param cmd 所要执行的命令。
     * @return 输出结果。
     */
    public static Object exec(String cmd) {
        try {
            Process process = Runtime.getRuntime().exec(getCMD(cmd));
            LineNumberReader br = new LineNumberReader(new InputStreamReader(
                    process.getInputStream()));
            StringBuilder sb = new StringBuilder();
            String line;
            while ((line = br.readLine()) != null) {
                if(Config.output){ //判断是否开启了output
                    System.out.println(line);
                }
                sb.append(line).append("\n");
            }
            return sb.toString();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 得到当前系统所执行command的格式。
     *
     * @return 转码完成的CMD。
     */
    private static String[] getCMD(String s) {
        if (SystemOS.isWindows) {
            return new String[]{"cmd /c " + s};
        } else {
            return new String[]{"/bin/sh", "-c", s};
        }
    }
```

关于调用的SystemOS和Config，请见我们的 [**Git**(/Share/Shell)](https://git.mocimc.cn/share/Shell)。

- - - - -

##条款
作者@cam。
1. 您不允许 转载/发布再次或重新声明作者为他人。[我们将会追究责任]
2. 本人有权利拒绝任何无理由的栽赃。且不对任何本插件造成的损害负责。（会造成损害？）
3. 本人不可以完全保证本插件与其他的兼容性。但目前暂未发现。
4. 本人随时可以发布本插件到其他网站。
5. 本插件为非盈利性插件，免费发布，严禁销售和转卖。

另： 本插件主要用于给新手学习，可能不是最优化的代码。如果代码有雷同的，是巧合。




