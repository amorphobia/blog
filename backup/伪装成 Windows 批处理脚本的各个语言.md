## 起因

当初使用 PowerShell 脚本为 [星空键道](https://github.com/amorphobia/rime-jiandao) 写了一个安装器，奈何 Windows 上为了安全，`.ps1`文件无法双击运行。后来在 [Stack Overflow](https://stackoverflow.com/a/57572270) 上找到一个方法，可以将 PowerShell 脚本伪装成批处理脚本，扩展名改为`.bat`后就可以双击运行了。

下面是代码：

```PowerShell
<# :
  @echo off
    powershell /nologo /noprofile /command ^
        "&{[ScriptBlock]::Create((cat """%~f0""") -join [Char[]]10).Invoke(@(&{$args}%*))}"
  exit /b
#>

Write-Host Hello, $args[0] -fo Green
# * Your program goes here * ...
```

## 简单分析

可以明显看出，代码分为两个部分，第一部分是从`<# :`到`#>`，第二部分就是余下代码。

这段代码进入命令提示符后，首先遇到了`<# :`，批处理脚本会将其当作一个标签，因此不会执行任何指令；而随后的命令则是把当前脚本转换成 PowerShell 的“脚本块”，附上所有参数来运行；从`#>`开始都是非法的批处理脚本，但接下来便执行了退出命令，因此它们不会被执行。

进入 PowerShell 后，会从头执行脚本，由`<#`和`#>`包围的部分是 PowerShell 中的块状注释，会被直接忽略；随后才会执行真正的业务逻辑。

## 其他语言

可以发现，伪装成批处理的方法就是把批处理的脚本藏在真正执行语言的**注释**或者**非活动块**中，并在调用真正的解释器后退出命令提示符。经过搜索和尝试，我找到了其他一些语言的伪装代码，这里不加解释地列出。

### AutoHotkey

```AutoHotkey
::a::a ^
/*
    @autohotkey "%~f0" %* & exit /b
*/
Hotstring "::a", , "Off"
MsgBox("Hello, AutoHotkey!")
ExitApp
```

### Node.js

```JavaScript
0<0// : ^
/*
@node "%~f0" %* & exit /b
*/
console.log("Hello, Node.js!");
```

### Python

#### 命令行

```Python
0<0# : ^
'''
@python "%~f0" %* & exit /b
'''
print("Hello, World!")
```

#### tkinter 图形界面

```Python
0<0# : ^
'''
@start /b pythonw "%~f0" %* & exit /b
'''
from tkinter import *
root = Tk()
a = Label(root, text = "Hello, Tkinter!").pack()
root.mainloop()
```

### Bash

#### Git bash

```bash
: '
@bash "%~f0" %* & exit /b
'
echo "Hello, Bash!"
```

#### WSL bash

```bash
: '
@set foo="%~f0"
@set "foo=%foo:\=/%"
@for /f "delims=" %%a in ('bash -c "wslpath ''%foo%''"') do @set self=%%a
@bash %self% %* & exit /b
'
echo "Hello, WSL!"
```

### Tcl

#### 命令行

```tcl
::if no {
    @tclsh "%~f0" %* & goto :eof
}
puts "Hello, Tcl!"
```

#### Tcl/Tk 图形界面

```tcl
::if no {
    @wish "%~f0" %* & goto :eof
}
button .hello -text "Hello, Tcl/Tk!" -command { exit }
pack .hello
```

## 还有吗？

以上是我自己尝试的一些脚本语言，应该还有更多的语言可以如此“伪装”，待以后再探索。