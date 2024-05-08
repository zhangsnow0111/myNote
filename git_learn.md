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

7. 查看远程仓库信息
	`git remote -v`
	i.显示远程仓库的名称和对应的 URL。输出将包括远程仓库的名称（通常是 "origin"）以及远程仓库的 URL


# 上传github（其实是上面的融合）

1. 在 GitHub 上创建一个新的仓库。
在 GitHub 网站上登录并点击页面右上角的 "+" 图标，然后选择 "New repository" 创建一个新的仓库。给仓库起一个适当的名称，并选择其他相关配置选项，例如是否设为私有仓库、是否添加 README 文件等。

2. 在本地机器上初始化一个 Git 仓库。
打开命令行终端，并导航到包含您的代码的文件夹或项目根目录。执行以下命令来初始化 Git 仓库：
```git init```

3. 将代码添加到 Git 仓库。
执行以下命令将所有文件添加到 Git 的暂存区：
```git add .```
如果只想添加特定文件，可以将 "." 替换为文件名。

4. 提交代码到本地仓库。
执行以下命令提交代码并添加一条提交消息：
```git commit -m "Initial commit"```
在引号中的 "Initial commit" 部分，可以根据需要自定义提交消息。
可能需要配置邮箱和用户名
```
git config --global user.email "642716972@qq.com"
git config --global user.name "zhangsnow0111"
```
5. 关联本地仓库与 GitHub 远程仓库。
执行以下命令，将您在 GitHub 上创建的仓库 URL 替换为 <repository-url>，注意有的时候网络不好，我们需要选择第二个连接也就是ssh连接进行绑定和推送，使用此链接需要联合下面的密钥创建使用：
```git remote add origin <repository-url>```

6. 将代码推送到 GitHub 远程仓库。
执行以下命令将本地仓库的代码推送到 GitHub：
```git push -u origin master```
这会将代码推送到名为 origin 的远程仓库的 master 分支上。

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

## 添加ssh密钥
1. 检查 SSH 密钥：
首先，确保你的计算机上存在 SSH 密钥。你可以使用以下命令检查是否存在 SSH 密钥：
```shell
ls ~/.ssh
```
如果没有任何文件或目录显示，表示你没有 SSH 密钥。在这种情况下，你需要生成一个新的 SSH 密钥对。

2. 生成 SSH 密钥（如果需要）：
如果你没有 SSH 密钥，可以使用以下命令生成一个新的 SSH 密钥：
```shell
ssh-keygen -t rsa -b 4096 -C "你的电子邮件地址"
ssh-keygen -t rsa -b 4096 -C "642716972@qq.com"
```
在命令中，将 "你的电子邮件地址" 替换为你的 GitHub 帐户关联的电子邮件地址。按照提示设置密钥保存位置和密码（一般全部enter）。这将生成一个新的 SSH 密钥对。

3. 添加 SSH 密钥到 GitHub：
将生成的 SSH 公钥添加到你的 GitHub 帐户中，以便进行身份验证。你可以使用以下命令获取 SSH 公钥的内容：
```shell
cat ~/.ssh/id_rsa.pub
```
复制输出中的内容，然后前往你的 GitHub 帐户的设置页面，找到 "SSH and GPG keys"（SSH 和 GPG 密钥）部分，并添加一个新的 SSH 密钥。将复制的 SSH 公钥粘贴到 "Key"（密钥）字段中，然后保存。
4.验证 SSH 连接：
确保你的计算机可以成功与 GitHub 建立 SSH 连接。你可以使用以下命令进行验证：
```shell
ssh -T git@github.com
```
如果一切正常，你将看到一条欢迎消息，表示 SSH 连接成功。
现在，你应该能够使用 SSH 协议访问 GitHub 上的仓库。请确保你有正确的访问权限，并且仓库确实存在。

> **-t rsa -b 4096 是什么意思**
在生成 SSH 密钥时，-t rsa -b 4096 是用于指定密钥类型和密钥长度的选项。
-t rsa 表示要生成 RSA 密钥。RSA 是一种非对称加密算法，用于生成公钥和私钥对。
-b 4096 表示要生成的密钥长度为 4096 位。密钥长度越长，安全性越高，但生成和使用密钥的过程也会更耗时。
综合起来，-t rsa -b 4096 意味着你正在生成一个使用 RSA 算法，长度为 4096 位的 SSH 密钥对。这样的密钥对通常提供较高的安全性，尤其适用于安全敏感的环境和需要更高级别身份验证的情况。

## 可能出现的问题
```
git push
error: update_ref failed for ref 'refs/remotes/origin/main': cannot lock ref 'refs/remotes/origin/main': Unable to create '/home/sinet/P4/tutorials/exercises/congAvoid2-8h8s/.git/refs/remotes/origin/main.lock': Permission denied
Everything up-to-date
```
这个错误表示在执行 git push 命令时，出现了更新引用的错误。具体错误信息是无法锁定引用 refs/remotes/origin/main，因为无法创建 .git/refs/remotes/origin/main.lock 文件，权限被拒绝。

这通常是由于权限设置问题导致的。请确保你对该仓库的本地副本具有读写权限。你可以尝试以下解决方法：

检查文件和文件夹权限：确保你的用户帐户对 .git 文件夹及其子文件夹具有适当的权限。你可以使用 ls -la 命令检查文件和文件夹的权限，并使用 chmod 命令更改权限。
```
ls -la /home/sinet/P4/tutorials/exercises/congAvoid2-8h8s/.git
chmod -R 777 /home/sinet/P4/tutorials/exercises/congAvoid2-8h8s/.git
```

