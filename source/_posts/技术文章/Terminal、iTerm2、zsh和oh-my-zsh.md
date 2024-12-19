---
title: Terminal、iTerm2、zsh与oh-my-zsh
tags:
  - Shell
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
date: 2022-08-03 00:00:00
sticky:
---

作为一名程序员，我们跟终端打交道的频率非常地高，终端的是否好用，往往跟我们的效率关系很大。一开始用的是 MacOS 自带的 zsh，有时候在敲很长的 shell 命令时，发现打错了某个字符，想要修改，很是麻烦。这让我不禁想，要是终端命令也能用 vi 来编辑该多好！！！于是开始捣鼓，我发现了 oh-my-zsh

<!-- more -->

## 1.1 Terminal、iTerm2、zsh、bash之间的关系

在使用 oh-my-zsh 之前，先了解一下 Terminal、iTerm2、zsh、bash 之间的关系。

Terminal、 iTerm2 跟 zsh、bash 并不是一类东西。

zsh 和 bash 是两种不同的 shell，是区别于『核』的『壳』，借助 shell，我们可以通过命令对系统进行操作。zsh 和 bash 之间有一定的兼容性。

而 Terminal 和 iTerm2 是相同种类的 APP，是各种 shell 的 GUI，可以通过 Terminal.app 来跑 zsh 和 bash，也可以通过 iTerm2 来运行。只是相比之下，iTerm2 比 MacOS 自带的 Terminal 要好用得多。

此外，像 Jetbrains IDE 和 Visual Studio Code 等内置的终端，跟 Terminal 和 iTerm2 也是同一类应用程序。

## 1.2 [iTerm2](https://iterm2.com/index.html)

iTerm2 的配色主题方案：<https://iterm2colorschemes.com/>

## 1.3 oh-my-zsh简介与官网

官网与项目地址：

* <https://ohmyz.sh/>
* <https://github.com/ohmyzsh/ohmyzsh>

oh-my-zsh 可以理解成 zsh 的超级增强版，如果是 MacOS，且默认的 shell 是 zsh，那安装是相当容易的，只需要根据 GitHub 上的教程，执行以下命令，就可以安装好了

````shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
````

安装后，oh-my-zsh 会将自己的配置 `.zshrc` 去覆盖旧的，而旧的 `.zshrc` 会被备份为 `.zshrc.pre-oh-my-zsh`，该备份有两个用处：

1. 可以将自己原先的配置，例如 Java 相关环境变量，移动到新的 `.zshrc` 配置中
2. 当以后要写在 oh-my-zsh 时，直接在终端执行 uninstall_oh_my_zsh 命令，程序会将所有与 oh-my-zsh 有关东西都删除掉，然后还原该备份文件为 `.zshrc`

当然，我相信你用了它之后，不会想卸载的，作为 zsh 的超级增强版，oh-my-zsh 真的很强大，很易用

oh-my-zsh 本质跟 zsh 没有什么区别，该怎么配置，还是怎么配置，但 oh-my-zsh 在原基础上新增了插件和主题机制，同时提供了很多实用的别名（alias）

oh-my-zsh 在安装后，便自带了很多插件和主题，我们只需要在 `.zshrc` 中根据注释，在 plugins 中加入想要的启动的插件即可，主题也同理

> 但是要注意，许多主题如果想要正常地显示，可能需要特定的字体补丁。即需要安装 [Powerline Font](https://github.com/powerline/fonts) 或者 [Nerd Font](https://github.com/ryanoasis/nerd-fonts)，对于这两种的字体补丁的介绍见：[字体](/pages/c48a5b/)
>
> 例如 agnoster 主题，如果没有合适的字体补丁，就会乱码
>
> <img src="https://oss.puppetdevz.top/image/note/9d5bbe83622b201dec9fdabadea6c6f4.png" alt="image-20221109130141997" style="zoom:50%;" />
>
> 用了 Powerline Font 或 Nerd Front 后，就正常了

此外，oh-my-zsh 还支持第三方的插件和主题，只需要下载后，放置到 `~/.oh-my-zsh/custom` 目录下即可启动

## 1.4 推荐的插件

插件也不宜启动太多，oh-my-zsh 的作者也提到，如果插件太多，会影响到 shell 的启动速度

* vi-mode：首推，也是我想要用 oh-my-zsh 的原因（虽然其他的很多也可以，但挡不住它简单）

* git：提供很多 git 命令的缩写（别名）

* autojump：快速跳转到最近的文件夹

* zsh-syntax-highlighting：命令高亮，且如果路径正确，会带下划线

  ```shell
  # 切换到 ~/.oh-my-zsh/custom/plugins 目录下后执行下面命令
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git 
  ```

* zsh-autosuggestions：自动补全可能的路径

  ```shell
  # 切换到 ~/.oh-my-zsh/custom/plugins 目录下后执行下面命令
  git clone https://github.com/zsh-users/zsh-autosuggestions 
  ```

## 1.5 推荐的主题

每个人的 the right-one 都不同，可以到[这里](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)去找

其他三方主题推荐：

* <https://github.com/romkatv/powerlevel10k>
