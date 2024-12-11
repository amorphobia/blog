## 定义

> [自产生程序](https://zh.wikipedia.org/zh-cn/%E8%87%AA%E7%94%A2%E7%94%9F%E7%A8%8B%E5%BC%8F)（英语：Quine），指的是输出结果为[程序](https://zh.wikipedia.org/zh-cn/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A8%8B%E5%BA%8F)自身[源码](https://zh.wikipedia.org/zh-cn/%E6%BA%90%E4%BB%A3%E7%A0%81)的程序。其英文名称以美国哲学家[奎恩](https://zh.wikipedia.org/zh-cn/%E5%A8%81%E6%8B%89%E5%BE%B7%C2%B7%E8%8C%83%C2%B7%E5%A5%A5%E6%9B%BC%C2%B7%E8%92%AF%E5%9B%A0)（Willard Van Orman Quine）命名，能够直接读取自己源码、读入用户输入或空白的程序一般都不视为自产生程序。

## AutoHotkey 的 Quine

在 Rosetta Code 的 [Quine](https://rosettacode.org/wiki/Quine) 页面，列出了很多语言写的“自产生程序”。其中就有我使用的 AutoHotkey[^1]：

```AutoHotkey
FileRead, quine, %A_ScriptFullPath%
MsgBox % quine
```

然而这段代码是“作弊”的，因为 Quine 不允许查看自身代码也就不能使用 `FileRead`。

我尝试着用 AHK v2 写出了真正的 Quine：

```AutoHotkey
s:="s:={1}{2}{1},MsgBox(Format(s,Chr(34),s))",MsgBox(Format(s,Chr(34),s))
```

运行效果如图

![AutoHotkey Quine](https://github.com/user-attachments/assets/992473a6-f2ed-4be4-88b4-1be36124ed0f)


[^1]: https://rosettacode.org/wiki/Quine#AutoHotkey