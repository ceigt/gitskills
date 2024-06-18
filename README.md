配置git用户名和邮箱：
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

初始化目录，将目录变成git可以管理的仓库：
git init

把文件添加到仓库：
git add readme.md
git add . (所有更新)

把文件提交到仓库，-m后面输入的是本次提交的说明：
git commit -m “this is a comment”

查看当前状态：
git status

查看修改内容：
git diff readme.md

查看提交日志：
git log
git log --pretty=oneline 
git log --pretty=oneline --abbrev-commit
git log --graph --pretty=oneline --abbrev-commit

回退版本：
git reset --hard HEAD^ (回退到上一个版本)
git reset --hard HEAD^^ (回退到上上一个版本)
git reset --hard HEAD~100 （回退到上一百个版本）
git reset --hard xxx  (xxx代表具体的版本号，也就是commit id)

查看git操作历史版本号(commit id)：
git reflog

查看工作区和版本库里面最新版本的区别：
git diff HEAD -- README.MD

丢弃工作区的修改：
git checkout -- README.MD
命令git checkout -- README.MD意思就是，把README.MD文件在工作区的修改全部撤销，这里有两种情况：
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。

把暂存区的修改撤销掉（unstage），重新放回工作区：
git reset HEAD README.MD

删除文件：
git rm test.txt

创建SSH Key：
ssh-keygen -t rsa -C "youremail@example.com"
在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
登陆GitHub，打开“Account settings”，“SSH Keys”页面，然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容

与远程仓库关联，在本地的gitdemo仓库下运行命令：
git remote add origin git@github.com:ceigt/gitdemo.git

删除已有的GitHub远程库：
git remote rm origin

一个本地仓库同时关联两个以上远程仓库：
git remote add github git@github.com:ceigt/gitdemo.git
git remote add gitee git@gitee.com：ceigt/gitdemo.git
如果要推送到GitHub，使用命令：
git push github master
如果要推送到Gitee，使用命令：
git push gitee master


把本地库的所有内容推送到远程库上：
git push -u origin master

把本地master分支的最新修改推送至GitHub：
git push origin master

查看远程库信息：
git remote -v

删除远程库：
git remote rm <name>
此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

从GitHub上克隆一个仓库：
git clone git@github.com:ceigt/gitskills.git

创建dev分支，并且切换到dev分支：
git checkout -b dev
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
git branch dev （创建分支）
git checkout dev （切换分支）
最新版本的Git提供了新的git switch命令来切换分支：创建并切换到新的dev分支，可以使用：
git switch -c dev
仅切换：
git switch dev


查看当前分支：
git branch
git branch命令会列出所有分支，当前分支前面会标一个*号

把dev分支的工作成果合并到master分支上，先git checkout master切换回主分支，然后：
git merge dev
合并dev分支，请注意--no-ff参数，表示禁用Fast forward：
git merge --no-ff -m "merge with no-ff" dev
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

删除dev分支：
git branch -d dev

Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
git stash
查看隐藏的工作现场：
git stash list
恢复工作现场：
一是用git stash apply恢复，但是恢复后，stash内容并不删除，需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了：
可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
 git stash apply stash@{0}

同样的bug，要在dev上修复，我们只需要把4c805e2 fix bug 100这个提交所做的修改“复制”到dev分支。注意：我们只想复制4c805e2 fix bug 100这个提交所做的修改，并不是把整个master分支merge过来。
为了方便操作，Git专门提供了一个cherry-pick命令，让我们能复制一个特定的提交到当前分支：git cherry-pick 4c805e2

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场；
在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

开发一个新feature，最好新建一个分支；
如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

在本地创建和远程分支对应的分支，使用git checkout -b local-branch-name origin/remote-branch-name，本地和远程分支的名称最好一致；

多人协作的工作模式通常是这样：
    首先，可以试图用git push origin <branch-name>推送自己的修改；
    如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
    如果合并有冲突，则解决冲突，并在本地提交；
    没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to=origin/<remote-branch> <local-branch>。



git rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

创建标签：
git tag <name>  e.g: git tag v1.0   git tag <name> <commit id> 
查看所有标签：
git tag
查看标签详细信息：
git show <tagname>
还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
删除本地标签：
git tag -d v1.0
推送某个标签到远程:
git push origin v1.0
一次性推送全部尚未推送到远程的本地标签：
git push origin --tags
如果标签已经推送到远程，要删除远程标签，先从本地删除git tag -d v1.0，然后再从远程删除：
git push origin :refs/tags/v1.0

在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。
有些时候，添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了，可以用-f强制添加到Git：
git add -f <filename>
.gitignore写得有问题，需要找出来到底哪个规则写错了:
git check-ignore -v <filename>
把指定文件排除在.gitignore规则外的写法就是!+文件名，只需把例外文件添加进去即可。
https://github.com/github/gitignore  gitignore模板

给命令配置别名，如：
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch

配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
每个仓库的Git配置文件都放在.git/config文件中，全局配置文件放在用户主目录下的一个隐藏文件.gitconfig中。

搭建git服务器：
1.sudo apt install git
2.sudo adduser git
3.收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个
4.选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
sudo git init --bare sample.git
把owner改为git：sudo chown -R git:git sample.git
5.出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：
git:x:1001:1001:,,,:/home/git:/bin/bash
改为：
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
要方便管理公钥，用[Gitosis](https://github.com/res0nat0r/gitosis)；
要像SVN那样控制权限，用[Gitolite](https://github.com/sitaramc/gitolite)。

git的图形界面工具推荐https://www.sourcetreeapp.com/




















