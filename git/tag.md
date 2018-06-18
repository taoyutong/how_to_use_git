### 目标

了解 标签 的使用


### 内容

参考[官网文档](cf.jd.com/pages/viewpage.action?pageId=108803427) 获取详细基础信息，以下只记录个人使用点和理解点

两种标签类型

+ lightweight tag 

	轻量标签，理解为 commit object 的一个指向，或者别名，不包含其他信息，创建时也不支持各种参数

+ annotated tag 

	附注标签，是一个 tag object , 包含注释信息等，创建方法如下方



```powershell

# 创建一个 附注标签
# git tag -a {tag_name} -m {tag_info}

➜  git_test_3 git:(dev) git tag -a v1.0 -m 'tyt test create tag'

# 查看本地 tag 列表
➜  git_test_3 git:(dev) git tag
v1.0

# 查看 tag 详细信息
# 包含指向的 commit object 
➜  git_test_3 git:(dev) git show v1.0

tag v1.0
Tagger: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 23:46:12 2018 +0800

tyt test create tag

commit 6edd27381d3b19ddaf1bc8e35b4c03b5cb5e2723
Author: platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local>
Date:   Mon Jun 18 23:05:28 2018 +0800

    test

diff --git a/test_4.info b/test_4.info
index cafa4d1..590aa3b 100644
--- a/test_4.info
+++ b/test_4.info
@@ -1 +1,2 @@
 add 1 file
+add some word again
(END)

# 为过去的某个版本打标
# git log 查看某次提交的 commit object 校验和
# git tag -a {tag_name} {校验和} -m {tag_info}
➜  git_test_3 git:(dev) git log --pretty=oneline -4
➜  git_test_3 git:(dev) git tag -a v0.5 f09cc9771e
# 在交互模式添加 tag_info

➜  git_test_3 git:(dev) git tag
v0.5
v1.0

# 默认在本地创建标签，要共享标签，需要 push remote 
# git push {remote_name} {tag_name}
➜  git_test_3 git:(dev) git push origin v1.0
Counting objects: 1, done.
Writing objects: 100% (1/1), 179 bytes | 0 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To https://github.com/taoyutong/fortest.git
 * [new tag]         v1.0 -> v1.0


```

### 扩展使用

tbc