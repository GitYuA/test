# 在 Alpine Linux 上安装 OpenList

本指南介绍了如何在 Alpine Linux 系统中通过手动方式安装 **OpenList**，并使用 **OpenRC** 管理其后台服务。

## 1. 下载并解压程序文件

首先，创建安装目录并下载适用于 musl 架构（Alpine 默认）的二进制文件。

```bash
# 创建并进入安装目录
mkdir -p /opt/openlist && cd /opt/openlist

# 下载最新版本 (musl-amd64)
wget https://github.com/OpenListTeam/OpenList/releases/latest/download/openlist-linux-musl-amd64.tar.gz

# 解压文件
tar -zxvf openlist-linux-musl-amd64.tar.gz

# 赋予主程序执行权限
chmod +x openlist

```

## 2. 配置 OpenRC 服务管理

为了让 OpenList 能够在后台运行并实现开机自启，我们需要创建一个 OpenRC 脚本。

### 创建服务文件

使用编辑器（如 `vi` 或 `nano`）创建文件 `/etc/init.d/openlist`：

```bash
vi /etc/init.d/openlist

```

### 写入以下内容

```bash
#!/sbin/openrc-run

name="OpenList"
directory="/opt/openlist/"
command="/opt/openlist/openlist"
command_args="server"
command_background="yes"
pidfile="/run/${RC_SVCNAME}.pid"

depend() {
    need net
    after net
}

```

### 设置权限

```bash
chmod +x /etc/init.d/openlist

```

## 3. 服务管理命令

现在你可以使用标准的服务命令来管理 OpenList：

| 操作 | 命令 |
| --- | --- |
| **启动服务** | `service openlist start` |
| **停止服务** | `service openlist stop` |
| **重启服务** | `service openlist restart` |
| **查看状态** | `service openlist status` |
| **设置开机自启** | `rc-update add openlist default` |
| **取消开机自启** | `rc-update del openlist default` |

---

## 4. 管理员密码设置

在启动服务后，您需要配置管理员密码以登录后台。

> **注意**：请确保在 `/opt/openlist` 目录下执行以下命令。

* **随机生成密码**：
```bash
./openlist admin random

```


* **手动指定密码**（将 `NEW_PASSWORD` 替换为您想设置的密码）：
```bash
./openlist admin set NEW_PASSWORD

```



---

## 常见问题排查

* 如果无法启动，请检查 `/opt/openlist/openlist` 是否具有执行权限。
* 确认 Alpine 是否已安装 `ca-certificates` 以支持 HTTPS 连接：`apk add ca-certificates`。

---

**需要我帮您补充如何配置 Nginx 反向代理以实现域名访问吗？**
