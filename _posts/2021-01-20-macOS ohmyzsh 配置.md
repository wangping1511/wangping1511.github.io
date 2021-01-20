---
layout: article
aside:
  toc: true
title: macOS 配置 OhMyZsh
tags: macOS 运维
---

### 1.  oh my zsh
- [oh my zsh 官网](https://ohmyz.sh/) 在终端运行一下命令：
```
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

####  incr.zsh 补全插件

[下载插件](https://mimosa-pudica.net/src/incr-0.2.zsh) 将插件移动到 `~/.oh-my-zsh/plugins/incr/`

编辑 oh my zsh 配置
```
$ vi ~/.zshrc
```

在最下面加入

```
$ source ~/.oh-my-zsh/plugins/incr/incr*.zsh
```

![123](https://mimosa-pudica.net/img/zsh.gif)

#### zsh-syntax-highlighting 语法高亮

下载
```
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

在 `~/.zshrc` 中配置
```
plugins=(其他的插件 zsh-syntax-highlighting)
```
重启终端
