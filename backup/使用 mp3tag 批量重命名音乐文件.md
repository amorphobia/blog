最近在整理我的音乐库，一个比较大的改变是利用 `SUBTITLE` 标签，把歌曲名里的 `(Live)`、`(粤语版)` 这类信息从 `TITLE` 标签移出了。

修改了标签后，顺手就把文件名按照预设模板改成了 `1.02. 歌曲名.mp3` 这样的模式。但这样对于某些歌曲就不好从文件名区分了，比如同时有国语版和粤语版，文件名现在就只有曲目编号的区别，需要查看 ID3 标签才知道哪个是哪个。

于是我使用 [mp3tag](https://www.mp3tag.de/en/) 把副标题也加到文件名中，以便在文件系统里更好地管理。

```
$if(%discnumber%,$num(%discnumber%,1).,)$num(%track%,2). %title%$if(%subtitle%, ($meta_sep(subtitle,', ')),)
```

使用这个模板就可以把文件命名为 `1.02. 歌曲名 (副标题1, 副标题2).mp3` 这样的形式。其中用到的函数可在 [scripting 文档](https://docs.mp3tag.de/scripting/)页面查看。