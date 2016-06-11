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

git fetch 是拉取当前respository上面的所有modify过的分支。

和以往的git pull 相比：

日常： 以往的git pull 实际上是会自动拉取最新的origin 的分支上的代码
一般来说，我们local的branch的develop一般会跟github上面的respository去对应。
比如说local的develop 跟 github上的develop是一致的。
当我们从local的develop上modify了一个branch a 在最后需要pull request 的时候
经常会发现github上的develop变了，我们需要回到local的develop 先pull github上的
develop再checkout到我们的branch a 去merge develop
这样我们才能将我们的branch a auto merge到github上的develop

现在： 这里提到的到local上的develop去git pull 我们github上的代码。
实际上我们是可以 用git fetch分开做 
我们local的git branch 实际上不会永远跟github上的一致。（因为现在都是一个需求
一个分支 提交合并 再建一个比较清晰，但你无法知道别人建了啥）

git branch -r 去查看github上现在的所有branch 
比如说我们得到：
origin/develop 
origin/branch a
origin/branch b

git fetch 所拉下来的所有分支是不会帮你建立或者合并的。
你可以自己去合并
如果你想要达到以前的目的，那么你可以直接git merge origin/develop 
然后git mergetool 一下 再提交，就可以auto merge了。实际上就是git pull的操作
比如说，你还可以git merge orign/branch b 这个是别人的分支。。

总结一下当前运用的git 操作。

我们总会在local中保留一条develop 与github上面的一致。

为了让提交和更新清晰，我们会一个issues一条branch，review后merge到develop 再把
branch删除。不出现分支branch上面再起branch的混乱

日常：
建立一个issues，modify 需要的代码文件。
git status 查看modify的文件
git log 可以查看之前的commit记录
git diff ID 可以对比现在这条分支上的代码与ID标记的代码的区别（这里指检查你所提交
的代码和你没modify前的区别）
修改完毕后：
git add 添加需要提交到staging area // git add -u 是提交所有
git commit -m “留下issues 号 和一些信息 便于以后回看”
git push origin branchX
到github上面将pull request 的请求信息补全 
常见can't not auto merge 
回到local -》 checkout develop -》 git pull -》 git checkout branchX -》
git merge develop -》git mergetool -》git push origin branchX

Or： local -》 checkout develop -》 git fetch -》 git branch -r -》 
git merge develop-》 checkout branchX -》 git merge develop -》
git mergetool -》git push origin branchX 


