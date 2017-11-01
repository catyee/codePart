
- ## 文字很多,但是大家不必多看,听我说就是了;
  - 有问题随时提,随时解答,随时实验
- ## 主要看图说话

## 
* 在 Git 中任何 已提交的 东西几乎总是可以恢复的。 
* 甚至那些被删除的分支中的提交或使用 --amend 选项覆盖的提交也可以恢复
* 然而，任何你未提交的东西丢失后很可能再也找不到了。
* git设置别名真的很好用,具体怎么改,百度

## 先扫盲
* 一个重要的问题,参数前面一根横杠和两个横杠的区别 
  - -m 是 --mesage的简写

* blob(binary large object) 
  - 二进制大对象,是一个可以存储二进制文件的容器.常常用来在数据库中存储图片或者音频等二进制大文件.
  - git就是用blob保存文件的快照的

* 干净的工作目录:
  - 指的是你执行 git status 的时候,没有任何modified和staged状态的文件
  - 即执行完一次commit 或者 stash后,你的当前工作目录就是干净的
  - 在合并分支/pull之前,尽量保持工作目录干净


* ### 这些非常重要,若你理解了git的思路和基本工作原理,用起来就会知其所以然,游刃有余.
--
  + git直接记录快照,而非比较差异.git会把数据看作是当前小型文件系统的一组快照.每次你提交更新,或者在git中保存项目状态时,它主要对当时的"全部文件"制作一个快照并保存这个快照的"索引"

* ### git只添加数据,即使是你删除了某些文件,也只会使你的.git文件夹更大而已.
  + 这意味着,你不必担心任何的误操作会对删除/破坏你的旧文件.当前前提是你得学会"撤销"相关命令.
- ### 一个commit对象和它的指向

  <img src="/git-pics/git-commit-tree.png">


### git中文件的三种状态 
  - 这三种状态是git各种操作的基础.你的文件一定会处于一下三种状态之一
    * modified --> 已经修改了文件,但是没有保存到git数据库中(检测到文件修改后自动加入
    * committed -> 数据已经安全的保存在本地数据库中(commited后加入
    * statged  --> 表示对一个已修改文件的当前版本做了标记,它会被包含在下次提交的快照中(add之后加入


### 由此引入git的三个工作区的概念
* Working Directory(工作目录)
    - 当我们切换分支的时候,会发现我们当前目录下的文件发生了改变.这是因为我们的working directory是对项目的某个版本独立提出出来的"内容".通过切换版本/分支,你把对应的文件从仓库中拿到这里来,然后进行修改
* Staging Area(暂存区域)
    * Staging Area是一个"文件"它保存了下次将要提交的"文件信息列表",它也被叫做"索引"

* .git directory(仓库)
   - 这是保存一个git仓库的元数据(这是啥?)和对象数据库(这又是啥?)的地方.我们clone操作的时候,就是clone的这个仓库


- ## .git目录
  - .git/HEAD   -> 当前分支的指向   ref: refs/heads/master 说明当前是 master分支
  -  config 文件包含项目特有的配置选项
  - info 目录包含一个全局性排除（global exclude）文件，用以放置那些不希望被记录在 .gitignore 文件中的忽略模式（ignored patterns）。 
  - hooks 目录包含客户端或服务端的钩子脚本（hook scripts）,这个暂时用不着
  - objects 目录存储所有数据内容；
  - refs 目录存储指向数据（分支）的提交对象的指针；
  - HEAD 文件指示目前被检出的分支；
  - index 文件保存暂存区信息


* git 中文件的状态周期,会随着我们对文件的add/修改/commit而改变

  <img src="/git-pics/lifecycle.png">


### traced 和 untraced文件 --> 通过add把文件假如追踪 --> 我们接下来的操作都是对已跟踪文件的操作
    * 有时候,你新加入了一些文件,比如引入一个新的js,一张新的图片,你是不能直接commit -am的.
    * 因为新加入的文件是未被追踪的.你需要手动add他们.
    * 已被追踪文件指的是那些被纳入版本控制的文件,在上一次的快照中有他们的记录.注意:只有被追踪的文件,才会有 modified,staged committed状态.
    * git add命令接受一个文件或者目录作为参数,如果是目录,它会开始跟踪目录下的所有文件



* ### git add命令
             * 可以用于把一个文件加入追踪,
             * 也可以把已修改(modified)的文件放入暂存区(staging area) 
             * 还可以用于"合并时将有冲突的文件标记为已解决冲突状态".
             * --> 所以它的功能综合看来应该是"把文件添加到下一次提交中".

### 状态预览:
    git status我们可以看到当前工作区内文件的修改,暂存,是否有新添加的为traced的文件等状态,这里说几个status的参数
    * git status -s   表示 git status -short --> 列出被修改的文件,通过MA来标识文件是否被暂存/修改/traced
    * git diff --> 查看已暂存的和未暂存的修改,与status不同的是,它会展示出文件的具体修改
        * git diff 比较当前工作目录和暂存区快照之间的差异.也就是修改之后还没有暂存起来的变化内容.也就是说是当前文件和上一次 add操作之间的差异.所以,你add -A之后,即使没有commit,立即执行git diff的话,也是没有任何内容返回的
        * git diff --staged  查看暂存区那些将要提交的内容和上次commit之间的差异.(也可以用git status --cached)
    * git status -sb    -b显示当前分支和远程分支的差异


- ### 移除文件 - 文件未被追踪
  - git clean -f 清理没用的未tracked的文件,一般当我们不小心在工作目录中加入不需要的新文件,或者merge后想反悔的时候用
- ### 移除文件 - 文件已经被追踪
  - 直接删除文件,然后正常提交就行,git rm或者git mv方法感觉太繁琐


## 查看提交历史
### git log  --> 无任何参数,git log 会列出"所有的"commit记录,"最近"的一次在最上面
    * git log -p  列出每次commit的详细内容.  git log -p -2  列出最近的两次提交
    * git log --stat 可以查看每次commit的简略信息,它会列出所有被修改过的文件
    * git log --pretty 可以指定显示的log格式.这个基本不用,这里不提
* ### 根据某些条件，找出与之相关的提交
      * git log --author=guoqiang  列出guoqiang的提交
      * git log -Sbugfix   列出添加或者移除了bugfix字符串的提交,比如某个人删除了一行带啊没,你可以直接-Sstring找出是谁干的
      * git log --grep=string    commit -m里面带有string字符串的提交

      * git log --since/after --until/before="2014-10-11"  列出指定时间前/后的提交历史

- ### 撤销操作 -->很有用.但是注意:有些撤销操作是不可逆的.而这些不可逆的操作是有可能导致工作丢失的几个地方之一
    * git commit --amend
      - 执行一次commit之后,如果你发现-m的信息写错了,或者漏掉了一些修改.那么可以通过--amend参数将后面的修改添加到一条commit记录中
 
- ### 取消暂存的文件--即被add的文件
    * git reset HEAD file1 来取消刚才对file1的add操作,也就是把file1从暂存区拿出来,回到modified状态
    * git checkout file1 撤销对file1的修改  
        - 注意!!!! 这会丢弃你所有的修改,很危险!!!即使是ctrl+z都不行!!!! 
        - checkout本质是用另一个文件覆盖了你修改的文件.
    * git checkout .丢弃所有修改


## 远程仓库的使用,这个不多说,因为我至今遇到这方面的问题,一般用的时候都是直接git clone 然后其他的命令现用现查,说了也没啥印象,知道有这个事情就得了


## 打标签 git tag  --> 给历史中的"某一次提交"打上tag
    * git tag --> 列出所有的tag
    * git tag -a tagName -m"我的tag"   -a添加tag,,-m  tag的说明.
    * git tag -l 'tag*' 查找tag
    * tag是一次特殊的commit


## git 分支
### 首先回顾一下git是怎样保存数据的
    * git保存的是一系列"不同时刻的快照"--> 这些快照仅仅是这一版本中被改变的那些文件,而不是文件的变化或者差异
    * 当我们进行commit操作的时候,git会保存一个"提交对象"( commit object).该提交对象包括一个指向暂存内容快照的指针
### 一个git提交对象
<img src="/git-pics/git-commit-tree.png">
  - ### 对上图的解读
	- 假设我们的当前目录中有三个将要被暂存和提交的文件,
	- 暂存操作会为每一个文件进行哈希校验,然后把当前当前版本的文件快照(blob对象)保存到.git仓库
	- 最终将哈希校验和加入到暂存区,等待提交
	- 执行git add -A     git commit -m"mm" 之后,git仓库中有5个对象
		+ 三个blob对象,保存着文件快照
		+ 一个数对象(tree),记录着目录结构和blob对象索引
		+ 一个提交对象(commit object),包含一个指向本次tree的指针,和-m之后保存的信息,还有作者信息,另外还有一个指向上次提交提交对象的指针(所有git仓库的第一次提交的提交对象没有这个指向前辈的指针,因为它是第一次啦)

### git的分支,本质上就是指向提交对象(commit object)的"可变指针"
#### git branch testing 创建分支 -> git 创建分支的本质是创建了一个可以移动的新指针
	- git默认分支名为master,这个master分支,会在你每次提交之后,指向你的最后一次提交.(git 的"master"其实并不是一个特殊分支,它和其他的分支完全没有区别,之所以每一个库都有一个master分支,是因为git init默认创建它,而且大家都懒得修改)

    	

   - git怎么知道当前在哪一个分支上呢? --> 它有一个名为 HEAD的特殊指针,指向当前所在的"本地分支"
        + 我们可以使用 git log --oneline --decorate 来查看当前分支指向的对象

          - d9fe634 (HEAD -> testing, origin/master, master) o
                
       + 如此可见, testing ,master 都是指向校验和以d9fe63开头的提交对象,当前的HEAD指向testing

	   + 也可以用 git log --oneline --decorate --graph --all 来查看每一个分支的提交状况.


  - git checkout master 切换分支 ,把HEAD指针指向master,master本来指向的是在master分支上面的最后一次提交对象.
  - git分支实质上"仅仅"是包含所指向的对象校验和(长度为40的SHA-1字符串)的文件,所以它的创建和销毁都异常高效.
   + 1. git的分支是一个文件
   + 2. 这个文件的内容是一个指向提交对象的40位字符串
  - 这与其他的版本控制系统形成了鲜明的对比.其他系统创建分支都是直接把当前文件复制到一个特定的目录里面,--> 这样的操作会耗时很长.同时git中每次提交都会记录父对象,所有寻找"恰当的合并基础"(即共同祖先)也是同样的简单和高效.


#### 分支的创建和合并
    git checkout -b testing   --> 新建testing分支并且切换到testing分支,相当于先执行git branch testing,然后checkout testing,所以如果一个已有分支的话,会报错

	- fast-forward.  我们在pull代码或者merge一个新分支的时候,经常会看到这个词.这是因为你pull或者merge的分支上面的commit object 树比之于你当前的master分支属于下游的东西.我们的merge/pull操作实际上将master分支这个(文件,还记得上面说的吗,分支本职上一个文件,文件的内容是指向某一提交对象的40位字符串)文件上的指针指向hotfix分支指向的那个commit object.
	- git branch -d testing --> 删除分支

	- git 分支的合并(注意:pull代码也是一种合并).这里分两种情况,
		1. 一种是上面提到的,你从master分支新建了一个hotfix分支,在hotfix上面修改完了之后,checkout 到master,然后执行 git merge命令,这时候master分支是没有前进的(forward)的,所以这样不会产生冲突,这样只需要master的指针向前指向hotfix分支末端的提交对象就可以了.
		2. 第二种是,你从master分支新建了testing,然后在master做了修改,在testing分支也做了修改.两个分支的末端都向前走了一段,这样你在master 分支执行 git merge testing时候,git就会进行一次"三方合并",这次合并的三方分别是,testing分支创建时候的提交对象,testing分支的末端对象,master的末端提交对象.git会把没有冲突的文件合并,把有冲突的文件添加标识,然后把他们"留出来",放到暂存区,等你解决冲突后,再自定提交.
	- git 冲突
		+ 这个大家都知道的,暂时就不说了 
			- HEAD >>>>>>
			  "our scripts"
			  =========
			  "others scripts"
			  <<<<<<<<
			- HEAD到等号之间的是我们的代码,等号到<<< 之间是其他人->准确的说是另一次commit object的代码,这个修改可能是你做的,比如用 git commit --amend 命令
		+ 解决冲突的时候有很多好的工具,比如webstorm,或者git mergetool,感兴趣的可以查一下
## 高级合并
### 冲突合并
----
- 首先: 在合并冲突前,我们应该保证工作目录是**干净**的
- 你可以用 stash 或者 commit 来把当前目录打扫干净
- 这样不管你接下来做了什么操作,都可以安全恢复.
### 一下命令都在merge过程中执行,也就是执行 git merge test命令之后
* git merge --abort
* 终止并撤销掉刚才的merge操作(前提是你在merge前的工作目录必须是干净的) 

### 撤销合并
- git reset --hard HEAD~  在git merge之后执行此命令,恢复到merge前的状态
- 解读一下这条命令? --hard   HEAD   ~波浪号  -> 需要解读吗??

- 问题:
  + merge操作后不能有新的提交,否则无效
  + 如果你merge之后,push了,别人有pull了代码,并且做了新的修改,那么你的这条操作会带来新的冲突.简直越来越乱


### 还原合并 -> 撤销某次提交的内容
- git revert  HEAD 或者 git revert hash-keys  可以撤销某一次提交,
- 它同时会创建一次新的提交--> 而这条提交的作用就是撤销你的某一次提交

+ git merge testing
+ git diff --base 查看文件在两边都做了何种改动
+ git diff --theirs 与上面作用相同,但是显示格式不同


### 其他merge选项
- git merge -Xours testing    遇到冲突的时候保留我们的操作,忽略testing分支上的修改
- git merge -Xtheirs testing  遇到冲突的时候保留testing上的修改,忽略当前分支上的修改
- 子树合并  -> 这个太麻烦,而且容易出问题,就不说了. 而且它和子模块也没有关系

* 忽略空白
* 有时候我们在合并的时候,有些冲突可能是空白引起的,如果一次合并中有大量的空白问题,可以使用此命令忽略空白修改
* git merge -Xignore-space-change testing(这是分支名)
* 或 git merge -Xignore-all-space testing
* 然而这个我试过了,没啥用,用不用这个参数,都没有把空格包括在内???还是是试错了???
### 使用git调试
* git blame file1.js  查看某个文件每一行的**最后修改时间**以及是**被谁**修改的
* git blame -L 12,16 file1.js   用-L参数来限定12行到16行

* 对于重命名的文件,Git不会显式地记录文件的重命名。 它会记录快照，然后在事后尝试计算出重命名的动作.
* git bisect 用二分法定位哪次提交导致了问题,当然前提是你得知道最后一次正确的代码是哪次提交
* bisect这个没用过,感觉js直接报错,然后blame一下,基本就能找到问题
* 子模块 --> 没用过



#### 分支管理
	- 几个有用的命令
	- git branch -v  显示每一个分支+他们最后的一次提交
	- git branch --merged  显示已经合并的分支,这在我们删除分支的时候很有用
	- git branch --no-merged  显示么有被合并的分支,这些分支你在执行 git branch -d testing 的时候会报err,提示你是用-D参数删除


#### 变基 -> git中整合来自两个不同分支的修改有两个办法:merge 和 rebase
	- merge会把两个分支的最新快照和"最近的"共同祖先快照合并成一个新的快照,提交或者等待你提交.
	- git checkout testing 然后git rebase master ,这就是一次变基操作.但是我不建议用
	   + 这条命令会
			1. 找到master 和 testing分支共同的最近的祖先分支
			2. 然后把testing分支上面相对于公共祖先的提交(commit object)提取出来
			3. 然后将当前分支指向master分支的末端(即最后一次提交),即testing指针前进到master相同的地方
			4. 把第二部提取出来的数次提交依次应用到当前,testing指针依次前进.
        - 这个依次前进的意思就是git会一次将你在testing(当前)分支所做的修改依次生效到对应的文件中,这个过程很可能会爆发冲突,
        - 因为如果你在不同分支上对同一个文件做了多次conflict的修改,git会持续报错,不建议使用.
	- rebase的好处是能够清晰把你在自己分支上的commit记录集中的放在一起,而merge会把你在自己分支中的commit记录和其他人的commit记录混合起来.(慎用)
  
### --squash 参数
- 在merge 或者rebase时使用,可以把你目标分支上的所有commit忽略,只在master分支上保留一次commit记录.

### 一些有用的东西
	- git show sha-a-key  
    - 根据hash值查看某一个commit
	- git show master  
    - 查看master分支是当前指向的提交对象的hash值
	- git reflog  
    - 引用日志,记录了"最近几个月你的 HEAD 和分支引用所指向的历史
		- 每当你的HEAD所指向的位置发生了变化,git会将这次变化存储到这个引用日志中
	- 这个引用日志只存在于本地仓库,一个记录你在自己的仓库做过什么的日志.其他人拷贝的仓库里的引用日志不会和你的相同,而你clone一个新仓库的时候,引用日志是空的

## 重置 
## reset 和 checkout
- git reset --soft
- git reset --hard
- git reset --mixed(不加参数默认)
- ### 先回顾下git的三棵树
- Git 主要的目的是通过操纵这三棵树来以更加连续的状态记录项目的快照。
------
<img src="/git-pics/git-work-flow.png">
- git init 初始化仓库
  <img src="/git-pics/reset-ex1.png">
- git add 来获取工作目录中的内容，并将其复制到索引中。
  <img src="/git-pics/reset-ex2.png">
- git commit，它首先会移除索引中的内容并将它保存为一个永久的快照
- 然后创建一个指向该快照的提交对象，
- 最后更新 master 来指向本次提交。
  <img src="/git-pics/reset-ex3.png">
- 再次修改file.txt文件
  <img src="/git-pics/reset-ex4.png">
- 再次 git add file.txt
  <img src="/git-pics/reset-ex5.png">
- 再次运行 commit
- 发现 HEAD-master 的指向前进了一步,指向了第二次的提交对象
  <img src="/git-pics/reset-ex6.png">
- 我们进行了三次提交后的 提交历史 如下
  <img src="/git-pics/reset-start.png">
- git reset --soft  
- 将会使 master 指向 9e5e64a,同时因为HEAD指针是指向master的,所以相当于HEAD指针也发生了改变
- 它本质上是撤销了上一次 git commit 命令
  <img src="/git-pics/reset-squash-r1.png">
- git reset --mixed
- 它依然会撤销一上次 提交，但还会 取消暂存 所有的东西。 于是，我们回滚到了所有 git add 和 git commit 的命令执行之前。
  <img src="/git-pics/reset-squash-r2.png">
- git reset --hard
- 更新HEAD指向 -> 然后更新暂存区 -> 最后用指定hash值指向的快照"代替"当前工作目录中的文件.
  <img src="/git-pics/reset-squash-r3.png">
- ### 也可以直接通过hash值恢复某一个文件的内容
- git reset eb43bf file.txt

* git rest 先移动HEAD的指向
* git reset HEAD~   这个^表示HEAD现在指向的提交对象的hash-key的父提交对象hash-key  
* git reset 317ae78a6982f2d(某个hash值)把当前HEAD指向的分支(master),把master的指针指向317ae78

* git checkout testing  把HEAD指向 testing,而master分支仍然指向原来的hash-key
* 注意上条,checkout 和 reset 的区别:
  - reset 的HEAD仍然指向master,master的指针位置改变了
  - checkout HEAD指向了另一个分支,原来master的指针没有变


- 储存与清理:  
   - 一种场景,你在当前分支上做了很多修改,但是这个时候你需要切换到另一个分支上去修bug或者加需求,你又懒得去把当前分支的工作去做一次提交,这时候可以使用 git stash 或者git stash save 这两个命令一样效果
    + git stash 会处理工作目录"脏"的状态,只要是被追踪的文件(tracked)的文件,stash会保存文件的修改(modified)和暂存(staged),然后把当前目录指向最近一次的提交
    + git stash list  列出所有的stash记录,最近的一次stash在最上面
    + git stash apply 把stash列表最上面的一次stash 应用, 也可以使用 git stash apply stash@{4} 来恢复你的往前数第四次修改
    + 已经apply 过的 stash不能再次apply;
    + 比如你暂存了 test.ts,然而你忘了自己的暂存,对test.ts进行了新的修改.那么这时候你再去apply stash的时候,会报错

  完了
