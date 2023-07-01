###### 配置文件
    git config uesr.name "name"
    git config user.email "email"
    --global 该机器所有仓库都使用 此次配置
    --unset 删除对应配置，注意：配置时如果使用了--global，删除时也需要该选项
###### 提交文件至暂存区
    git add 
    .
    filename
###### 提交至版本库
    git commit -m "描述"
###### 查看提交日志 与 HEAD

    git log 查看提交日志
        [qlz@tianen1573 GIT]$ git log --pretty=oneline
        20b33292484776d78de2225e52c0c32fb4d64de5 modify git.txt
        37e86ee30b34fa3b89d98e5ea3dcf3cd42cf16d1 add file5
        3fb324911f1785736edf9a5d38cfd5fcd842786f add file4
        2578891270bbe96964a94b2686274dd9603df1fa add 3 files
        327afd031a5dacf6c2e0bda42a02924b8ed73f52 git.txt
        这些都是commit id
    HEAD就是我们默认指向的master分支的指针
    cat .git/HEAD
        ref: refs/heads/master
    cat .git/refs/heads/master
        20b33292484776d78de2225e52c0c32fb4d64de5 //master最新版本
###### objects对象库
    [qlz@tianen1573 GIT]$ ls .git/objects
    20  25  31  32  35  37  39  3f  51  6b  70  8d  b0  bd  d0  d1  e6  info  pack
    和tree与log作比较会发现commitid前两位是文件夹名称，后38位是文件名称

    git cat-file -p commitid 可以查看该文件内容
        [qlz@tianen1573 GIT]$ git cat-file -p 20b33292484776d78de2225e52c0c32fb4d64de5
        tree 391e01f7de7a86cb02bbe8bb47e2e2402a90217f
        parent 37e86ee30b34fa3b89d98e5ea3dcf3cd42cf16d1
        author qlz <415258758@qq.com> 1688039631 +0800
        committer qlz <415258758@qq.com> 1688039631 +0800
        
        modify git.txt
        //这就是我们最近一次提交
        //继续打印，可以发现，git还会保存对文件的修改
        [qlz@tianen1573 GIT]$ git cat-file -p 391e01f7de7a86cb02bbe8bb47e2e2402a90217f
        040000 tree 31881726d51abec1a09b8ab82606e805df49b230    lesson1
        [qlz@tianen1573 GIT]$ git cat-file -p 31881726d51abec1a09b8ab82606e805df49b230
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    file1
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    file2
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    file3
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    file4
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    file5
        100644 blob bd6bb09f40ad27a3bbdf55592438e4b154c59b92    git.txt
        [qlz@tianen1573 GIT]$ git cat-file -p bd6bb09f40ad27a3bbdf55592438e4b154c59b92
        hello git!
        hello git!
###### 查看仓库状态
    git status //可以查看文件在commit之后是否有变化
###### 查看文件改动
    git diff filename 查看工作区和暂存区里文件的改动
        [qlz@tianen1573 lesson1]$ git diff git.txt
        diff --git a/lesson1/git.txt b/lesson1/git.txt  a代表改之前，b代表改之后
        index d1c6469..bd6bb09 100644
        --- a/lesson1/git.txt
        +++ b/lesson1/git.txt
        @@ -1 +1,2 @@
        hello git!
        +hello git! +为新增，-删除
    git diff HEAD filename 查看暂存区和版本仓库的文件改动
###### 版本回退
    git reset [--soft | --mixed | --hard] [HEAD]
    --mixed 为默认选项，使用时可以不用带该参数。该参数将暂存区和版本库内退回为指定提交版本内容，工作区文件保持不变，版本库之间的差异和暂存区的内容都放到工作区。
    --soft 参数对于工作区和暂存区的内容不变，只是将版本库回退到某个指定版本，两个版本库的差异内容会放到暂存区保存。
    --hard 参数将版本库，工作区和暂存区都退回到指定版本。切记工作区有未提交的代码时不要用这个命令，因为工作区会回滚，你没有提交的代码就再也找不回了，所以使用该参数前一定要慎重。
    //https://zhuanlan.zhihu.com/p/487579089
    HEAD 说明：
        ◦可直接写成commitid，表示指定退回的版本
        ◦HEAD表示当前版本
        ◦HEAD^上一个版本
        ◦HEAD^^上上一个版本
        ◦以此类推...
        可以使用〜数字表示：
        ◦HEAD~0表示当前版本
        ◦HEAD~1上一个版本
        ◦HEAD^2上上一个版本
        ◦以此类推...
###### 查看本地的提交命令的commitid
    git reflog 不受版本约束，只关联本地
###### 撤销修改/回退
    1. 未add，撤销工作区
        git checkout -- filename 让工作区的文件回到最近一次add/commit状态 //--很重要，是选项
    2. 已add，未commit，撤销暂存区
        git reset HEAD filename //省略了--mixed 回退到上个版本，并将暂存区的内容和版本差异放到工作区，清空暂存区
        git checkout -- filename//将工作区回到最近一次
    3。 add + commit，但没有push
        git reset --hard HEAD^ //回退到上个版本，版本库+暂存区+工作区全回退
###### 文件删除
    git rm filename //从暂存区和工作区删除文件
    git commit -m "描述" //提交至版本库
###### GIT分支管理
    git branch //查看本地仓库的分支

    HEAD指向的分支，就是当前工作的分支，不是必须指向主分支

    git branch 分支名字 //创建新分支
    git checkout 分支 //将HEAD切换到对应分支，即切换当前工作分支
    git checkout -b 分支b //创建分支b，且切换到分支b

    git push 远程仓库 本地分支 //将本地分支推送到远程仓库 并建立两者连接

    git merge 分支b //将一个分支b合并到当前分支a
    //合并冲突时,需要手动选择保存哪些冲突内容

    git branch -d 分支b //在当前分支下， 删除另一个分支b, 分支b是已经被merge过的分支
    git branch -D 分支b //强制删除分支b

###### 分支合并模式
    Fast-forward //查看log时， 看不出来时是commit提交还是merge提交
    no-ff // 可以看出来是commit还是merge
    git merge --no-ff -m "描述此次合并信息" 分支b //代表当前分支合并分支b时，不使用ff模式， 但需要提交描述信息

###### 分支策略
    主分支和其他分支在稳定上的区别

###### 分支管理——bug分支
    git stash 会保存被追踪的文件的变动至当前分区的工作区，即使没add，其他分支也看不到
	git stash pop 将存储的更改放出来
	git stash list 将存储的更改展示出来

    dev分支 与 和bug分支合并后的master主分支，存在冲突
    我们让dev合并master，在本地分支进行修改测试解决冲突，保证master的稳定
    再让master合并dev分支，此时不会在存在大漏洞

###### 远程操作--理解分布式版本控制

###### 克隆远程仓库
    https ： 直接克隆
    ssh ： 需要配置本地公钥

###### 推送push
    git push 远程仓库 本地分支:远程分支
###### 拉取pull
    git pull 远程仓库 远程分支:本地分支 //拉取并合并某分支, 拉取分支内的内容
    git pull //拉取整个远程仓库

###### 忽略特殊文件
    .gitignore
    // gitignore 文件里可以选择 忽略某些类型的文件，这些文件不会被追踪
    // 写在文件里的代表是被忽略的， 带！是不忽略的
    git add -f 文件 //-f选项可以强制添加被忽略的文件
###### 配置命令别名
    git config [--global] alias.st status //给status起别名为st

###### 标签管理
    git tag v1.0 //默认给最新的一次提交commit打标签
    git tag v1.0 commitid //指定提交打标签

    git push 远程仓库 v1.0 //将标签v1.0推送到远程仓库
    git push 远程仓库 --tags //将所有标签推送到远程仓库

    git tag -d v1.0 //删除标签v1.0
    git push 远程仓库 :v1.0 // 把删除标签v1.0推送到远程仓库

###### 多人协作
    git branch -r //可查看远程分支
    git branch -a //所有分支
    git branch -vv //查看本地分支和远程分支所建立的连接
    git branch --set-upstream-to=远程仓库/远程分支 //将本地分支与远程分支 建立连接
    git checkout -b [本地分支][远程仓库/远程分支]

    1. 本地merge
    2. PR申请