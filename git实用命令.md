

* ### nodeppt -> markdown的ppt
	* 地址: https://github.com/ksky521/nodePPT#mixed-code

* ### 参考来自 git-scm.com -> git文档
	* https://git-scm.com/book/zh/v2


[slide]
* ### 个人单拉分支开发,开发完成后需要合并到主分支
* git merge testing --squash
* 结果:
	- 普通merge 会把两个分支的commit记录合并成一个,并按照时间顺序排列
	- --squash 参数会忽略testing的所有commit记录,合并成一个记录
[slide]
* ### 个人单拉分支开发,开发完成后需要合并到主分支
* rebase 
	- 不建议使用,一般情况下不会出问题
	- 出问题了,可以根据命令行提示用 git rebase --abort 撤销
	- 出问题的原因是因为我们在不同分支对同一个文件做了修改,并且将其commit

[slide]
* ### merge,pull代码遇到问题,想要反悔
	- git reset --soft  相当于撤销 commit 操作,修改和add的效果还在,也就是说,文件是 staged状态(一般显示绿色)  
	- git reset --hard  直接把文件恢复到上一次commit时候的状态,之后的修改全被扔掉
	- git reset --mixed(不加参数默认)  撤销commit 和 add操作, 文件恢复到 modified状态(一般显示红色)

* 执行merge命令后,遇到问题,想终止当前的merge操作
	- git merge --abort

[slide]
* ### 某次提交的代码出错了,想要撤销这次commit的修改
	- git revert hash-key(想要撤销的提交记录的hash值,也可以是 HEAD  HEAD^)

* 注意: revert操作不能恢复 merge 操作

[slide]
* ### 在自己分支开发,需要紧急切换到master分支改bug
	+ git stash 会处理工作目录"脏"的状态,只要是被追踪的文件(tracked)的文件,stash会保存文件的修改(modified)和暂存(staged),然后把当前目录指向最近一次的提交
    + git stash list  列出所有的stash记录,最近的一次stash在最上面
    + git stash apply 把stash列表最上面的一次stash 应用, 也可以使用 git stash apply stash@{4} 来恢复你的往前数第四次修改
    + 已经apply 过的 stash不能再次apply;
    + 比如你暂存了 test.ts,然而你忘了自己的暂存,对test.ts进行了新的修改.那么这时候你再去apply stash的时候,会报错

[slide]

* ### 提交代码后,发现还有代码需要修改,想要把这两次修改一同提交

- git commit --amend   注意: 这后面没有任何参数!!!

[slide]
* ### 代码提交前,状态预览:
    git status我们可以看到当前工作区内文件的修改,暂存,是否有新添加的为traced的文件等状态,这里说几个status的参数
    * git status -s   表示 git status -short --> 列出被修改的文件,通过MA来标识文件是否被暂存/修改/traced
    * git diff --> 查看已暂存的和未暂存的修改,与status不同的是,它会展示出文件的具体修改
        * git diff 比较当前工作目录和暂存区快照之间的差异.也就是修改之后还没有暂存起来的变化内容.也就是说是当前文件和上一次 add操作之间的差异.所以,你add -A之后,即使没有commit,立即执行git diff的话,也是没有任何内容返回的
        * git diff --staged  查看暂存区那些将要提交的内容和上次commit之间的差异.(也可以用git status --cached)
    * git status -sb    -b显示当前分支和远程分支的差异

[slide]
## 查看提交历史
### git log  --> 无任何参数,git log 会列出"所有的"commit记录,"最近"的一次在最上面
    * git log -p  列出每次commit的详细内容.  git log -p -2  列出最近的两次提交
    * git log --stat 可以查看每次commit的简略信息,它会列出所有被修改过的文件
    * git log --pretty 可以指定显示的log格式.这个基本不用,这里不提
[slide]
* ### 根据某些条件，找出与之相关的提交
      * git log --author=guoqiang  列出guoqiang的提交
      * git log -Sbugfix   列出添加或者移除了bugfix字符串的提交,比如某个人删除了一行带啊没,你可以直接-Sstring找出是谁干的
      * git log --grep=string    commit -m里面带有string字符串的提交
 * git log --since/after --until/before="2014-10-11"  列出指定时间前/后的提交历史
[slide]
### 使用git调试
* git blame file1.js  查看某个文件每一行的**最后修改时间**以及是**被谁**修改的
* git blame -L 12,16 file1.js   用-L参数来限定,只显示12行到16行的记录
* git bisect 用二分法定位哪次提交导致了问题,当然前提是你得大概知道最后一次正确的代码是哪次


