---
layout: article
title: macOS 配置 OhMyZsh
tags: macOS 运维 OhMyZsh
---

### 安装 oh my zsh

- [oh my zsh 官网](https://ohmyz.sh/) 在终端运行一下命令：
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 安装插件 

#### zsh-syntax-highlighting 语法高亮

- 下载安装

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

- 在  `~/.zshrc`  中配置

```
plugins=(其他的插件 zsh-syntax-highlighting)
```
- 使配置生效

```bash
source ~/.zshrc
```

#### zsh-autosuggestions 自动补全

- 下载安装

```bash
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

- 在  `~/.zshrc`  中配置

```
plugins=(其他的插件 zsh-autosuggestions)
```

- 使配置生效

```bash
source ~/.zshrc
```

### 主题

- 修改  `~/.zshrc`  配置

```
ZSH_THEME="ys"
```

- 使配置生效

```bash
source ~/.zshrc
```

### QA

#### 1.使用 OhMyZsh 配置文件为 .zshrc 会导致 bash 的配置文件 .bash_profile 失效;

- 在 .zshrc 中添加

```
source ~/.bash_profile
```

- 使配置生效

```bash
source ~/.zshrc
```
