## 问题

我在 Windows 里日常用的是 Windows Terminal 中的 PowerShell。这就有一个问题，一旦创建了[配置文件](https://docs.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2)，PowerShell 启动速度就会[很慢](https://stackoverflow.com/questions/49053189/how-do-i-disable-personal-and-system-profiles-loading-time-message-on-powershell)，即使删除所有配置文件，启动速度也回不到从前。因此在最近一次重装系统后，我再也没搞过配置文件，我的 PowerShell 就一直保持秒开的速度。

## 想法

可是无配置文件的 PowerShell 的确是少了些方便，今天突发奇想，既然 PowerShell 自己读取配置很慢，那我能不能在启动它之后再指定一个配置文件，这个文件不在 PowerShell 默认的路径里，这样即使以后觉得速度太慢而删除它，也能恢复秒开。

## 思路

系统默认的配置文件路径分别是

- `$PSHOME\Profile.ps1`
- `$PSHOME\Microsoft.PowerShell_profile.ps1`
- `$Home\[My ]DocumentsPowerShell\\Profile.ps1`
- `$Home\[My ]DocumentsPowerShell\\Microsoft.PowerShell_profile.ps1`

挺好，那我在用户目录下创建一个 `.profile.ps1` 文件作为我的配置文件，就不会触发 PowerShell 的启动加载了。那么问题就变成了：

1. 如何加载这个文件？
2. 如何在启动后自动加载这个文件？
3. 还有其他问题吗？

## 解决

中间的尝试略过的话，最后总结了一下[解决方法](#总结)。

### 加载文件

由于我对 PowerShell 不熟悉，所以加载这个自定义的配置文件也算一个问题。在类 Unix 系统里加载 Shell 配置我是知道的，直接 `source <file>` 就成了。我来试试吧：

```PowerShell
PS C:\Users\amorphobia> source $HOME\.profile.ps1
source : 无法将“source”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径
正确，然后再试一次。
所在位置 行:1 字符: 1
+ source $HOME\.profile.ps1
+ ~~~~~~
    + CategoryInfo          : ObjectNotFound: (source:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

寄了呀！其实不试也猜得到，从来都没听说过 PowerShell 有 `source` 命令……那试试直接运行呢？

```PowerShell
PS C:\Users\amorphobia> $HOME\.profile.ps1
所在位置 行:1 字符: 6
+ $HOME\.profile.ps1
+      ~~~~~~~~~~~~~
表达式或语句中包含意外的标记“\.profile.ps1”。
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : UnexpectedToken
```

嗯……可能直接运行也不行吧？于是网上搜了一下，发现其实脚本是可以直接运行的，问题出现在这个 `$HOME` 环境变量，需要将其展开成完整的路径，才可以运行。那如果我不想展开呢？解决方案也有，就是使用 [`&` 操作符](https://ss64.com/ps/call.html)。赶快试试吧：

```PowerShell
PS C:\Users\amorphobia> & $HOME\.profile.ps1
PS C:\Users\amorphobia>
```

没有报错，而且配置生效了！撒花🎉🎉🎉

### 自动加载

由于我用的是 Windows 终端，能指定 PowerShell 启动时的参数，那么 PowerShell 应该也需要有类似 `sh -c <cmd>` 这样的命令，这回直接看看帮助吧：

```PowerShell
PS C:\Users\amorphobia> powershell -h

PowerShell[.exe] [-PSConsoleFile <文件> | -Version <版本>]
    [-NoLogo] [-NoExit] [-Sta] [-Mta] [-NoProfile] [-NonInteractive]
    [-InputFormat {Text | XML}] [-OutputFormat {Text | XML}]
    [-WindowStyle <样式>] [-EncodedCommand <Base64 编码命令>]
    [-ConfigurationName <字符串>]
    [-File <文件路径> <参数>] [-ExecutionPolicy <执行策略>]
    [-Command { - | <脚本块> [-args <参数数组>]
                  | <字符串> [<命令参数>] } ]
......
```

诶，有个 `-Command` 参数，讲了啥呢？

```PowerShell
-Command
    执行指定的命令(和任何参数)，就好像它们是
    在 Windows PowerShell 命令提示符下键入的一样，然后退出，除非
    指定了 NoExit。Command 的值可以为 "-"、字符串或
    脚本块。

    如果 Command 的值为 "-"，则从标准输入中读取
    命令文本。

    如果 Command 的值为脚本块，则脚本块必须
    用大括号({})括起来。只有在 Windows PowerShell 中运行 PowerShell.exe 时，
    才能指定脚本块。脚本块的结果将作为反序列化的 XML 对象
    (而非活动对象)返回到父 Shell。

    如果 Command 的值为字符串，则 Command 必须是命令中的
    最后一个参数，因为在命令后面键入的所有字符
    都将解释为命令参数。

    若要编写运行 Windows PowerShell 命令的字符串，请使用以下格式:
        "& {<命令>}"
    其中，引号表示一个字符串，调用运算符(&)
    导致执行命令。
```

哎呀，帮助里就提示了用 `&` 来运行命令。好的，加到 Windows 终端的设置里吧！

```Json
...
    "profiles": 
    {
        "defaults": {},
        "list": 
        [
            {
                ...
                "commandline": "powershell.exe -Command \"& $HOME\\.profile.ps1\"",
                ...
            },
            ...
        ]
    },
...
```

保存，关掉 Windows 终端，再启动。咦，窗口闪了一下就没了，怎么回事呢？原来还不行，有其他问题呀。

### 其他问题

想想 `sh -c <cmd>` 这个命令，也是运行完成就退出吧，要想运行之后进入交互模式，还得用 `sh -ic <cmd>` 才行。那么 PowerShell 呢？看看刚才的帮助：

```PowerShell
-NoExit
    运行启动命令后不退出。
```

就它了！在 Windows 终端的设置里把这玩意儿加上

```Json
...
    "profiles": 
    {
        "defaults": {},
        "list": 
        [
            {
                ...
                "commandline": "powershell.exe -NoExit -Command \"& $HOME\\.profile.ps1\"",
                ...
            },
            ...
        ]
    },
...
```

关掉再试试，成了！

回想一下我最初的需求，万一这东西速度慢了，不想要了，会不会和默认路径的配置文件删除后一样，速度恢复不了？既然我只是在 Windows 终端里干了这事，那就应该不会影响 PowerShell 本身吧。试了试不用 Windows 终端，直接启动 PowerShell 本体，速度依然飞快。嗯，目的达成了。

## 总结

想达成我的目的只需两步：

1. 创建一个自定义配置文件，不要放到 PowerShell 默认的路径，比如我的文件是 `C:\Users\amorphobia\.profile.ps1`
2. 在 Windows 终端配置里把 PowerShell 的启动命令改为 `powershell.exe -NoExit -Command "& $HOME\.profile.ps1"`，如果直接改 Json 配置要注意引号和反斜杠的转义

完了。

<!-- ##{"timestamp":1647486310}## -->