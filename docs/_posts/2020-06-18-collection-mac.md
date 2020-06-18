---
layout: post
title:  "mac工具收集"
date:   2020-06-18 21:50
categories: collection
permalink: /collection/20200618/mac
---

# mac 工具收集

### 免代理安装Homebrew

运行下面自动脚本（已经全部替换为国内地址）：

`/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"`

### zsh 配置使用代理

`vim ~/.zshrc`，然后增加如下配置

```
# 开启控制台代理
alias proxy='export all_proxy=socks5://127.0.0.1:1086'
alias unproxy='unset all_proxy'
```
