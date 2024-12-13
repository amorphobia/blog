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
s:="s:={:c}{}{1:c},MsgBox(Format(s,34,s))",MsgBox(Format(s,34,s))
```

运行效果如图

![AutoHotkey Quine](https://github.com/user-attachments/assets/2f895589-aabc-474a-87fa-a5f7f3fabb2e)

## 另一个思路

在知乎上看到了一个 Quine 的构造方法[^2]，算是比较通用的方式，有一点编译器自举的既视感，尝试用 AutoHotkey 实现了一遍。

首先这个方法语言支持匿名函数，这在 AutoHotkey 里叫做“[胖箭头函数](https://www.autohotkey.com/docs/v2/Variables.htm#fat-arrow)”。用其构造一个函数，可以连续打印两遍输入，第一遍使用括号将其包含，第二遍使用括号和引号将其包含。

```AutoHotkey
quine := (s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41))

quine("q")       ; 打印 (q)("q")
quine("quine")   ; 打印 (quine)("quine")
(quine)("quine") ; 打印 (quine)("quine")
```

这种 `(q)("q")` 的形式就表达了“声明一个函数对象，并把声明的字符串传入自身调用”。因此，我们接下来把第一行整句作为字符串传入 `quine` 函数来调用：

```AutoHotkey
; 打印 (quine := (s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41)))("quine := (s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41))")
(quine)("quine := (s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41))")
```

打印的结果就是一个自产生程序，最后，由于函数对象声明后立即调用，可以省去变量赋值而直接使用其匿名形式：

```AutoHotkey
((s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41)))("(s) => MsgBox(Chr(40) s Chr(41) Chr(40) Chr(34) s Chr(34) Chr(41))")
```

![Another Quine](https://github.com/user-attachments/assets/4732177e-2d29-4aeb-ba07-dd04000499a1)


[^1]: https://rosettacode.org/wiki/Quine#AutoHotkey
[^2]: https://www.zhihu.com/question/38145793/answer/984510905