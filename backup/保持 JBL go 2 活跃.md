自从我配了台式机以后，始终缺一个可靠的音箱。买过 2.0, 也买过 2.1 声道的，最终结果就是其中某个坏掉，或者整个都不行了。其实我对音箱的要求真的不高，能正常出声音、稳定、方便就行了。我也用过小米的蓝牙音箱连接电脑，可靠是可靠，就是连接不怎么方便。

后来机缘巧合入手了一个 JBL go 2 音箱，虽然是个蓝牙音箱，但同时支持 AUX 连接电脑，这才部分解决了方便性的问题。为什么说部分呢？因为这音箱有个脑残的“功能”——如果一段时间内没有播放任何声音，就会自动关机。

这个功能对于作为便携蓝牙音箱的情形还算是有用，自动关机节省电量，有需求的时候再开机。但是我是拿它当台式机音箱，充电线是始终连着的，不缺这点电呀……

## 解决方法

如何让这个音箱保持活跃呢？很简单，让它在关机之前播放声音就好了。通过多方搜索，我找到了 [求问如何避免蓝牙音箱自动关机。？ - 66666的回答 - 知乎](https://www.zhihu.com/question/41682642/answer/789367103) 这个答案。

那么接下来就简单了，写了个 [AHK](https://www.autohotkey.com/) 脚本，每分钟让系统发出一个人耳察觉不到的声音，顺利解决这个问题了！

```
Loop {
	RunWait, "%USERPROFILE%\scoop\apps\nircmd\current\nircmd.exe" beep 30 100 ;
	sleep, 60000 ;
}
```

编译成可执行文件放到启动里，电脑不关机，音箱就会一直待命了。

<!-- ##{"timestamp":1587646751}## -->