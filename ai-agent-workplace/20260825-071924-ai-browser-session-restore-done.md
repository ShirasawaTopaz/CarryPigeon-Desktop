# 任务单：浏览器联调启动走会话恢复

任务名称：browser-joint-debug-session

状态：done

任务目标：
浏览器 `pnpm run dev` 联调时，首屏与 Tauri 一样走会话恢复：未配置服务器则进入 `/login`，而不是空的 `/chat`。

任务背景：
`src/main.ts` 在无 Tauri 时只 `ensureInitialServerSelection()` 后标记就绪，跳过 `restoreStartupSession`。
点击流必须手动打开 `/login`。联合调试应让默认入口可点。

影响模块：
- `src/main.ts`
- 频道信息弹窗 i18n（联调中发现 `channel_announcement` 缺失，两个「编辑」按钮撞车）

允许修改范围：
- 浏览器主窗口启动路径：调用已有 `restoreStartupSession`
- 不引入新的 Tauri 依赖调用（避免 `listen` 非 safe 路径把启动打成 failed）
- 频道信息弹窗文案，避免改名点击流点到公告编辑

禁止修改范围：
- 服务端仓库
- 路由表重构
- Mock 语义

验收标准：
- 无持久化 server socket 时，浏览器打开根路径最终停在 `/login`
- 有 socket 时仍尝试连接/恢复会话

实际结果：
- `http://127.0.0.1:1420/` 进入 `/login`，「连接到服务器」可见。
- 对真服 `http://127.0.0.1:8080` 用 `carry-owner` 完成连接、登录、建频道、发文本、改名、发现页、提及、审计日志、联系人 UID 搜索。
- 补 `channel_announcement` / `edit_announcement`，改名走成员身份旁的「编辑」。
