# Tmux

一般在连接上服务器之后，会话开始，关闭窗口之后会话就结束了，如果中途突然断电，就找不回上次的指令了。

tmux可以在一个窗口中访问多个会话，断了不会丢失

## 安装

```bash
# Ubuntu 或 Debian
$ sudo apt-get install tmux

# CentOS 或 Fedora
$ sudo yum install tmux

# Mac
$ brew install tmux
```

## 常用指令

```bash
# 启动一个新的
tmux 
# 改名
crtl+b ,   --- 只是改变进入tmux之后显示的名字，外界ls的名字不改变
crtl+b $   --- 改变了外面的ls现实的名字，实际上改变了session的名字

# tmux 查看log
crtl+b [
# tmux中搜索
crtl+s

# 退出
crtl+b d --- 后台还在运行
crtl+b x --- 永久杀死

# 重新连接
tmux a -t tag_name

# 查看
tmux ls

# tmux 分屏
crtl+b %

# tmux快速切换会话
crtl+b s --- 之后可以选择对应的会话

```

## 指令

启动

```bash
tmux
```

退出：按下`Ctrl+d`或者显式输入`exit`命令，就可以退出 Tmux 窗口

命令需要用`crtl+b`作为前缀键，帮助命令的快捷键是`Ctrl+b ?`。

## 会话管理

### 新建

为会话起名字，默认是按照数字进行排序

```bash
$ tmux new -s <session-name>
```

### 分离

按下`Ctrl+b d`或者输入`tmux detach`命令，就会将当前会话与窗口分离。

查看所有的会话：`tmux ls`

### 接入会话

```bash
# 使用会话编号
$ tmux attach -t 0

# 使用会话名称
$ tmux attach -t <session-name>
```

### 杀死会话

```bash
# 使用会话编号
$ tmux kill-session -t 0

# 使用会话名称
$ tmux kill-session -t <session-name>
```

### 切换会话

```bash
# 使用会话编号
$ tmux switch -t 0

# 使用会话名称
$ tmux switch -t <session-name>
```

### 重命名会话

```bash
$ tmux rename-session -t 0 <new-name>
```

### 快捷键

- `Ctrl+b d`：分离当前会话。
- `Ctrl+b s`：列出所有会话。
- `Ctrl+b $`：重命名当前会话。

## 划分窗口

```bash
# 划分上下两个窗格
$ tmux split-window

# 划分左右两个窗格
$ tmux split-window -h
```

### 快捷键

* **`Ctrl+b s`: 多个会话切换**

- **`Ctrl+b %`：划分左右两个窗格。**
- `Ctrl+b "`：划分上下两个窗格。
- `Ctrl+b <arrow key>`：光标切换到其他窗格。`<arrow key>`是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键`↓`。
- `Ctrl+b {`：当前窗格与上一个窗格交换位置。
- `Ctrl+b }`：当前窗格与下一个窗格交换位置。
- `Ctrl+b Ctrl+o`：所有窗格向前移动一个位置，第一个窗格变成最后一个窗格。
- `Ctrl+b Alt+o`：所有窗格向后移动一个位置，最后一个窗格变成第一个窗格。
- `Ctrl+b x`：关闭当前窗格。
- `Ctrl+b !`：将当前窗格拆分为一个独立窗口。
- `Ctrl+b z`：当前窗格全屏显示，再使用一次会变回原来大小。
- `Ctrl+b Ctrl+<arrow key>`：按箭头方向调整窗格大小。
- `Ctrl+b q`：显示窗格编号。

