如果你需要一款支持多配置（订阅）、仅提供命令行界面的 mihomo 管理工具，那么 [mihomosh](https://github.com/SamuNatsu/mihomosh) 会比较合适。

## 安装

mihomosh 本身不包含内核，需要单独[安装](https://wiki.metacubex.one/startup/)。如果你的包管理器同时提供 mihomo 和 mihomosh，安装会更方便。

### Arch Linux

```
paru -S mihomo mihomosh
```

### Windows（scoop）

```
# mihomosh 尚未收录到 main bucket, 可添加 siku bucket
scoop bucket add siku https://github.com/amorphobia/siku
scoop install mihomo mihomosh
```

### 预编译二进制

（[mihomo](https://github.com/MetaCubeX/mihomo/releases), [mihomosh](https://github.com/SamuNatsu/mihomosh/releases)）下载对应系统的二进制文件后，将其所在路径加入环境变量，便可直接调用。

## 初始化

> 为便于区分，下文将 mihomo 称为“内核”（kernel），mihomosh 称为“外壳”（shell）。 

外壳通过内核提供的控制 API 工作，因此需要先启动内核，并开启外部控制。一般可以在 `$HOME/.config/mihomo` 目录下新建 `config.yaml`，写入：

```yaml
external-controller: '127.0.0.1:9090'
```

然后使用以下命令启动内核：

```
mihomo -d ~/.config/mihomo
```

启动完成后，后续配置和管理都可以通过外壳完成。如果你有常用编辑器，可以先设置环境变量 `EDITOR`，这样外壳会调用该编辑器。执行以下命令编辑外壳配置：

```
mihomosh config edit
```

根据实际情况修改并保存，例如：

```yaml
mihomo-path: /home/alp/.config/mihomo/config.yaml
mihomo-api: http://127.0.0.1:9090
log-level: info
mode: rule
mixed-port: 7890
allow-lan: true
```

需要注意，外壳会覆盖掉 `$HOME/.config/mihomo/config.yaml`，因此需要在外壳中[再次设置“外部控制”](https://github.com/SamuNatsu/mihomosh/issues/12#issuecomment-3677838607)。执行：

```
mihomosh profile egxc
```

在打开的编辑器中写入以下内容并保存：

```yaml
external-controller: '127.0.0.1:9090'
```


## 添加订阅

接下来创建配置（订阅）：

```
mihomosh profile create
```

根据编辑器中的注释填写内容并保存。如果是订阅，`type` 应设为 `remote`。 需要注意，当前版本中用于更新订阅的代理只能设置为 `none`、`system` 或 `mihomo`。如果系统中暂时没有可用代理，订阅下载可能会失败。可以先手动下载订阅文件，再导入使用；之后更新时即可通过内核自身的代理完成。因此这里建议将 `proxy` 设置为 `mihomo`。

外壳默认的配置目录为：

```
$HOME/.local/share/mihomosh/profiles/
```

目录中会生成一个 `<profile-id>.yaml` 文件，即刚创建的配置。若你通过其他方式下载了订阅文件，可以将其保存为 `<profile-id>.data.yaml`，然后执行：

```
mihomosh profile update <profile-id>
```

完成后，订阅即添加成功。

## 常用命令

- 更新订阅：`mihomosh profile update <profile-id>`（省略 `<profile-id>` 表示更新全部）
- 切换订阅：`mihomosh profile activate <profile-id>`
- 查看内核日志：`mihomosh inspect log`
- 列出代理：`mihomosh proxy view`
- 选择代理：`mihomosh proxy update <proxy-name>`

更多命令可通过添加 `--help` 参数查看说明。
