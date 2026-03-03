# cprint-upload-tool

联创云打印命令行上传工具（基于会话 Cookie）。

## 快速开始

1. 在浏览器里登录联创云打印页面。
2. 从浏览器复制 Cookie（至少包含 `OSESSIONID`，建议包含 `SESSIONID`）。
3. 保存 Cookie：

```bash
cprint-upload-tool/bin/cprint-tool set-cookie --cookie 'OSESSIONID=xxx; SESSIONID=yyy'
```

4. 上传文件：

```bash
cprint-upload-tool/bin/cprint-tool upload --file /绝对路径/文件.pdf
```

## 常用参数

- `--copies 2`：两份
- `--color 2`：彩色
- `--duplex 3`：双面长边
- `--paper 9`：A4

完整参数见：

```bash
cprint-upload-tool/bin/cprint-tool --help
```

## 登录态说明

- 若返回 `code=4` 或提示“无效会话，未登录”，说明 Cookie 已过期。
- 重新在浏览器登录后，再执行 `set-cookie` 覆盖即可。
