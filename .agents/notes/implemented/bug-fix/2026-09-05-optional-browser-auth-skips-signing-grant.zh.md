# Agent Note: 浏览器认证禁用时不创建签名凭据

Status: implemented

[English](2026-09-05-optional-browser-auth-skips-signing-grant.md) | 中文

## 问题

私有 LAN Web 组合禁用了 `requireBrowserAuth`，但 Connection 仍在插件激活时创建 BrowserAuth。进程 token 和签名 cookie 路径不可达时，这仍会写入持久化的浏览器会话签名凭据。

## 决策

Connection 只在 `requireBrowserAuth` 解析为 `true` 时创建 BrowserAuth。默认值仍为启用。已授权的 LAN 组合将其设为 `false` 时，HostConnectionService 不生成带 launch token 的 URL，并允许受信任请求不带浏览器会话 cookie；既有的 Host/Origin 信任栅栏仍拒绝不受信任请求。

## 考虑过的替代方案

**保留未使用的签名凭据。** 保留任何请求都不能使用的凭据会扩大凭据范围，并让后续操作员必须轮换无关状态。

**在禁用浏览器认证时也禁用 Host/Origin 信任栅栏。** 浏览器会话认证与 authority 校验保护不同的属性。LAN 组合只省略浏览器交换。

**将默认值改为禁用。** 其他 profile 需要既有的 token 和 cookie 流程，因此显式部署设置仍是唯一的 opt-out。

## 后果

禁用的配置不再持久化签名凭据。聚焦的 node-half 测试先观察到一次凭据写入，随后证明禁用认证时写入次数为零、受信任请求可通过、不受信任请求被拒绝。现有的启用认证测试仍在同一套件中。
