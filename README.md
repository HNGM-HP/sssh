# sssh

基于 `dialog` 的交互式 SSH 快捷连接命令。

把脚本放进 `~/.local/bin` 后，只需要在终端执行：

```bash
sssh
```

即可打开 SSH 主机搜索列表，选择主机后自动执行 `ssh <Host别名>` 连接。

## 安装

复制脚本到 `~/.local/bin`：

```bash
mkdir -p ~/.local/bin
cp ./sssh ~/.local/bin/sssh
chmod +x ~/.local/bin/sssh
```

确保 `~/.local/bin` 已加入 `PATH`：

```bash
export PATH="$HOME/.local/bin:$PATH"
```

可以把上面这行加入 `~/.bashrc` 或 `~/.profile`，这样每次打开终端都能直接使用 `sssh`。

验证命令是否可用：

```bash
which sssh
```

正常情况下会输出类似：

```text
/home/你的用户名/.local/bin/sssh
```

之后直接运行：

```bash
sssh
```

## 快捷连接

执行：

```bash
sssh
```

会打开 SSH 主机搜索列表。输入关键词筛选，回车后连接选中的主机。

主列表操作：

- `↑↓`：移动选择
- `<连接>`：连接当前 Host
- `<新增>`：添加 SSH 配置
- `<修改>`：修改当前 Host
- `<删除>`：删除当前 Host，删除前会二次确认
- `Esc`：退出

## 管理 SSH 配置

执行：

```bash
sssh --manage
```

或：

```bash
sssh -m
```

会进入 `~/.ssh/config` 管理界面，支持：

- 添加 Host
- 修改 Host
- 删除 Host

写入前脚本会自动备份当前配置文件，备份路径类似：

```text
~/.ssh/config.bak.20260509123045
```

删除 Host 时需要输入 `yes` 二次确认。

## 依赖

运行前需要这些命令：

- `bash`
- `python3`
- `ssh`
- `dialog`
- `awk`
- `sed`

脚本启动时会检查 `dialog`，如果没有安装，会尝试通过 `apt-get` 自动安装。

Ubuntu/Debian 手动安装 `dialog`：

```bash
sudo apt install dialog
```

## 配置 SSH 主机

`sssh` 读取的是标准 SSH 配置文件：

```text
~/.ssh/config
```

基础配置示例：

```sshconfig
Host pve
  HostName 192.168.1.30
  User root
  Port 22

Host nas
  HostName 192.168.1.20
  User admin
  Port 22
```

执行 `sssh` 后会出现 `pve`、`nas` 两个可选项。选中 `pve` 后，实际执行的是：

```bash
ssh pve
```

也可以通过管理界面交互式添加这类配置：

```bash
sssh --manage
```

## 分组配置 Demo

`sssh` 支持用注释给 SSH 主机分组。例如：

```sshconfig
# 家庭网络 > 服务器
Host pve
  HostName 192.168.1.30
  User root
  Port 22

Host nas
  HostName 192.168.1.20
  User admin
  Port 22

# 云服务器 > 生产环境
Host app-prod
  HostName 203.0.113.10
  User ubuntu
  Port 22

Host db-prod
  HostName 203.0.113.11
  User ubuntu
  Port 2222
```

在 `sssh` 列表中会显示成：

```text
家庭网络 / 服务器 / pve
家庭网络 / 服务器 / nas
云服务器 / 生产环境 / app-prod
云服务器 / 生产环境 / db-prod
```

说明：

- `# 家庭网络 > 服务器` 是普通注释，不是 SSH 标准语法。
- 这是 `sssh` 自定义支持的分组写法。
- `>` 会被显示为 `/`，用于表达层级。
- 分组注释会影响它后面的主机，直到遇到新的分组注释。

## 注意事项

- 脚本只读取带 `HostName` 的 `Host` 配置。
- `Host *` 会被跳过。
- 没有配置 `Port` 时，显示为默认端口 `22`。
- 如果执行 `sssh` 提示找不到命令，优先检查 `~/.local/bin` 是否已经加入 `PATH`。
