### git命令总结
##### 不常用命令
`git cat-file -t 哈希值`        // 查看对象的类型
`git cat-file -p 哈希值`        // 查看对象的内容
`git rev-parse 哈希值前几位`           // 通过简短哈希值获取整个哈希值
`git rev-parse HEAD`   // 通过HEAD获取哈希值
`git rev-parse master` // 通过分支名或者标签名获取完整哈希值
`git ls-files -s`               // 查看索引文件 
`git config --global core.autocrlf false`     // 禁用自动转换换行符
`git reset --hard`           // 版本回退
`git log --oneline`    // 查看该版本之前版本提交日志
`git reflog`          // 查看所有提交日志  记录各种指针的常见操作
`git branch -v`      // 查看分支详细信息
`git branch -vv`     // 查看本地分支与远程分支的对应关系
`git checkout 分支`   // 切换到该分支
`git checkout -b test`   // 同时完成创建test分支并检出（切换）test分支的操作
`git log --oneline --all --graph`   // 在命令行中以字符的形式尽量接近图形化的方式展示分支
`gitk --all`      // 图形化显示所有分支
`git diff`   // 比较所有文件工作区和暂存区的差异
`git diff -- 指定文件`       // 比较指定文件工作区和暂存区的差异，后面可以跟多个文件，用空格隔开
`git diff HEAD或者哈希值`   // 查看工作区与当前分支最新的提交之间的差异
`git diff --cached`     // 查看暂存区和最新提交之间的差异
`git diff a63d5b2..aafbaa8`   // 通过哈希值比较两个提交，中间加两个点


##### 比较差异
`git diff`
比较工作区和暂存区
 
`git diff HEAD`
比较工作区和当前分支最新的提交，你可以把HEAD换成别的分支的名字，比如test分支，"git diff test"表示比较当前工作区和test分支最新的提交之间的差异，也可以把HEAD替换成任何一个commit的ID，表示比较当前工作区和对应提交之间的差异。
 
`git diff --cached`
比较暂存区和当前分支最新的提交
 
上述命令都是比较所有文件的差异，如果想要指定文件，可以使用"--"指定文件的路径，文件路径可以有多个，用空格隔开。
 
`git diff -- file1`
`git diff -- ./file1`
只比较工作区和暂存区中file1文件的差异
 
`git diff -- file1 file2`
只比较工作区和暂存区中file1以及file2文件的差异
 
`git diff -- dir1/d1/f1`
只比较工作区和暂存区中dir1/d1/f1文件的差异
 
`git diff -- dir1/`
只比较工作区和暂存区中dir1目录中所有文件的差异
 
`git diff HEAD -- ./file1`
只比较工作区和当前分支最新的提交中file1文件的差异，HEAD可以替换成分支名或者commitID
 
`git diff testbranch -- ./file1`
只比较工作区和testbranch分支最新的提交中file1文件的差异
 
`git diff --cached testbranch`
比较暂存区和testbranch分支最新的提交
 
`git diff --cached testbranch --./file1`
只比较暂存区和testbranch分支最新的提交中file1文件的差异
 
`git diff HEAD~ HEAD`
比较当前分支中最新的两个提交之间的差异
 
`git diff HEAD~ HEAD -- file1`
比较当前分支中最新的两个提交中的file1文件的差异
 
`git diff commitID1 commitID2`
比较两个commit之间的差异
`git diff commitID1..commitID2`
同上，比较两个commit之间的差异，两个命令等效
 
`git diff branch1 branch2`
比较两个分支上最新提交之间的差异
`git diff branch1..branch2`
同上，比较两个分支上最新提交之间的差异，两个命令等效


##### 撤销
撤销已经添加到暂存区中的修改，即让暂存区与最近的提交保持一致，可以使用如下命令，如下三条命令等效
`git reset`
`git reset HEAD`
`git reset --mixed HEAD`

也可以针对某些文件撤销暂存区中的修改，命令如下
`git reset HEAD -- file1 file2`

撤销所有暂存区和工作区中的所有变更
`git reset --hard HEAD`

回退到指定的提交
`git reset --hard commitID`

你已经将部分提交暂存到了暂存区，然后继续在工作区工作，工作区产生了新的变更，但是这些新变更没有添加到暂存区，此时你创建了提交，刚刚创建完提交你就后悔了，你想要的回到提交创建前一刻的状态，可以使用如下命令
`git reset --soft HEAD~`

使用如下命令可以撤销工作区中file1文件的相关变更，可以细分为两种情况
情况一：你先修改了file1，并且暂存了，然后又修改了file1，在工作区产生了新的变更，此时执行上述命令，会将工作区中最新的变更撤销，工作区中的file1将会变成暂存区中file1的状态。

情况二：你修改了file1，暂存区中没有file1相关的变更，此时执行上述命令，会将工作区中最新的变更撤销，工作区中的file1将会变成最近一次提交中file1的状态。
`git checkout -- file1`



##### 合并分支
表示将A分支合并到当前分支
`git merge A`


`git merge --no-ff A`
上述命令表示将A分支合并到当前分支，但是明确指定不使用”Fast-forward”的模式进行合并， “–no-ff”中的”ff”表示 “Fast-forward”，即使合并条件满足”Fast-forward”模式，也不使用快进的方式进行合并，而是使用创建合并提交的方式进行合并。

`git merge --ff-only A`
上述命令表示将A分支合并到当前分支，但是只有在符合”Fast-forward”模式的前提下才能合并成功，在不符合”Fast-forward”模式的前提下，合并操作会自动终止，换句话说就是，当能使用”Fast-forward”模式合并时，合并正常执行，当不能使用”Fast-forward”模式合并时，则不进行合并。


`git merge --no-edit A`
上述命令表示将A分支合并到当前分支，但是没有编辑默认注释的机会，也就是说，在创建合并提交之前不会调用编辑器（上文的示例中会默认调用vim编辑器，以便使用者能够有机会编辑默认的注释信息），换句话说就是，让合并提交直接使用默认生成的注释，默认注释为” Merge branch ‘BranchName’ “


`git merge A --no-ff -m "merge A into master,test merge message"`
上述命令表示将A分支合并到当前分支，并且使用-m参数指定合并提交对应的注释信息。



`git commit -am "注释"`
上述命令表示：省略”git add”操作，自动完成暂存并创建提交，换句话说就是，直接将工作区中的所有变更创建成一个提交，注意：完全新建没有被git跟踪过的文件不会自动暂存。

`git rm testfile`
上述命令表示：删除testfile文件，并自动将变更暂存，效果相当于在文件系统中删除testfile，然后手动暂存。

`git mv testfile tf`
上述命令表示：将testfile重命名成tf，并自动将变更暂存，效果相当于在文件系统中重命名了testfile，然以手动暂存。



##### 推送到远程仓库
`git push --all`
此命令表示当本地分支与上游分支同名时，push所有分支的更新到对应的远程分支。

`git fetch`
此命令表示获取远程仓库的更新到本地，但是不会更新本地分支中的代码。

`git pull remote branchA`
此命令表示将remote仓库的A分支pull到本地当前所在分支，如果你想要pull到本地的A分支，需要先checkout到本地A分支中。

`git pull remote branchA:branchB`
此命令表示将remote仓库的A分支pull到本地的B分支，在成功的将远程A分支pull到本地B分支后（如果远程A到本地B的pull操作失败了，后面的操作不会执行），再将远程A分支pull到本地的当前所在的分支。

`git pull`
此命令表示当本地分支与上游分支同名时，对当前分支执行pull操作，对其他分支执行fetch操作，具体的差异主要取决于对应的远程分支有没有更新。