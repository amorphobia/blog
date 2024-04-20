# 一行命令完成需求

近日在处理文件差异的时候搜索到的一个方法，可以用 vim 把差异导出为 HTML ([来源](https://stackoverflow.com/a/68465366/6676742))

```sh
vimdiff -c TOhtml -c "w vimdiff_export.html | qa!" file1 file2
```

使用 `vimdiff` 对比两个文件，依次执行 `TOhtml`、保存、全部退出。

---

> 2024-04-20 更新

# 解决横向滚动同步问题

Vim 保存的网页在表格中使用了左右两个 `<div>`，可以自然地同步纵向的滚动，但横向的滚动并不同步。搜索一番后，我在 stackoverflow 上找到了[这个回答](https://stackoverflow.com/a/41998497/6676742)。稍作修改，把如下代码放到 `body` 的末尾，就能同步两边的横向滚动了。

```html
<script type="text/javascript">
var isSyncingLeftScroll = false;
var isSyncingRightScroll = false;
var leftDiv = document.getElementsByTagName('div')[0];
var rightDiv = document.getElementsByTagName('div')[1];

leftDiv.onscroll = function() {
  if (!isSyncingLeftScroll) {
    isSyncingRightScroll = true;
    rightDiv.scrollLeft = this.scrollLeft;
  }
  isSyncingLeftScroll = false;
}

rightDiv.onscroll = function() {
  if (!isSyncingRightScroll) {
    isSyncingLeftScroll = true;
    leftDiv.scrollLeft = this.scrollLeft;
  }
  isSyncingRightScroll = false;
}
</script>
```

## 缩短代码以便在 shell 脚本里完成

虽然这段代码已经够短了，但我还是把它缩得更短了：

```html
<script>m=!1,s=!1,a=document.getElementsByTagName("div"),l=a[0],r=a[1];l.onscroll=function(){m||(s=!0,r.scrollLeft=l.scrollLeft),m=!1},r.onscroll=function(){s||(m=!0,l.scrollLeft=r.scrollLeft),s=!1}</script>
```

这样，在我的 shell 脚本里，只需三行就可以满足需求：

```bash
SRC='<script>m=!1,s=!1,a=document.getElementsByTagName("div"),l=a[0],r=a[1];l.onscroll=function(){m||(s=!0,r.scrollLeft=l.scrollLeft),m=!1},r.onscroll=function(){s||(m=!0,l.scrollLeft=r.scrollLeft),s=!1}</script>'
vimdiff -c TOhtml -c "w vimdiff_export.html | qa!" file1 file2
sed -i '/<\/body>/i'"${SRC}" vimdiff_export.html
```
