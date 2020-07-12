+++

author = "邱圆辉"

title = "manjaro安装总结"

date = "2019-09-22"

description = "manjaro安装过程"

tags = [
    "manjaro", "linux", "tutorial"
]

images = ["img/2014/04/pic01.jpg"]

+++

鼓捣了一天 manjaro 系统，在室友的大力帮助下终于把基本的环境、包都配置好了，在此按照系统安装之后的时间顺序稍做记录，以便后续翻阅备用。

### 将 pacman 源更改为清华镜像源

pacman 为 manjaro 默认的软件包管理工具，它将一个简单的二进制包格式和易用的构建系统结合了起来。不管软件包是来自官方的 Arch 库还是用户自己创建，Pacman 都能方便的管理。

但是 pacman 默认源在国外，因此安装包时速度会很慢，所以第一步将 pacman 的源更改为清华镜像源。

在终端输入以下命令：

```bash
sudo pacman-mirrors -i -c China -m rank
```

### 更新系统（所有的包）

在终端输入以下命令：

```bash
sudo pacman -Syyu
```

### 安装 alacritty

alacritty 是 linux 上一个使用 gpu 进行渲染的终端模拟器（windows 也能安装，本人没试过也不想试，有了 linux 终端谁还记得 windows 呢）。其他简介、优点说明就省了，具体安装如下：（安装包的话找 pacman 就完事儿了）

```bash
sudo pacman -S alacritty
```

设置 i3 的默认终端为 alacritty（修改 i3 配置文件，文件路径见后文）：

```bash
bindsym $mod+Return exec alacritty
```

找到这一行并将 exec 后的终端改为 alacritty （上面已修改）。

### 安装窗口管理器 i3

i3 是 linux 系统上的一个平铺窗口管理器，简而言之在这里能够用键盘完全代替鼠标进行操作，而且还自带分屏及分屏排列等功能，完虐 windows 手动分屏功能（而且还只支持两分屏及四分屏），具体安装如下：

```bash
sudo pacman -S i3
```

安装成功后 reboot，在登录界面点击设置图标可选择要进入的桌面环境（i3 或默认安装的 gnome），选择 i3 开始翱翔。

#### i3 基础配置

重启后系统会进入 i3 的配置页面，如左图：

  <center class="half">
    <img src="1.png" height=200px>
    <img src="2.png" height=200px>
  </center>

选择 esc，即不使用默认配置。然后显示页面如右图：

这个页面配置的是 manjaro 的 mod 键（可以和很多键位组成一些快捷键），个人建议选择 alt 键，windows 键太远，而且在机械键盘上有 lalt 和 ralt 两个键，而 windows 只有一个键。

#### i3 常用快捷键及设置

在 i3 配置文件中可修改快捷键搭配方式，如：

```bash
bindsym $mod+Shift+h move left
```

即设置 mod + Shift + h 为向左移动当前窗口。

以下为我的一些个人 i3 快捷键：

  <center>

|    快捷键    |      作用      |     快捷键      |            作用            |
| :----------: | :------------: | :-------------: | :------------------------: |
| mod + return |  新建终端窗口  |     mod + f     |        全屏状态切换        |
|   mod + h    |  向左切换窗口  | mod + shift + h |        向左移动窗口        |
|   mod + j    |  向下切换窗口  | mod + shift + j |        向下移动窗口        |
|   mod + k    |  向上切换窗口  | mod + shift + k |        向上移动窗口        |
|   mod + l    |  向右切换窗口  | mod + shift + l |        向右移动窗口        |
|   mod + f    |  全屏状态切换  |     mod + d     |      进入软件打开界面      |
|   mod + b    | 打开默认浏览器 |     mod + s     | 以上下标签式（栈）排列窗口 |

  </center>
  其中部分为系统默认，部分为用户自定义，即修改i3配置文件：

```bash
nvi ~/.config/i3/config
```

其中 nvi 为 neovim 的别名，后面将介绍其相关配置。

### 修改默认 shell 环境

- 安装 zsh

  manjaro 默认的 shell 环境为 bash。而 zsh 环境比 bash 更方便，因此选择将默认 shell 改为 zsh。

  zsh 的介绍及配置见：[终极 Shell——ZSH - 池建强的文章 - 知乎](https://zhuanlan.zhihu.com/p/19556676)

  各系统 zsh 安装方式见：[Installing ZSH](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH)

  manjaro 系统安装 zsh 的命令同上：

  ```bash
  sudo pacman -S zsh
  ```

- 将 zsh 设置为默认 shell

  zsh 安装完成后需要将其设置为默认 shell，命令如下：

  ```bash
  chsh -s $(which zsh)
  ```

- oh-my-zsh 的安装

  oh-my-zsh 是一个 zsh 配置管理框架，可用于安装各种插件和主题更改等功能。

  github 主页为：[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

  安装很简单：

  ```bash
  curl -Lo install.sh https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
  ```

  若想更换 zsh 相关配置，修改其配置文件：

  ```bash
  nvi ~/.zshrc
  ```

  如更换主题（修改配置文件中的 ZSH_THEME 字段）：

  > ZSH_THEME="robbyrussell"

  给命令设置别名（alias）：

  > alias py="python"

  其中等号左边为要设置的别名，右边引号内为原始命令。

每次修改 zsh 配置后需要重启终端或输入以下命令应用修改：

```bash
source ~/.zshrc
```

### 安装 vim

用 vim 编辑文本的好处我已经不想多说了，用过的都说好啊！然而 manjaro 竟然不自带 vim，好在有 pacman 管理工具：

```bash
sudo pacman -S vim
```

### 安装 neovim

neovim 科普文章：

[科普，21 世纪的 Vim - neovim - 亦俊的文章 - 知乎](https://zhuanlan.zhihu.com/p/21364426)

安装命令：

```bash
sudo pacman -S neovim
```

由于从零开始配置 vim 过于麻烦，因此直接使用 github 上的 thinkvim 配置。具体安装及注意事项见：

[github hardcoreplayers/ThinkVim](https://github.com/hardcoreplayers/ThinkVim)

### 配置 alacritty

### 主题

alacritty 配置文件：

> ~/.config/alacritty/alacritty.yml

在其中修改 colors 字段的代码即可。具体主题可见：

[github eendroroy/alacritty-theme](https://github.com/eendroroy/alacritty-theme)

用对应主题的配置文件中的 colors 字段覆盖上述字段。

每次修改配置文件后重启终端即可应用修改。

#### 字体

修改 alacritty 配置文件中的 fonts 字段。

#### compton

compton 是一个合成管理器（我也不知道什么意思），可以给 i3 窗口添加淡入淡出、半透明、阴影等效果，具体介绍见：
[Arch Linux - Compton](https://wiki.archlinux.org/index.php/Compton)

compton 的安装及环境依赖见：
[github tryone144/compton](https://github.com/tryone144/compton)

配置文件：

> ~/.config/compton.conf

修改配置文件后需要关闭重启 compton 以应用修改：

```bash
killall compton
```

然后 _ctrl + d_ 搜索 compton 并打开。

安装并配置好后在 i3 配置文件中添加代码如下：

```bash
exec_always compton
```

即每次启动 i3 时自动启动 compton。

### 其余配置

- #### CapsLock 到 Esc 的映射

  CapsLock 占据了重要的位置发挥着最少的最用，Esc 占据了最偏远的位置发着最大的作用。而在 vim 中 Esc 键又是极其重要的，因此有必要将 CapsLock 映射成 Esc。

  具体方法网上有很多教程，试了几个不管用，我个人的最终解决方法为：

  1. 在家目录下新建.xmodmap 文件

  ```bash
  nvi ~./xmodmap
  ```

  2. 加入以下两行代码：

  ```bash
  clear lock
  keycode 0x42 = Escape
  ```

  3. 修改 i3 配置文件：

  ```bash
  nvi ~/.config/i3/config
  ```

  4. 添加如下代码：

  ```bash
  exec_always xmodmap ~/.xmodmap
  ```

  即每次启动 i3 时自动应用这个映射。

  但是正确的方法应该是从系统层面将 CapsLock 修改为 Escape，如 Windows 里面修改注册表的方法，但是监于本人还未找到合适方法，先用这个代替吧。

- #### 安装 variety

  到目前为止 i3 还没有一个合适的桌面壁纸呢，因此安装 variety 壁纸管理工具：

  ```bash
  sudo pacman -S variety
  ```

- #### 安装 arandr

  arandr 是终端的一个文件浏览器，同时支持 vim 的操作语法，十分方便：

  ```bash
  sudo pacman -S arandr
  ```

  然后在 i3 配置文件中添加：

      exec_always variety
      exec_always arandr

  即每次启动 i3 时自动启动这两个软件。

关于 manjaro 的配置暂时就这么多，以后应该会安装更多软件，到时再作记录。

总而言之这几天用着 manjaro 感觉工作效率提高了不少，一个字：爽！

如果你目前还是一个忠实的 windows 用户，同时又是一个程序猿的话，欢迎加入 linux 大家庭！
