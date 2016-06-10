在windows下可以用 FC FILE1 FILE2  来区别两个文件的不同

在linux下可以用 diff -u FILE1 FILE2 来区别两个文件的不同

git log --stat 不仅打出了log 还会显示每一次提交的文件具体增加和减少了什么

git checkout <log 里面的 id > 会将当前的代码reset 到那个时候. 跟我们git checkout
某个branch 差不多。

git diff -u 是执行你上一次git diff log1 log2 的操作

git diff 是 比较working directory 和 staging area 
git diff --staged 是比较staging area 和 the most recent commit 的对比
git diff A B 是比较A和B
