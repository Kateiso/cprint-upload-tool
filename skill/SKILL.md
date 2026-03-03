---
name: cprint-upload
description: 上传文件到联创云打印（10.135.0.139:9130），通过手动登录后复用 Cookie 完成接口上传；当用户提到“云打印上传”“cprint 上传”“上传到联创云打印”时使用。
---

# cprint-upload skill

## 用途
把“联创云打印页面上传”封装成可复用流程：
- 先保存登录 Cookie
- 再上传指定文件

## 触发词建议
- 上传到联创云打印
- cprint 上传
- 云打印上传文件

## 执行步骤
1. 确认用户已在浏览器登录 `http://10.135.0.139:9130/client/new/cprintPc/cprint.html`。
2. 引导用户提供 Cookie（`OSESSIONID`/`SESSIONID`）。
3. 执行：
   - `cprint-upload-tool/bin/cprint-tool set-cookie --cookie '...'`
   - `cprint-upload-tool/bin/cprint-tool upload --file /path/to/file`
4. 回报上传结果（成功/失败 + code/message）。

## 获取 Session（Cookie）教程
目标：拿到 `OSESSIONID`（有 `SESSIONID` 一并带上）。

1. 打开并登录页面：`http://10.135.0.139:9130/client/new/cprintPc/cprint.html`。
2. 登录成功后按 `F12` 打开开发者工具。
3. 进入 `Application`（或“应用”）-> `Cookies` -> 选择 `http://10.135.0.139:9130`。
4. 复制 `OSESSIONID`，如果有 `SESSIONID` 也复制。
5. 按下面格式拼接并保存：
   - `cprint-upload-tool/bin/cprint-tool set-cookie --cookie 'OSESSIONID=xxx; SESSIONID=yyy'`
6. 执行上传测试：
   - `cprint-upload-tool/bin/cprint-tool upload --file /path/to/file.txt`

快速判定：
- 若返回 `code=0`：会话有效、上传成功。
- 若返回 `code=4`：会话失效，重新登录并更新 Cookie。

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
