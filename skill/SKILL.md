---
name: cprint-upload
description: 上传文件到联创云打印（10.135.0.139:9130），通过手动登录后复用 Cookie 完成接口上传；当用户说“打印一下这个文件...”时优先触发。
---

# cprint-upload skill

## 用途
把“联创云打印页面上传”封装成可复用流程：
- 先保存登录 Cookie
- 再上传指定文件

## 触发词建议
- 打印一下这个文件...
- 打印一下这个文件：/path/to/file.pdf
- 打印一下这个文件并设为 A4 黑白 单面 1 份

## 执行步骤
1. 确认用户已在浏览器登录 `http://10.135.0.139:9130/client/new/cprintPc/cprint.html`。
2. 引导用户提供 Cookie（`OSESSIONID`/`SESSIONID`）。
3. 执行：
   - `cprint-upload-tool/bin/cprint-tool set-cookie --cookie '...'`
   - `cprint-upload-tool/bin/cprint-tool upload --file /path/to/file`
4. 回报上传结果（成功/失败 + code/message）。

## 获取 Session（Cookie）教程
目标：拿到 `OSESSIONID`（必需），有 `SESSIONID` 一并带上。

1. 打开并登录页面：`http://10.135.0.139:9130/client/new/cprintPc/cprint.html`。
2. 确认已登录成功（页面不是登录提示状态）。
3. 按 `F12`（或 `Option + Command + I`）打开开发者工具。
4. 进入 `Application`（应用）面板。
5. 左侧进入 `Storage` -> `Cookies` -> 选择 `http://10.135.0.139:9130`。
6. 在表格中找到 `OSESSIONID`，复制其 `Value`。
7. 若存在 `SESSIONID`，也复制其 `Value`。
8. 按以下格式拼接：
   - 有两个值：`OSESSIONID=xxx; SESSIONID=yyy`
   - 只有一个值：`OSESSIONID=xxx`
9. 保存到工具：
   - `cprint-upload-tool/bin/cprint-tool set-cookie --cookie 'OSESSIONID=xxx; SESSIONID=yyy'`
10. 立刻做一次上传验证：
   - `cprint-upload-tool/bin/cprint-tool upload --file /path/to/file.txt`

快速判定：
- 若返回 `code=0`：会话有效、上传成功。
- 若返回 `code=4`：会话失效，重新登录并更新 Cookie。
- 若连接超时：检查是否在校园网络可访问 `10.135.0.139:9130`。

## 参数映射
- 黑白/彩色 -> `--color 1|2`
- 纸型 -> `--paper -1|9|8`
- 单双面 -> `--duplex 1|2|3`
- 页码范围 -> `--from N --to N`
- 份数 -> `--copies N`

## 失败处理
- `code=4`：会话失效，要求重新登录并更新 Cookie。
- HTTP 非 200：网络或服务异常，重试并检查地址可达性。
- `413`：文件超限，压缩或拆分文档后重试。
