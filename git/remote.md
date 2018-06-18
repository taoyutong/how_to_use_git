### 目标

了解 远程 相关使用


### 内容

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)中的基础操作很详细，无需赘述，以下仅整理常用操作，并补充理解思路


```powershell

# 本地创建的仓库，添加到远程上
# 先在远程创建好对应profile
# git remote add {remote_name} {git url}
# git push {remote_name} {branch_name}
➜  git_test_3 git:(dev) ✗ git remote add origin https://github.com/taoyutong/fortest.git
➜  git_test_3 git:(dev) ✗ git remote
origin

➜  git_test_3 git:(dev) ✗ git push origin dev
Counting objects: 15, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (15/15), 1.16 KiB | 0 bytes/s, done.
Total 15 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), done.
To https://github.com/taoyutong/fortest.git
 * [new branch]      dev -> dev

# 当 remote repo 更新，要同步到本地
# 使用 git fetch 从 remote 拿到数据, 但不会修改工作目录，需要手动合并，那数据在哪里？？
# 本地同步创建一个对应远程分支的remote-tracking分支，一般命名是 remotes/{branch_name}

➜  git_test_3 git:(dev) ✗ git fetch
gitremote: Counting objects: 3, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/taoyutong/fortest
   6edd273..8ab0823  dev        -> origin/dev
   
➜  git_test_3 git:(dev) ✗ git branch --all
* dev
  master
  remotes/origin/dev
 
# 如何手动合并？？？

```