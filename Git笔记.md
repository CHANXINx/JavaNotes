# SVN和GIT的区别：
## SVN：
- SVN(Subversion), 集中式管理控制。代码托管在中央服务器，开发者需要从中央服务器获取和提交代码。
## Git：
- Git，分布式管理控制。代码在本地仓库和中央仓库都存在，用户在本地仓库操作后再添加、提交并推送到中央仓库，并可以在操作前拉取中央仓库的更新。
## 区别：
### 速度和性能：
- SVN：代码库和版本数据存放在中央服务器上，开发者每次操作都需要连接到中央服务器，从服务器获取和提交代码，对网络的要求较高！
- Git：开发者本地仓库有代码库的完整副本，包含完整的历史记录和版本信息，可以在本地完成大部分操作，无需每次操作都连接到中央服务器，不强依赖于网络。
### 数据安全性：
- SVN：代码库存放在中央服务器，这样既是好处、又是坏处：好处在于如果开发者不持有代码库的完整副本，无需担心代码库被窃取。坏处在于如果中央服务器崩溃，可能导致代码库/数据丢失或损坏！
- Git：开发者持有完整的代码库，可能会造成被窃取数据/代码库！但是无需担心中央服务器出问题。

# Git/Linux常用命令

| command    | action            |
| ---------- | ----------------- |
| `cd xx`    | 改变目录              |
| `cd ..`    | 回退到上一级目录          |
| `pwd`      | 显示当前所在的目录路径       |
| `ls(ll)`   | 列出当前目录下的所有文件及文件夹  |
| `touch xx` | 新建xx文件            |
| `mkdir xx` | 新建xx文件夹           |
| `rm`       | 删除文件，不可用于删除文件夹    |
| `rm -r`    | 删除文件夹（及内部所有文件）    |
| `mv a b`   | 将文件（文件夹）a移动到文件夹b中 |
| `clear`    | 清屏                |
| `history`  | 查看命令历史            |
| `exit`     | 退出                |
## Git相关配置
- `git config --system --list`：查看系统配置信息
- `git config --global --list`：查看当前用户配置
- 系统配置信息存放于`F:/Git/etc/gitconfig`中，全局配置信息存放于`C:/User/Adminstrator/.gitgonfig`中。
  ![[GitNote_gitconfig.png]]
# Git基本理论:
## 工作分区：
1. **Working Directory：** 存放本地代码库；
2. **Stage：** 暂存区，临时存放改动，保存即将commit的文件列表信息；
3. **Local Repository：** 仓库区，用于安全存放数据的地方，存放提交的版本信息；
4. **Remote Repository：** 远程仓库，托管代码的服务器。
## 工作流程：
1. 本地添加、修改文件；
2. 使用`git add ..`将改动添加到暂存区；
3. 使用`git commit -m ~`将改动commit到`Local Repository`。其中`-m ~`为commit时的备注。
4. 使用`git push` 将commit后的改动push到远程仓库。
# Git项目搭建： 
**搭建方法1：在Github上创建仓库，然后clone到本地。**
1. 在想保存本地仓库的地方运行`git clone ~`，其中~为github项目地址。
**搭建方法2：本地仓库先创建，然后与Github上的仓库连接**
	需要先添加SSH密钥，否则无法使用此方法。
1. 选择作为仓库的文件夹，允许`git init -b ~`命令使其成为git仓库，其中`-b ~`为指定主分支的名称为`~`。此时文件夹内会多出一个隐藏的`.git`文件夹。
2. 使用`git remote add origin URL`，将本地仓库与URL地址的仓库进行链接。
# Git文件操作：
## Git文件状态：
1. **Untracked：** 未跟踪状态。此时文件虽在git本地库中，但其不参与版本控制。通过`git add <filename>`将状态变为`Staged`。
2. **Unmodify：** 未修改状态。此时文件在git本地库中，参与版本控制，但未被修改。文件改动后，则变为`Modified`；或可以使用`git rm --cached <filename>`  移出版本库，变为`Untracked`。
3. **Modified：** 已修改状态。此时文件在版本库中，并且已被改动。通过`git add <filename>` 可变为`Staged`状态；使用`git checkout`则取回远程仓库中的文件，覆盖当前改动！
4. **Staged：** 暂存状态。此时文件改动已`git add` 添加到暂存区。通过commit可将改动提交到本地库中；执行`git reset HEAD filename`则取消暂存，变回Modified状态。
5. **.gitignore：** 主目录下文件，用于存放不想纳入版本控制的文件名称。
	**基本规则：** 每行指定1个规则；空行忽略；#~为注释。
	**通配符解释：** `*(代表0~n个字符)`，`?(代表单个字符)`，`**(匹配0~n个目录)`
	1. `*.log`：忽略以.log结尾的文件；
	2. `file?.txt`：忽略`file1.txt`, `file2.txt`等文件；
	3. `/node_modules/`：忽略根目录下`node_modules`目录及其所有内容；
	4. `logs/*.log`：忽略所有logs文件夹内以`.log`结尾的文件(但不忽略logs文件夹)
	5. `/logs/*.log`：忽略根目录下logs文件夹里的以`.log`结尾的文件；
	6. `!~`：忽略某个文件/目录；
	7. `/temp`： 忽略根目录下的temp目录及文件，不忽略子目录下的；
	8. `temp/`：忽略所有名为temp的目录及其所有内容，不忽略temp文件。
	9. `/temp/`： 忽略根目录下的temp目录及其内容，子目录下的不忽略。
	10. `temp`：忽略所有名为temp的目录及内容
	   /在前：表示仅从根目录检索，/在后，表示忽略的是文件夹及其内容。
# Git分支：
- Git分支为同一仓库的不同子仓库，互不干扰。
- 常用分支名称：
	- main/master：主分支，保存较稳定版本。
	- dev：开发者分支。
	- vX.X：
## 常用命令：

| 常用命令                                       | 作用                             |
| ------------------------------------------ | ------------------------------ |
| `git branch`                               | 查看所有本地分支                       |
| `git branch -r`                            | 查看所有远程分支                       |
| `git branch [branch-name]`                 | 新建branch-name分支但不切换            |
| `git checkout -b [branch-name]`            | 新建branch-name分支并切换到新分支         |
| `git checkout [branch-name]`               | 切换到branch-name分支               |
| `git checkout -`                           | 切换到上一个分支                       |
| `git merge [branch]`                       | 将branch分支与当前分支合并               |
| `git branch -d [branch-name]`              | 删除branch-name分支                |
| `git push [origin] --delete [branch-name]` | 删除远程origin仓库中的branch-name分支    |
| `git branch -dr [remote/branch]`           | 删除本地仓库与远程仓库remote中的branch分支的连接 |
| 最后两条分支配合使用，即删除远程仓库中的分支，并断开本地仓库与该无用分支的连接。   |                                |
