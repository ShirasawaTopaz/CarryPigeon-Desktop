# UI 点击流 × 服务端联合调试证据（2026-08-25）

环境：Vite `http://127.0.0.1:1420`，真服 HTTP `:8080` / WS `:18080`，账号 `carry-owner` / `carrypigeon123`。

服务端 `GET /api/channels/1/messages?around_mid=5001&before=5&after=5` → **200**（修前为 500，SQL 字面量 CDATA）。

| 段 | 操作 | 结果 |
| --- | --- | --- |
| A | 地址 `http://127.0.0.1:8080` → 连接 | pass。确认页 CarryPigeonBackend / 1.0 |
| B | 用户名密码登录 | pass。进入 `/chat`，绿点 SERVER |
| C | `+` 创建频道 `联调点击流频道` | pass |
| D | 发送 `联调点击流：文本消息` | pass。气泡 CARRY OWNER / Core:Text |
| E | 频道信息 → 成员身份旁「编辑」→ 改名为 `联调点击流频道-已改名` → 保存 | pass（第一次误点公告编辑；补文案后成功） |
| F | 「未加入频道」+ 类型下拉「公开」 | pass。空态「没有可加入的公开频道」 |
| G | 顶栏「提及」 | pass。`暂无提及`（carry-owner 无收件箱数据） |
| H | 频道设置 → 审计日志 | pass。空列表可打开，筛选控件可见 |
| I | 「联系人」搜 UID `1001` | pass。命中 Carry Owner |

未覆盖：File/Voice（storage 关闭）、邮件成功路径、提及跳转 `around_mid`（mention-inbox 按设计不请求 around_mid；around_mid 用 HTTP 验过）。
