近日在处理文件差异的时候搜索到的一个方法，可以用 vim 把差异导出为 HTML ([来源](https://stackoverflow.com/a/68465366/6676742))

```sh
vimdiff -c TOhtml -c "w vimdiff_export.html | qa!" file1 file2
```

使用 `vimdiff` 对比两个文件，依次执行 `TOhtml`、保存、全部退出。