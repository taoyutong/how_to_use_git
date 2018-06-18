### 目标

了解 git log 的使用


### 内容
默认不用任何参数的话，git log 会按提交时间列出所有的更新，最近的更新排在最上面。 正如你所看到的，这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

git log 有许多选项可以帮助你搜寻你所要找的提交

```powershell

# 不加参数，按提交时间倒序，列出所有更新
# 包含：
# 1. commit object sha1校验和
# 2. author、提交时间等信息
# 3. 提交注释

➜  git_test_3 git:(dev) ✗ git log

commit 69d527d6ae447f06e27538be6efcc0fd7cef04a2
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 20:55:31 2018 +0800

    commit on dev branch

commit d53a0c3f746937717cd8942ae00dc6e3b18055a2
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 20:02:48 2018 +0800

    tyt modify 1

commit f09cc9771e6b8b2b2be46eef7c57373bfa020f76
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 19:43:10 2018 +0800

    tyt commit 2 files
(END)

# git log -p  查看每次提交的差异
# git log -p -2 查看最近两次提交的差异
# git log --stat -2 显示提交的简略统计信息
# 如下，统计哪些文件 changed 

commit 69d527d6ae447f06e27538be6efcc0fd7cef04a2
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 20:55:31 2018 +0800

    commit on dev branch

 test_1.info | 1 +
 test_4.info | 1 +
 2 files changed, 2 insertions(+)

commit d53a0c3f746937717cd8942ae00dc6e3b18055a2
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 20:02:48 2018 +0800

    tyt modify 1

 test_1.info | 1 +
 test_3.info | 1 +
 2 files changed, 2 insertions(+)
 
# git log --pretty=oneline -2 
# --pretty 可以指定打印 log 的格式
69d527d6ae447f06e27538be6efcc0fd7cef04a2 commit on dev branch
d53a0c3f746937717cd8942ae00dc6e3b18055a2 tyt modify 1
 
```

打印log格式参数，可以参考 [here](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)

### 扩展用途

tbc
