在windows下可以用 FC FILE1 FILE2  来区别两个文件的不同

在linux下可以用 diff -u FILE1 FILE2 来区别两个文件的不同

git log --stat 不仅打出了log 还会显示每一次提交的文件具体增加和减少了什么

git checkout <log 里面的 id > 会将当前的代码reset 到那个时候. 跟我们git checkout
某个branch 差不多。

git diff -u 是执行你上一次git diff log1 log2 的操作

git diff 是 比较working directory 和 staging area 
git diff --staged 是比较staging area 和 the most recent commit 的对比
git diff A B 是比较A和B

在github上新建一个respository :

git remote add origin git@github.com:TonySheva/udaCity.git
git push origin master

fork 是clone 别人的respository 上面的project 到你的respository 
fork到自己的respository后就可以进行基本的操作，clone到本地，add，commit，push
如果想要push到别人的respository 就要pull request 给别人了。
fork更多是开源精神，合伙做事。

git pull origin master = git fetch origin + git merge master master origin/master


git branch 本地分支
git branch -r 远程跟踪分支
