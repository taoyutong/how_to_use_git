### 目标

了解 git diff 的使用


**缓存区** diff **修改区**

```powershell
➜  git_test_3 git:(dev) echo 'add some word again ' >> test_4.info

# git diff 不加任何参数，用于比较未缓存数据与缓存区数据差异
➜  git_test_3 git:(dev) ✗ git diff

diff --git a/test_4.info b/test_4.info
index cafa4d1..590aa3b 100644
--- a/test_4.info
+++ b/test_4.info
@@ -1 +1,2 @@
 add 1 file
+add some word again
(END)

# 如果已加入缓存区，再 diff , 不会有区别
➜  git_test_3 git:(dev) ✗ git add .
➜  git_test_3 git:(dev) ✗ git diff

# git diff --cached ，比较缓存区与上一个版本的差异
➜  git_test_3 git:(dev) ✗ git diff --cached

diff --git a/test_4.info b/test_4.info
index cafa4d1..590aa3b 100644
--- a/test_4.info
+++ b/test_4.info
@@ -1 +1,2 @@
 add 1 file
+add some word again


# 如何 diff 两个版本之间的差异呢？
```