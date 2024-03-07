记录一下是如何设置 Home Assistant 的吧。没有什么难点的部分就不写了，反正 Google 一下就找得到。

## 安装 Home Assistant

Google 之。

## 配置 Home Assistant 中的 HomeKit

最简配置，添加到 `configuration.yaml` 里面。

```
homekit:
  auto_start: False
```

自动启动关闭，否则 HomeKit 组件在设备还未接入完全的时候启动，在家庭 App 里就找不到设备。

相反，在 `automations.yaml` 里面加入以下代码，在 Home Assistant 启动五分钟后再启动 HomeKit 组件。

```
- id: '$IDOFAUTOMATION'
  alias: Start HomeKit
  trigger:
  - event: start
    platform: homeassistant
  condition: []
  action:
  - delay: 00:05
  - alias: ''
    data: {}
    service: homekit.start
```

## 接入设备

### Yeelight 吸顶灯

Yeelight 吸顶灯可以在 App 里打开局域网控制，在启动 Home Assistant 的时候就会自动搜寻并添加，五分钟后启动 HomeKit 就能在 App 上出现了。

### 彩云天气

插件地址 [caiyun.py](https://github.com/Yonsm/HAExtra/blob/master/custom_components/sensor/caiyun.py)。复制到 `custom_components/sensor/` 里面，然后在 `configuration.yaml` 配置

```
sensor:
  - platform: caiyun
```

也可以顺便把默认的天气插件 `yr` 关掉。

### 米家空调伴侣 (lumi.acpartner.v2)

由于前期调查没做好，买了个不支持[局域网控制协议](http://docs.opencloud.aqara.cn/development/gateway-LAN-communication/)的版本，无法将其当作网关加入 Home Assistant，只能当作空调加入。

首先获取设备的 token，参考[这篇教程](https://github.com/jghaanstra/com.xiaomi-miio/blob/master/docs/obtain_token.md)。

然后使用[这个插件](https://github.com/syssi/xiaomi_airconditioningcompanion)添加，使用方法见其 README。

### 石头扫地机器人 (roborock.vacuum.s5)

获取 token 的方法与空调伴侣相同，然后参照[官方教程](https://www.home-assistant.io/components/vacuum.xiaomi_miio/)添加到 Home Assistant。值得注意的是，扫地机器人不适用于 HomeKit 组件，有待以后研究其他解决方案。

最后看看成果吧：

![Home Assistant 截图](https://github.com/amorphobia/blog/assets/523025/0fd78cdf-4474-41b4-acda-c23bd7a3aa4e)
![家庭 App 截图](https://github.com/amorphobia/blog/assets/523025/fea8e83b-bab1-438a-bb67-9c15b05c39a0)

<!-- ##{"timestamp":1546604081}## -->