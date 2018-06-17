### 目标
提炼知识点
- 版本库、工作目录 概念
- 工作目录下文件状态、状态转换、状态转换对应的具体命令操作
- .git 文件夹的作用、在状态转换过程中 .git 文件夹下都有哪些变化 

##### 仓库、工作目录、stage区、快照

创建一个文件夹A，执行 `git init`,A文件夹下生成一个 .git 目录。 

.git 目录中，保存了 git 使用的所有信息。

A文件夹以及其子文件夹，是工作目录，这里的文件，可能交由git管理（被git跟踪等）

而本地仓库，可以理解为本地交给git管理的那些文件（个人理解，不确定是否符合严格的定义）

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

