
### 目标
homebrew 类似 Linux 下的 yum , 要下载安装的软件依托在 github 上，默认指向的地址下载较慢，而且可能被墙，可以通过换镜像库的方式解决（一般换清华镜像源or中科大镜像源）

### 方法
使用 `brew --repo` 可以看brew的本地库地址，切换到路径上
```shell
# 切换路径，替换homebrew对应的源
cd "$(brew --repo)"

# 查看默认源
➜  Homebrew git:(stable) git remote -v
origin	https://github.com/Homebrew/brew (fetch)
origin	https://github.com/Homebrew/brew (push)

# 设置为中科大源
➜  Homebrew git:(stable) git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

# check设置成功
➜  Homebrew git:(stable) git remote -v
origin	https://mirrors.ustc.edu.cn/brew.git (fetch)
origin	https://mirrors.ustc.edu.cn/brew.git (push)

# 切换路径，替换homebrew-code对应的源
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

# 再次执行安装命令即可
```