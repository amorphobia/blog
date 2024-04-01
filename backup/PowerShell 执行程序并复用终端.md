最近偶然得知了一个用 Free Pascal 写的编辑器 [CudaText](https://cudatext.github.io/)，便安装来玩玩。

轻量编辑器我一直都是使用 Sublime Text，而且重度依赖从终端命令启动编辑器打开文本，Sublime Text 就有一个官方的命令行可执行程序 `subl.exe`。例如当前目录下有一个文本文档 `file.txt`，我可以使用命令 `subl file.txt` 快捷打开，这个可执行程序会使用新的进程来运行 Sublime Text，因此当前的终端可以继续使用，不受影响。

# 当前终端无法复用

但 CudaText 就不行了，虽然它的可执行程序 `cudatext.exe` 也支持参数，可以便捷打开文件或目录，但打开后会持续占用当前的终端，这一点令人很不爽。若是 *nix 系统，可在命令最后加一个 `&` 使其在后台运行，但 Windows PowerShell 似乎没有这样的功能。

# 使用 `Start-Process` 创建新进程运行

一个简单的方法是使用系统中已有的命令 `Start-Process` 来运行。还是刚才的例子：

```PowerShell
Start-Process cudatext file.txt
# 或者使用别名
start cudatext file.txt
```

如果你尝试了上面的命令则会发现，对于 CudaText 这类占用终端的软件，即使是 `Start-Process` 也会产生一个 conhost 窗口。
![cudatext-with-conhost-window](https://github.com/amorphobia/blog/assets/523025/e13ea403-3e5e-4651-9fbe-c1c91ff3c7d9)

所幸的是，`Start-Process` 支持 `WindowStyle` 参数，可以将其设置为 `hidden`，就不会显示 conhost 窗口了。

## 命令太长了

当然，这样的命令不适合日常使用，但我们可以写一个脚本，这样就可以使用很短的命令来运行程序了。

```PowerShell
# run.ps1
if ($args.Length -lt 1) {
    exit 0
}

if ($args.Length -gt 1) {
    $Arguments = $args[1..($args.Length - 1)] -join ' '
    Start-Process -WindowStyle hidden $args[0] $Arguments
} else {
    Start-Process -WindowStyle hidden $args[0]
}
```

把这个脚本另存为 `run.ps1`，放到任意一个 `$env:Path` 目录中，就可以使用 `run cudatext file.txt` 快捷打开了。虽然这个脚本很短，但菜鸡如我，一开始写了一个很长的版本，边写边查给改成这样的。

对于文本编辑器这种常用程序，可以更进一步为其编写一个专用脚本

```PowerShell
# ct.ps1
if ($args.Length -lt 1) {
    Start-Process -WindowStyle hidden cudatext.exe
} else {
    Start-Process -WindowStyle hidden cudatext.exe ($args -join ' ')
}
```

这样便可使用 `ct file.txt` 来打开了。

# 其他想法

在写脚本的过程中，也想过写成一个函数放到 [`$PROFILE`](https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-5.1) 里，但始终没搞定参数的问题，只好作罢，如果有夶䎜对 PowerShell 比较了解，请不吝赐教！