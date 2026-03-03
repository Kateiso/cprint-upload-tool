# cprint-upload-tool

联创云打印 Agent/CLI 上传工具（基于会话 Cookie）。

目标：安装后由 Agent 执行上传，不再手动点网页。

## 适用前提

1. 当前网络可访问 `http://10.135.0.139:9130`
2. 已在浏览器登录联创云打印
3. Agent 能执行本机 shell 命令

## 安装与执行

1. 克隆仓库

```bash
git clone https://github.com/Kateiso/cprint-upload-tool.git
cd cprint-upload-tool
```

2. 保存登录 Cookie

```bash
./bin/cprint-tool set-cookie --cookie 'OSESSIONID=xxx; SESSIONID=yyy'
```

3. 上传文件

```bash
./bin/cprint-tool upload --file /绝对路径/文件.pdf
```

4. 让 Agent 直接执行（示例）
- `打印一下这个文件：/path/to/file.pdf`
- `用 cprint-tool 上传 /path/to/file.pdf，A4 黑白 单面 1份`

## 如何获取 Cookie（详细）

1. 打开并登录：`http://10.135.0.139:9130/client/new/cprintPc/cprint.html`
2. 按 `F12`（或 `Option + Command + I`）打开开发者工具
3. 打开 `Application`（应用）面板
4. 左侧进入 `Storage` -> `Cookies` -> 选择 `http://10.135.0.139:9130`
5. 复制 `OSESSIONID` 的 `Value`
6. 若有 `SESSIONID`，也复制其 `Value`
7. 拼接格式：
- `OSESSIONID=xxx; SESSIONID=yyy`
- 如果只有一个值：`OSESSIONID=xxx`
8. 执行 `set-cookie` 保存

## 常用参数

- `--paper 9`：A4（`8`=A3，`-1`=不指定）
- `--color 1`：黑白（`2`=彩色）
- `--duplex 1`：单面（`2`=双面短边，`3`=双面长边）
- `--copies 1`：份数
- `--from 0 --to 0`：全部页（或自定义页码范围）

完整参数：

```bash
./bin/cprint-tool --help
```

## 常见问题

1. `code=4` / “无效会话，未登录”
- Cookie 失效，重新登录并执行 `set-cookie`

2. HTTP 连接超时
- 当前网络无法访问校内地址 `10.135.0.139:9130`

3. `413` 或文件上传失败
- 文件过大，先压缩或拆分

## 安全说明

- 仓库不包含任何个人 Cookie
- Cookie 仅保存在本机：`~/.config/cprint-tool/cookie.txt`
- 不要将 Cookie 提交到 Git 或发到群聊
