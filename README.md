# JSiTV

江苏 iTV 的原生 Apple 平台客户端发行仓库。

JSiTV 使用 SwiftUI 构建，面向 iPhone、iPad、Mac、Apple TV 和 Apple Vision Pro，通过 CTJSIPTV 后端访问直播、节目单、回看和点播内容。源码仓库保持私有，本仓库用于公开发布可下载的构建产物以及面向用户的使用说明。

## 功能

- 直播频道分组、搜索和线路选择
- 使用 Apple 系统 AVKit 播放
- XMLTV 节目单与正在播出状态
- 后端支持时提供电视回看
- 电影、剧集、短剧、动漫、少儿、综艺和电竞分类
- VOD 详情、搜索与选集
- CTJSIPTV 后端连接配置和状态检查
- 支持 Dynamic Type、VoiceOver、系统语义化配色、SF Symbols 与原生 Apple 控件

## 支持平台

当前 GitHub Actions 自动生成以下构建产物：

| 文件 | 平台 | 用途 |
| --- | --- | --- |
| `JSiTV-macOS-arm64.zip` | macOS 27 / Apple Silicon | Mac 运行版本 |
| `JSiTV-iOS-iPadOS-Simulator.zip` | iOS / iPadOS 27 Simulator | iPhone、iPad 模拟器测试 |
| `JSiTV-tvOS-Simulator.zip` | tvOS 27 Simulator | Apple TV 模拟器测试 |
| `JSiTV-visionOS-Simulator.zip` | visionOS 27 Simulator | Apple Vision Pro 模拟器测试 |

> 当前 iPhone、iPad、Apple TV 和 Apple Vision Pro 发布包为 Simulator 构建，不能直接安装到对应真机。真机应用需要使用有效的 Apple Developer 证书和 provisioning profile 签名。

## 下载

请从本仓库的 **Releases** 页面下载最新版本。

当前版本：`v0.1.4`。

版本号采用 `v版本号` 的形式。构建由私有 JSiTV 源码仓库的 GitHub Actions 完成，成功后自动跨仓库发布到这里。

## 后端要求

JSiTV 是 CTJSIPTV 的客户端，本身不直接执行江苏电信 IPTV 登录，也不保存 IPTV 用户名、密码、Cookie 或临时播放鉴权参数。

使用前需要先部署 CTJSIPTV 后端，并确保运行 JSiTV 的设备能够访问该后端。

CTJSIPTV 的公开发行版及部署说明（服务端已内置网页、API 和图片代理）：

https://github.com/Primovist/CTJSIPTV-Release

对于家庭局域网部署，可以直接使用后端的局域网地址，例如：

```text
http://192.168.1.100:8765
```

公网访问建议为 CTJSIPTV 配置 HTTPS 反向代理，不建议直接将后端端口暴露到互联网。

## 首次配置

启动 JSiTV 后，在后端设置中填写 CTJSIPTV 服务地址，然后执行连接测试。

例如局域网环境：

```text
http://192.168.1.100:8765
```

使用域名和 HTTPS 反向代理时，例如：

```text
https://iptv.example.com
```

客户端会通过该地址访问 CTJSIPTV 的 `/api` 接口。

## 直播线路

默认直播线路为 **自动**。

- CTJSIPTV 已配置 RTP2HTTPD 时，客户端优先使用后端提供的 RTP2HTTPD HTTP 播放线路。
- 未配置 RTP2HTTPD 时，客户端使用 CTJSIPTV 提供的普通 HTTP/HLS 线路。
- Apple AVKit 不直接用于播放 IPTV RTP/UDP 组播，因此需要播放组播源时应由 CTJSIPTV/RTP2HTTPD 转换为 Apple 平台可播放的 HTTP 流。

## macOS 安装

下载 `JSiTV-macOS-arm64.zip` 并解压得到 `JSiTV.app`。

当前 GitHub Actions 生成的 macOS 应用为未签名构建。macOS 可能阻止首次启动，应根据本机的系统安全设置允许该应用运行。

本项目当前主要面向 Apple Silicon Mac。

## iPhone / iPad

Release 中的 `JSiTV-iOS-iPadOS-Simulator.zip` 是 Simulator 构建，主要用于开发和测试，不能作为普通 IPA 直接安装到 iPhone 或 iPad。

后续配置 Apple Developer 签名后，可以增加真机 IPA/归档发布流程。

## Apple TV

`JSiTV-tvOS-Simulator.zip` 当前用于 tvOS Simulator。Apple TV 真机版本同样需要有效签名和 provisioning profile。

## Apple Vision Pro

`JSiTV-visionOS-Simulator.zip` 当前用于 visionOS Simulator。真机安装需要对应的 Apple Developer 签名配置。

## 播放兼容性

JSiTV 使用 Apple 系统媒体框架播放视频，不负责实时转码。

因此播放兼容性同时取决于 CTJSIPTV 返回的实际播放地址、IPTV 源的视频和音频编码、RTP2HTTPD/HTTP 中转方式以及当前 Apple 平台对编码和封装格式的支持。

如果同一地址在 VLC、IINA 等播放器中可以播放，但在 JSiTV 中无法播放，应优先检查流媒体编码、HLS 封装以及 Apple AVKit 的兼容性。

## 隐私与安全

JSiTV 只作为 CTJSIPTV 客户端使用。IPTV 登录凭据应保存在 CTJSIPTV 后端，不应写入客户端或公开仓库。

播放地址可能包含临时鉴权信息，不建议长期保存、记录或公开分享。远程使用时建议使用 HTTPS，不直接暴露 CTJSIPTV 原始服务端口，并将服务限制在可信局域网、VPN 或受控反向代理环境中。

## 更新

新版本由私有 JSiTV 源码仓库完成测试和构建后自动发布到本仓库 Releases。正常情况下只需要关注本仓库即可获取新的公开发行版，不需要访问源码仓库。

## 说明

本项目用于个人学习、技术研究及自有 IPTV 环境中的客户端访问。使用者应确保其使用方式符合当地法律法规、网络运营商服务协议以及内容授权要求。
