### 目标
理解如下

- git repository, work directory,staging area 概念

- 文件状态、状态转换

- .git 文件夹

- Git是一个内容寻址系统，并在此基础上，提供了版本控制的UI

- git对象，git如何存储数据

##### 仓库、工作目录、stage区、快照

创建一个文件夹A，执行 `git init`, A文件夹下生成一个 .git 目录，为初始化仓库

.git 目录中，保存了 git 使用的所有信息。

A文件夹以及其子文件夹，是工作目录，这里的文件，可能交由git管理（被git跟踪等）

而本地仓库，可以理解为本地交给git管理的那些文件（个人理解，不确定是否符合严格的定义）以及提交历史

将文件提交给git仓库，需要先add到stage区，stage区可以理解为中间状态区，commit后，才会称为快照版本，多次commit，生成多个快照版本，每次都是指向最新快照版本。

##### 提交文件的核心流程

一定是从stage区，提交到快照版本。所以提交前要确认新文件，或者本次修改是否已经git add到了stage区。

##### 文件状态以及转换

- 新建一个文件，此时文件是 *untracked* 状态，表示 git 还不管此文件
- 想让 git 管，就 `git add filename ` ，此时，文件是 *staged* 状态，表示 git 可以管理此文件，并放在 staged 区，
- 如果 `git commit -m 'commments '`了，那么 file 就被提交到 *快照版本* 中
- 已经 staged 的文件，如果再次被修改了，则文件就变成了 *modified*  状态
	+ 如果想提交本次更新，还需要 `git add filename` , 然后 `git commit `
	+ 如果不想要本次修改，想回复到上次提交的状态，如何执行？
	+ 删除掉修改，又会变成 *unmodified*  状态

###### 查看文件状态

查看当前的文件状态，用 `git status `，此时提示文案较多较全面

用 `git status --short ` 可以简洁看状态，同时有颜色区分，红色表示没有被stage，绿色表示已经stage

```shell
$ git status -s
 M README  # M 可以有两个位置，本行的是在右侧，修改了，未进入stage
MM Rakefile  # 本行两个M，表示修改了进入stage后，又修改了
A  lib/git.rb
M  lib/simplegit.rb  #本行的M在左侧，表示修改了，进入stage，
?? LICENSE.txt  #新添加，未跟踪的
```


###### 状态变换

但实际并不是只提交就ok了，会遇到恢复文件，取消提交等情况，所以需要知道如何操作才能控制git中的文件状态，实际上就是文件从 *unstage* 状态（我自定义的一个状态，指untracked的文件，以及modified但未stage的内容）与 *stage区* 、*快照版本* 之间的切换。

- 新加文件进入stage区 

	`git add file1 file2 file3 ...`  or `git add .` 当前路径以及子路径的所有改动
	
- 从stage区删除某些文件，这里的删除又分为两种情况
	+ 只是从stage中删除，仍在本地保留
	
	 `git rm --cache file` 此时文件会被置为 *untracked* 状态
	
	+ 从stage中删除，且从磁盘中删除 

		`git rm -f file ` -f表示force强制删除
	
- 文件被修改（*modified* ）状态or被删除，但未到stage区，想恢复到最初的stage状态 

	`git  checkout .`

- 文件被修改or删除，已提到stage区，想恢复到快照版本

	 `git stash` 
	 
	 把所有没有commit的修改，存到stash中，此时 git status 看到的，将会是上一次快照版本的内容。也可以通过 `git stash pop` 将内容恢复，但此时不是stage状态。
- 文件已经被 commit 了，想从 快照版本2 ，恢复到快照版本1 。

	每次 commit , 会生成一个快照版本，HEAD指针指向最新的版本，HEAD^表示上个版本 ，HEAD^^表示上两个版本，HEAD~3表示上三个版本，可以通过修改HEAD指针的指向，来回退到某个版本

	比如：回退到上个版本 `git reset --hard HEAD^`  
	
	关于更详细的代码回滚，[这篇文章讲的很清晰](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert-%E7%9A%84%E9%80%89%E6%8B%A9) ，可以详细阅读，需要先了解branch管理

	后面为了自己记忆方便，会用自己的思维方式重新翻译下上述文章，写在分支一章中
	
##### Git 如何管理文件
Git没有保存以文件形式保存，而是以内容形式保存，如何理解？一个文件可能经过多次commit，存在多个版本，在Git中，不是每个版本的文件存一份，而是保存了每一次的变动，多次变动的累加，组成了这个文件。有这个概念后，再去理解 commit object \ tree object \ blob object 会有思路些

Git 在所有数据存储前，都是用sha-1散列，计算校验和（基于 Git 中文件的内容或目录结构计算）

Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

##### .git 文件
目录如下

```
$ ls -F1
HEAD
config*
description #  给gitweb 展示用的，不需要关注
hooks/
info/
objects/
refs/

```
##### git如何保存对象
在上面的文件目录中，四个特别重要

- HEAD文件，保存当前分支指向

```shell
➜  git_test_2 git:(master) cat .git/HEAD 
ref: refs/heads/master
```

- index 文件，保存stage信息

	index文件是个二进制文件，用cat命令是无法打开的, 使用 `hexdump -C index` 可以看到信息，数值含义可参考 [这里](https://link.jianshu.com/?t=https://github.com/git/git/blob/master/Documentation/technical/index-format.txt) （基本上用不上）
	
- refs 文件夹，指向数据的 commit object 的指针

- object 文件夹，包含数据内容

**走一个实际流程，来解读git如何保存数据**

初始化一个 repo

```shell
➜  codes git init git_test_2
Initialized empty Git repository in /Users/platfrom-test/Documents/taoyutong/codes/git_test_2/.git/

# 添加一个文件
➜  git_test_2 git:(master) echo 'tyt test 01'>> test_1.log

# check objects 下，无文件生成，当然，因为还没有 git add , git 自然不会处理这个文件
➜  git_test_2 git:(master) ✗ find .git/objects -type f

# git add 
➜  git_test_2 git:(master) ✗ git add .  

# 查看 objects 里内容，发现生成了文件 
# git是个key-value的数据库，value是内容，key是sha1生成的校验和，其中前两位是文件名 a2，后几位是文件名
# 该流程说明，git add后，文件进入 stage 区，git跟踪了文件内容，保存了文件
➜  git_test_2 git:(master) ✗ find .git/objects -type f
.git/objects/a2/e990d32c4141171c4c0e61f1f0d64988bfd135

# 使用 底层命令 cat-file 查看文件内容
# 发现为本次提交的内容
➜  git_test_2 git:(master) ✗ git cat-file -p a2e990d32c4141171c4c0e61f1f0d64988bfd135
tyt test 01

# 提交文件
➜  git_test_2 git:(master) ✗ git commit -m 't'
[master (root-commit) 64d3793] t

# 再次查看，发现文件变多了，除了最初的a2保存了原始内容，还多了两个文件
➜  git_test_2 git:(master) find .git/objects -type f                               
.git/objects/1c/e37a90940b52d9da97a89c62e8ddfcf76b3a56
.git/objects/64/d379346f6807df7e2f2847c02b3a1319dfe7f3
.git/objects/a2/e990d32c4141171c4c0e61f1f0d64988bfd135

# 使用 cat-file 查看文件内容
# 发现这两个文件分别是 1:commit object。2、blob object 
➜  git_test_2 git:(master) git cat-file -p 64d379346f6807df7e2f2847c02b3a1319dfe7f3
tree 1ce37a90940b52d9da97a89c62e8ddfcf76b3a56
author platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local> 1529253400 +0800
committer platfrom-test <platfrom-test@platform-testdeMacBook-Pro.local> 1529253400 +0800

t

# 查看文件内容，发现 tree object 的内容如下
# 100644 表示文件模式，参考了 unix 文件模式，其他包括 100755 可执行文件 等，只要了解这是和文件类型的标识即可
# 指向一个 blob 文件，blob文件的 sha1 以及文件名 
➜  git_test_2 git:(master) git cat-file -p 1ce37a90940b52d9da97a89c62e8ddfcf76b3a56
100644 blob a2e990d32c4141171c4c0e61f1f0d64988bfd135	test_1.log

# 用 cat-file -t 可以查看文件类型
# 这三个文件分别是 blob object \ commit object \ tree object 
➜  git_test_2 git:(master) git cat-file -t 1ce37a90940b52d9da97a89c62e8ddfcf76b3a56 
tree
➜  git_test_2 git:(master) git cat-file -t 64d379346f6807df7e2f2847c02b3a1319dfe7f3
commit
➜  git_test_2 git:(master) git cat-file -t a2e990d32c4141171c4c0e61f1f0d64988bfd135
blob

# 模拟下一次提交多个文件，看看tree object 如何关联？
# 多次提交，每次多几个文件，看看 tree object 如何保证最新？
# commit object 来表示历史提交链
# 和 git log 结合起来看

```

###### 理解blob object \ commit object \ tree object
blob object 中保存version数据，是变化数据

tree object 关联多个 blob object 和 tree object ，一个tree object 包含一个版本完整的数据

commit object 中保存提交信息，或者说，是包含了该版本的信息，包含：

+ 它先指定一个顶层树对象，代表当前项目快照
+ 作者/提交者信息（依据你的 user.name 和 user.email 配置来设定，外加一个时间戳）
+ 提交注释

通过上面的流程，知道每一次提交，都会生成这三个对象。如果再提交一次，会怎么样呢？


```shell
# 修改test_1.log文件，未git add ,发现object文件夹下没有变化，git还没有跟踪这次改变
➜  git_test_2 git:(master) ✗ find .git/objects -type f
.git/objects/1c/e37a90940b52d9da97a89c62e8ddfcf76b3a56
.git/objects/64/d379346f6807df7e2f2847c02b3a1319dfe7f3
.git/objects/a2/e990d32c4141171c4c0e61f1f0d64988bfd135

➜  git_test_2 git:(master) ✗ git add .

# 添加后，发现多了一个文件 9d 
➜  git_test_2 git:(master) ✗ find .git/objects -type f
.git/objects/1c/e37a90940b52d9da97a89c62e8ddfcf76b3a56
.git/objects/64/d379346f6807df7e2f2847c02b3a1319dfe7f3
.git/objects/9d/549a61a8d6154a801f68d85124d5814c9afba2
.git/objects/a2/e990d32c4141171c4c0e61f1f0d64988bfd135

# 看下文件内容
# blob里包含修改后的内容，是完整的文件，这里和我以前的想法冲突了，我以为git是内容的变化，每次整合，实际上blob对应一个文件完整的version内容
➜  git_test_2 git:(master) ✗ git cat-file -p 9d549a61a8d6154a801f68d85124d5814c9afba2
tyt test 01
add some words
➜  git_test_2 git:(master) ✗ git cat-file -t 9d549a61a8d6154a801f68d85124d5814c9afba2
blob

# 现在，我们知道最初的版本内容，也知道修改后的内容，可以随时将内容读出，比如
➜  git_test_2 git:(master) git cat-file -p a2e990d32c4141171c4c0e61f1f0d64988bfd135 > version_1_content.txt
➜  git_test_2 git:(master) ✗ git cat-file -p 9d549a61a8d6154a801f68d85124d5814c9afba2 > version_2_content.txt

# 但我们都是根据 sha1校验和 来获取某个版本的内容的，这样不便于记忆，而且库中也不仅仅是一个文件，一次提交多文件时怎么办呢？
# 这就是 tree object 解决的问题了
# 上面我们发现，一次commit，生成了一个tree object 
# git是如何做版本控制的呢，怎么表达多次提交
# 可以从实际使用的角度来理解git的原理
# 先会用，再根据使用的经验，去了解它的原理
# 如何把凌乱的思路，梳理结构化

➜  git_test_2 git:(master) find .git/objects -type f                               
.git/objects/1c/e37a90940b52d9da97a89c62e8ddfcf76b3a56
.git/objects/64/d379346f6807df7e2f2847c02b3a1319dfe7f3
.git/objects/66/b1ecf7c9328df7273cbc4aff9c0e2d20eb8af1
.git/objects/7b/0f782003174824b5376af2e37c8d15b007ba6c
.git/objects/9d/549a61a8d6154a801f68d85124d5814c9afba2
.git/objects/a2/e990d32c4141171c4c0e61f1f0d64988bfd135



```

理解了上面的流程，才能理解为什么说git是内容寻址的系统，通过指针，寻找到对应object，根据object,关联到对应的数据。





	




	
##### 辅助功能／使用技巧
###### 文件移动，git如何跟踪
*重命名后，git status 改变，如何更新staged ？*

- 直接在命令行中 mv fileA fileB git并不会关联重命名操作

	为了更新到staged，需要进行三步操作

	mv fileA fileB

	git rm fileA

	git add fileB

	三次命令较繁琐，因此可以用 git mv fileA fileB 来代替，该命令相当于上述三个命令

	还有一种方式就是，直接 mv 各种文件，最后 git add . 将所有改动全都保存到 staged 即可
	
###### 制定规则，过滤某些文件提交
参考资料为 [git help](https://help.github.com/articles/ignoring-files/)、[stack question](https://stackoverflow.com/questions/22906851/when-would-you-use-git-info-exclude-instead-of-gitignore-to-exclude-files)

两种情况，两种设置方式:

1. 在project下设置通用的过滤规则，对任何人都适用

	方法：在repo下，创建 .gitignore 文件，加上过滤规则，参考资料中已经汇总了一些推荐规则
	
	也可以在本地，创建全局的规则，对每个repo都生效，不过这个常见吗？怎么对其他人生效呢？
	
2. 希望只对自己生效，不影响别人。

	方法：在repo的 .git/info/exclude中，添加规则
	
	
	
	

