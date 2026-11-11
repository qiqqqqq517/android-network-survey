# Network Survey 安卓应用（简体中文版）

![Build Status](https://github.com/christianrowlands/android-network-survey/actions/workflows/android.yaml/badge.svg)
[![License](https://img.shields.io/badge/license-Apache%202-green.svg?style=flat)](https://github.com/christianrowlands/android-network-survey/blob/develop/LICENSE)
![Latest Release](https://img.shields.io/github/release/christianrowlands/android-network-survey.svg?logo=github)
[![Translation status](https://hosted.weblate.org/widget/network-survey-android-app/svg-badge.svg)](https://hosted.weblate.org/engage/network-survey-android-app/)

> **本仓库是修改版（2026-11-12）**：基于 Christian Rowlands 的
> [android-network-survey](https://github.com/christianrowlands/android-network-survey)（Apache License 2.0，
> Copyright 2019 Christian Rowlands）派生，仅做**简体中文全量本地化**与默认语言调整，未改动任何功能逻辑。
> 上游版权与许可声明完整保留；本修改版由本仓库维护者发布，与上游作者无隶属关系。
> 英文原文见 [`README.md`](README.md)；本中文版若与英文原文有出入，以英文原文及上游仓库为准。

[<img src="https://play.google.com/intl/en_us/badges/static/images/badges/en_badge_web_generic.png"
alt="在 Google Play 下载：https://play.google.com/store/apps/details?id=com.craxiom.networksurvey]"
width="32%">](https://play.google.com/store/apps/details?id=com.craxiom.networksurvey&referrer=utm_source%3Dgithub-android-readme)
[<img src="https://gitlab.com/IzzyOnDroid/repo/-/raw/master/assets/IzzyOnDroid.png"
alt="在 IzzyOnDroid 获取"
width="32%">](https://apt.izzysoft.de/fdroid/index/apk/com.craxiom.networksurvey)
[<img src="https://fdroid.gitlab.io/artwork/badge/get-it-on.png"
alt="在 F-Droid 获取"
width="32%">](https://f-droid.org/packages/com.craxiom.networksurvey/)

Network Survey 把安卓手机变成无线网络探测工具，可探测蜂窝网络、Wi-Fi 网络、蓝牙设备与 GNSS 星座。
一次探测只有一次「开始」和一次「停止」。在「探测」页选择要记录的记录类型（蜂窝网络、电话状态、Wi-Fi、
蓝牙、GNSS、通话记录）以及记录的去向。多个目标端可以同时运行，各自接收它能承载的记录类型：

* **日志文件**：保存在手机上的 CSV 文件，每种记录类型一个文件。通话记录唯一支持的目标端。
* **NS Analytics**：上传到你团队的 [NS Analytics](https://www.networksurvey.app/analytics) 工作区，通过扫描二维码连接。
* **MQTT 数据流**：把 JSON 记录发送到你自己的 MQTT 代理服务器。
* **gRPC 数据流**：把 protobuf 记录发送到你自己的 gRPC 服务器。
* **社区数据库**：蜂窝网络记录上传到 [OpenCelliD](https://opencellid.org/)，蜂窝网络与 Wi-Fi 记录上传到
  [BeaconDB](https://beacondb.net/)。

Network Survey 用户手册见[此处](https://networksurvey.app/manual)。

<p align="center">
  <img src="screenshots/survey_tab.png" alt="探测页：正在记录蜂窝网络、电话状态、Wi-Fi、蓝牙与 GNSS，显示任务 ID 与各类型记录数" width="190"/>
  <img src="screenshots/signals_cellular.png" alt="信号 - 蜂窝网络：n41 频段的 5G NR 独立组网服务小区，含载波聚合与信号强度图表" width="190"/>
  <img src="screenshots/signals_wifi.png" alt="信号 - Wi-Fi：按信号强度排序的接入点，含频段、信道、安全类型与 802.11 标准" width="190"/>
  <img src="screenshots/map_survey_points.png" alt="地图页：附近基站与一次步行探测的探测点，按蜂窝信号强度着色" width="190"/>
</p>

## 功能

* **探测页**：选择探测内容与去向，查看「什么发到哪里」，一键开始或停止。每次探测都有独立的任务 ID。
* **已保存的配置**：保存「记录什么、发到哪里」，一键切换回来。
* **信号**：蜂窝网络、Wi-Fi、蓝牙与 GNSS 的实时视图。长按可复制数值。
* **地图**：来自 OpenCelliD 与 BTSearch 的基站、BeaconDB 覆盖范围，以及「我的探测点」（在发送到
  NS Analytics 或社区数据库的探测中记录）。
* **关注列表**：当发现已保存的 Wi-Fi 网络（SSID 或 BSSID）时发出通知，并保留历史记录。
* **工具**：蜂窝网络工具（LTE 与 5G NR 频段、信道转频段、小区 ID 解码器）、PLMN 查询、OUI 查询、Wi-Fi 频谱。
* **主题**：浅色、深色或跟随系统，并可选强调色。
* **托管配置（MDM）**：每种记录类型与目标端都可设为「由用户决定」「始终开启」或「关闭」，还可配置
  MQTT 代理服务器、自动开始等设置。
* **Intent API**：其它应用可以启动和停止探测（见 [Intent API](#intent-api)）。
* **NS Scout**（可选）：从「更多 > 硬件 > NS Scout」配对 [NS Scout](https://networksurvey.app/scout)——一块运行
  只接收固件的 ESP32 开发板。当它作为 Wi-Fi 来源时，会替代手机的 Wi-Fi 扫描（不受 Android 扫描限制、
  每个接入点信息更详细），并新增「Wi-Fi 客户端」与「原始帧」（pcapng，仅日志文件）记录类型。参见
  [NS Scout 手册](https://networksurvey.app/manual/ns-scout/overview/)。

## CDR 日志文件

**简介：**
Network Survey 现在支持记录通话详细记录（CDR）。该功能可让你了解手机与蜂窝网络的各类交互。

**什么是 CDR？**
通话详细记录（CDR）是记录手机与蜂窝网络之间特定交互的数据记录，包括电话、短信与彩信（SMS 和 MMS），
以及基站连接的变化。

**使用场景：**

- **监控网络活动**：记录并回看手机的蜂窝网络交互。
- **教学用途**：帮助理解蜂窝网络如何运作。
- **隐私监控**：检查手机是否连接到了意料之外的基站。

**功能限制说明：**

- 普通版本的 Network Survey 不会在 CDR 中记录 SMS 或 MMS 事件，因为 Google Play 政策不允许应用申请完整短信权限。
- 普通版本也不支持记录与通话事件关联的对方号码。
- 两个版本都不记录 RCS 聊天。Android 只允许默认短信应用读取 RCS 消息，Network Survey 无法记录。

**功能更全的替代版本：**
如果你需要包含 SMS 与 MMS 事件及通话详情的 CDR 记录：

1. **从源码安装**：使用本仓库的源码自行构建安装。
2. **下载预编译 APK**：在 [GitHub Releases 页面](https://github.com/christianrowlands/android-network-survey/releases)
   最新发行版中获取 `*-cdr-release.apk`。
3. **IzzyOnDroid**：从 [IzzyOnDroid](https://apt.izzysoft.de/fdroid/index/apk/com.craxiom.networksurvey) 安装。

## 跟踪与隐私

Google Play 商店版本集成了 Firebase Crashlytics 与 Analytics，意味着应用崩溃日志与分析数据会离开设备发送到
Firebase。如果不想参与此类跟踪，有三种选择：

1. 从 [IzzyOnDroid](https://apt.izzysoft.de/fdroid/index/apk/com.craxiom.networksurvey) 安装。
2. 用本仓库源码指定 CDR 构建变体安装。
3. 从 [Releases 页面](https://github.com/christianrowlands/android-network-survey/releases) 安装 CDR APK。

本应用的[隐私政策](privacy_policy.md)说明了 Google Play 商店版本向 Firebase 发送的数据。

## 快速开始

按以下步骤构建并安装项目：

    1) 克隆仓库。
    2) 打开 Android Studio，然后打开克隆仓库的根目录。
    3) 连接安卓手机（确保已开启调试）。
    4) 点击 Android Studio 的「运行」按钮安装并运行应用。

若要用命令行构建，可用下列命令构建并安装 APK。运行安装命令前请先连接手机。

本项目有两个构建变体。默认变体是普通版本（即「Google Play」版本）。如隐私章节所述，「Google Play」版本启用了
Firebase Crashlytics 与 Analytics。第二个变体是「CDR」版本，不含 Firebase Crashlytics 与 Analytics，
并具有上文所述的扩展 CDR 记录功能。

### 构建并安装普通版本

> 注意：在 Windows 上请把 `./gradlew` 换成 `gradlew`

```shell
./gradlew assembleRegularDebug
./gradlew installRegularDebug
```

### 构建并安装 CDR 版本

```shell
./gradlew assembleCdrDebug
./gradlew installCdrDebug
```

### 运行测试

> 注意：需要连接设备（真机或安卓模拟器）

```
./gradlew connectedAndroidTest
```

### 前置条件

开发本项目需安装 Android Studio。

## gRPC 探测记录流式传输

gRPC 数据流目标端把 GSM、CDMA、UMTS、LTE、NR、802.11、蓝牙、GNSS、电话状态与设备状态记录发送到 gRPC 服务器；
当 NS Scout 记录 Wi-Fi 客户端时，还会发送 Wi-Fi 客户端与探测请求记录。可用
[Network Survey Messaging](https://github.com/christianrowlands/network-survey-messaging) 库搭建 gRPC 服务器，
之后由具体实现处理收到的探测消息。

若想了解如何用生成的 Go 代码创建 gRPC 服务器，可查看
https://github.com/christianrowlands/ns-messaging-go-grpc-server 的代码。

该服务器本身不做任何实际用途，只是一个 Go 语言实现示例。

## MQTT 代理服务器探测记录流式传输

MQTT 数据流目标端把 GSM、CDMA、UMTS、LTE、NR、802.11、蓝牙与 GNSS 探测记录发布到已连接的 MQTT 代理服务器，
使用以下 MQTT 主题：

* gsm_message
* cdma_message
* umts_message
* lte_message
* nr_message
* 80211_beacon_message
* bluetooth_message
* gnss_message
* 80211_client_message 与 80211_probe_request_message（Wi-Fi 客户端，配合 NS Scout）

另有一个设备状态与电话状态消息发布在 `device_status_message` 主题上。

当「通过 MQTT 流式传输」开启时（在「设置 > 关注列表」中，默认开启），Wi-Fi 关注列表事件也会发布：

* watchlist_match_message（发现了被关注的网络）
* watchlist_entry_message（手机关注的网络及其标签）

MQTT 代理服务器连接支持明文与 TLS/SSL，应用可保存多个代理服务器。

探测消息为 JSON 格式，遵循
[Network Survey Messaging](https://github.com/christianrowlands/network-survey-messaging) 库的 protobuf 定义。
[API 文档发布在这里](https://messaging.networksurvey.app/)。

### MQTT 代理服务器二维码

在 MQTT 数据流页面，「扫描二维码」可从含下列字段的 JSON 字符串二维码中保存一个代理服务器。

`mqtt_client` 字段可选，默认使用应用自动生成的值。

`mqtt_topic_prefix` 字段用于给探测记录发布到的 MQTT 主题加前缀。默认主题见上文（如 `lte_message`），
若要自定义前缀，可把该字段设为 `"mqtt_topic_prefix": "my/custom/topic/"`（注意结尾斜杠），
使主题变为 `my/custom/topic/lte_message`。

```json
{
  "mqtt_host": "mqtt.example.com",
  "mqtt_port": 8883,
  "mqtt_tls": true,
  "mqtt_client": "aclient",
  "mqtt_username": "auser",
  "mqtt_password": "apassword",
  "mqtt_topic_prefix": "my/custom/topic/path/",
  "mqtt_qos": 1,
  "device_status_stream_enabled": true
}
```

流式传输的内容由本次探测选定的记录类型决定。二维码不选择记录类型，因此旧版本中的按类型开关
（`cellular_stream_enabled`、`phone_state_stream_enabled`、`wifi_stream_enabled`、
`bluetooth_stream_enabled`、`gnss_stream_enabled`）在扫码时会被忽略。

## Intent API

Network Survey 支持 Intent API，允许其它应用启动和停止探测。Intent API 文档见
[Intent API 网站](https://www.networksurvey.app/intent-api)。

## 社区数据库

**社区数据库**目标端让你自愿把采集到的**蜂窝网络与 Wi-Fi 探测数据**贡献给公开数据库。它是**可选**的，
除非你主动开启，否则默认关闭。

### 支持的数据库：

- **[OpenCelliD](https://opencellid.org/)**：全球开放基站数据库，接收蜂窝网络记录。
- **[BeaconDB](https://beacondb.net/)**：众包地理定位数据库，接收蜂窝网络与 Wi-Fi 记录。

发送到社区数据库的探测记录会保存在手机上，并按「自动上传」或你选择的「立即上传」上传。
只使用蜂窝网络与 Wi-Fi 记录，不发送任务 ID。上传内容不含电话号码或 IMEI，但 Wi-Fi 上传包含每个网络的
名称（SSID）与硬件地址（BSSID）。

更多详情见 [Network Survey 用户手册](https://networksurvey.app/manual/advanced/upload-databases/)。

## APK 校验

要验证 Network Survey 的真实性，可用 `apksigner verify --print-certs <apk-file>` 或 AppVerifier 之类的应用
比对签名证书的 SHA-256 指纹。

### Google Play 商店

包名：`com.craxiom.networksurvey`
SHA-256：
`F5:85:5E:DE:9F:51:DD:EF:D7:2B:1C:D5:A2:29:76:1A:B7:35:2B:30:AD:4F:5F:CE:6D:E2:48:73:9F:4B:4C:79`

### GitHub Releases / 直接下载的 APK

包名：`com.craxiom.networksurvey`
SHA-256：
`AB:F5:AD:29:11:FE:31:91:6F:4E:25:5C:17:33:A3:F6:BC:2C:AB:2F:28:16:C9:58:7D:E1:1A:41:58:71:AF:07`

校验信息也发布在：https://networksurvey.app/manual/permissions/apk-verification/

> 说明：以上指纹属于**上游原作者**的签名。本修改版 APK 使用本仓库维护者自己的签名，指纹不同；
> 请以本仓库 Release 页面公布的指纹为准。

## 翻译

Network Survey 借助社区的 [Weblate](https://weblate.org/)（一个自由的在线持续本地化平台）进行本地化。
如果你想帮忙把应用翻译成其它语言，可直接在浏览器中参与，无需编程：
[Weblate 上的 Network Survey 项目](https://hosted.weblate.org/engage/network-survey-android-app/)。

感谢所有帮助翻译 Network Survey 的朋友。

> 本修改版在 Weblate 现有简体中文译文基础上，补全了全部 2195 条可翻译字符串的中文翻译，并把应用默认语言
> 设为简体中文。中国通用且无汉化空间的专有名词（Wi-Fi、GNSS、SSID、BSSID、MQTT、gRPC、LTE、NR、dBm 等）
> 按惯例保留原样。

## 致谢

### 贡献说明

本项目使用了 **[GPSTest 安卓应用](https://github.com/barbeau/gpstest)** 的部分元素，具体是 GPS 用户界面组件。
感谢 GPSTest 应用把这些资源开放给开源社区。

本项目还借鉴了 **[Tower Collector](https://github.com/zamojski/TowerCollector)** 的代码，用于把探测记录上传到
OpenCellID 与 BeaconDB。感谢 Tower Collector 应用。

### 致谢

感谢所有为本项目投入时间与技能的贡献者，你们的贡献对应用的开发与成功弥足珍贵。

感谢：

<a href="https://github.com/christianrowlands/android-network-survey/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=christianrowlands/android-network-survey" />
</a>

由 [contrib.rocks](https://contrib.rocks) 生成。

诚挚感谢各位的付出。

## 更新日志

各发行版详情见更新日志：[`CHANGELOG.md`](CHANGELOG.md)

## 联系方式

* **Christian Rowlands** - [Craxiom](https://github.com/christianrowlands)（上游作者）
