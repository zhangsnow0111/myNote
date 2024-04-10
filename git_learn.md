# 一些操作
git #查看版本



# 基本概念
1. 工作区：即版本库所在的文件夹除了.git以外的，我们进行编辑的一般文件
2. 暂存区（ stage , index）：在.git中，暂存修改
3. 版本库（ repository, \[rɪˈpɑːzətɔːri\]）：在.git中，存放版本
4. 暂存区的意义：暂存区是一个特殊的版本，提供临时性的记录功能
	1. 通常情况下，我们写一次代码需要修改多个文件，每个文件修改多次。
	2. 如果把每次修改后的结果都放到版本库中作为一个版本进行长期记录，版本库就会混乱不堪；而且改过去又改回来的情况也被记录了
	3. 如果把所有想修改的文件改好之后在提交，我们就不知道我们每次改了什么
	4. 暂存区就提供了一种临时记录的功能，记录修改方便我们多次修改多个文件；攒够一波再放到版本库中
5. 当前：git用HEAD标记一个版本和一个分支，称为当前版本和当前分支
	1. `HEAD` 当需要填入版本号时，可以填入HEAD
6. 跟踪：
	1. 如果一个文件当前被暂存，或者被commit过，我们就可以用git管理这个文件修改情况，这个文件被跟踪了
	2. 一般不用git跟踪非文本文件
	3. git跟踪的是修改
		1. 增加文件
		2. 删除文件
		3. 修改文件，即当前状态相对head中的状态：
			1. 增加了哪些行
			2. 减少了哪些行

# 本地操作
## 1. 分支内操作
针对当前分支
1. 创建git版本库
	 `git init`
	1. 会创建一个默认分支master作为当前分支(branch）
2. 暂存文件
	`git add <filename>`
3. 提交文件
	`git commit -m "message"`
4. 取消跟踪文件
	`git rm --cached <filename>`
	1. 备注：如果一个文件已经被跟踪，把它添加到.gitignore中是没用的。
5. 查看当前分支的情况
	`git status`
	1. 上游远程仓库
	2. 哪些文件未被跟踪
	3. 哪些已跟踪的文件有未暂存的修改
	4. 具体查看修改`git diff <filename>`
6. 把工作区置为某个版本
	`git reset --hard <版本号>`
7. 取消暂存区的修改
	`git reset HEAD <文件名>`
8. 取消工作区的修改
	`git checkout -- <文件名>`
	1. 如果当前有未提交的暂存，则置为暂存区的版本
	2. 如果没有，则置为HEAD的版本
1. .gitignore
	1. 指定一些文件，git会将他们忽略，而不是尝试跟踪他们
	2. 使用`*`匹配任意文件或文件夹名，例如`/*.pcap`忽略根目录下所有.pacp文件
		1. 开头的`/`可以忽略
	3. 使用**/匹配任意级目录，例如 `**/*.pcap`忽略所有目录下的.pcap文件
	4. 如果一个文件已经被跟踪，则把它添加到.gitignore不会起作用
## 2. 分支管理
1. 查看分支
	`git branch`
2. 分支改名
	`git branch -m`
3. 创建分支
	`git branch <branch name>`
	1. `git branch -d <branch name>`删除分支
4. 切换分支
	`git switch <branch name>`
	1. `git switch -c <branch name>` 如果不存在则创建后再切换
5. 合并分支
	`git merge <branch name>`
	把相应分支的最新版本commit到当前分支
6. 删除分支
	

# 远程操作
1. 在github中新建一个版本库，获得url（xxx.git）
2. 把ssh公钥添加到github中
3. 为本地的版本库添加一个远程库
	`git remote add <remote name> <url>`
	1. 本质是给url起一个名字，用于在本地版本库中使用的，比如之后用到这个url时可以直接写这个名字
	2. 删除远程库，或者说弃用名字
	`git remote rm <remote name>`
4. 第一次上传
	`git push -u <remote name> <remote branch>`
	1. 把当前分支和远程库的一个分支绑定，这个远程分支被称为当前分支的上游(upstream)
5. 之后的上传
	`git push`
	1. 即把当前分支上传到对应上游
6. 克隆远程库
	`git clone <url>`
	1. github提供多种协议的url，但ssh通常更快
	`git clone --depth 1 <url>` 只克隆工作区

> [!seealso]
> [[开发工具|开发工具]]  
> #常用