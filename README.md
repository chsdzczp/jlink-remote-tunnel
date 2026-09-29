# J-Link Remote Tunnel Server

自建的 J-Link 远程调试隧道中继服务端（Python asyncio，单文件，无第三方依赖），用于替代 SEGGER Remote Server / `jlink.segger.com` 的中继角色：硬件端（Remote）与调试端（Client）均连接到本服务端，由它完成配对与数据透传。

## 功能

- 双模式接入：
  - **SN 模式**：仅凭序列号配对；
  - **TLV 模式**：支持名称 / 序列号标识，可选密码。
- 密码模式下，鉴权（8 字节 challenge / 32 字节 SHA-256 应答）由客户端与硬件端点到端完成，服务端只透传，不经手明文密码。
- 配对前硬件端的数据先缓存、配对后回放，不丢 announce。
- 状态码与官方语义一致：重复注册 `-3`、无对应硬件 `-2`、硬件忙 `-4`。
- TLV 模式带 keepalive 序列（5×`0xAA` + `0xB6`，各带时间戳）。

## 用法

```bash
python tunnel_server.py [port]   # 默认端口 19020
```

在 J-Link Remote Server 中将 Host 指向部署本服务端的主机地址与端口即可。需 Python 3.10+。

## 说明

- 兼容 SEGGER J-Link Remote Server 的隧道协议。
- 仅用于自有设备的远程调试与互操作测试，请遵守相关软件许可条款。

---

## English

A self-hosted relay server for J-Link remote debugging tunnels (Python asyncio, single file, no third-party dependencies). It takes over the relay role of SEGGER Remote Server / `jlink.segger.com`: both the hardware side (Remote) and the debugging side (Client) connect to this server, which handles pairing and relays the traffic.

### Features

- Two connection modes:
  - **SN mode**: pairing by serial number only;
  - **TLV mode**: name / serial-number identity, optional password.
- In password mode, authentication (8-byte challenge / 32-byte SHA-256 response) happens end-to-end between client and remote hardware — the server only relays it and never sees the plaintext password.
- Traffic from the remote received before pairing is buffered and replayed afterwards, so no announce is lost.
- Status codes match the official semantics: duplicate registration `-3`, unknown remote `-2`, busy remote `-4`.
- TLV mode includes a keepalive sequence (5×`0xAA` + `0xB6`, each with a timestamp).

### Usage

```bash
python tunnel_server.py [port]   # default port: 19020
```

Point the J-Link Remote Server host at the machine running this server. Requires Python 3.10+.

### Notes

- Compatible with the SEGGER J-Link Remote Server tunnel protocol.
- Intended for remote debugging and interoperability testing of your own devices; please comply with the relevant software license terms.
