重命名后，git status 改变，如何更新staged ？

直接在命令行中
mv fileA fileB 
git并不会关联重命名操作

为了更新到staged，需要进行三步操作

mv fileA fileB

git rm fileA

git add fileB 

三次命令较繁琐，因此可以用 
git mv fileA fileB 来代替，该命令相当于上述三个命令

还有一种方式就是，直接 mv 各种文件，最后 git add . 将所有改动全都保存到 staged 即可 