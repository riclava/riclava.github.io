# windows 多个 RDP 会话配置

## 前提

+ OS 版本: Win10 教育版，1909，使用 `winver` 命令可以查看

## 步骤

+ 修改 termsrv.dll

  + 执行 `net stop termservice` 停止远程桌面服务
  + 管理员身份打开CMD，执行 `takeown /F C:\Windows\System32\termsrv.dll /A` 命令，修改 termsrv.dll 所属组为本地管理员
  + 执行 `icacls C:\Windows\System32\termsrv.dll /grant Administrators:F` 对本地管理员授予 “完全控制” 的权限
  + 使用16进制编辑器，修改 termsrv.dll
    + 下载 Tiny Hexer
    + 打开 C:\Windows\System32\termsrv.dll
    + 搜索 1909 版本对应的码 `39 81 3C 06 00 00 0F 84 5D 61 01 00`
    + 替换为 `B8 00 01 00 00 89 81 38 06 00 00 90`
    + 保存 (hexer 会自动生成一个备份文件)
  + 执行 `net start termservice` 启动远程桌面服务
  
+ 配置参数
  
  + Win + R，输入 gpedit.msc
  + 切换到 计算机配置 -> 管理模板 -> Windows组件 -> 远程桌面服务 -> 远程桌面会话主机 -> 连接
  + 修改如下三个参数
    + 允许用户通过使用远程桌面服务进行远程连接 -> 已启用
    + 限制连接的数量 -> 已启用，同时配置 “允许的 RD 最大连接数”，例如 5
    + 将远程桌面服务用户限制到单独的远程桌面服务会话 -> 已启用

+ 重启远程桌面服务

  执行 `net stop termservice` 然后执行 `net start termservice`

## 参考文档

+ [win10多用户同时远程桌面登陆(允许多个RDP会话)-支持win10最新1909版2004版](https://blog.csdn.net/super319/article/details/106202774)
