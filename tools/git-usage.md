## Git usage

### 1. Install

#### 1.1 Set enviromment.
* [Git](https://git-scm.com/downloads) 
* [TortoiseGit](https://tortoisegit.org/download/) GUI tools.
#### 1.2 一路默认点击安装（验证安装是否成功）

#### 1.2 Config local
```cmd
$git config --global user.name "Stephen Huang"
$git config --global user.email stephen_huang_@hotmail.com

$git config --list // view all config
$git config --global --edit // Use default editor(vim) edit .gitconfig file

// change set notepad++ as default editor.
$git config --global core.editor "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -nosession"

```

#### 1.3 初始化本地仓库
* `$git init`初始化本地仓库。使用gitCMD进入到需要被git版本控制的目录。
* `$git remote add origin git@github.com:yourdom/yourproject.git` //Related your remote code repor
* `$git add --all`将目录下的文件全部添加到本地代码仓库。
* `$git commit -m "init project code"`提交代码到本地仓库
#### 1.4 创建SSH（如果本地用户目录.ssh文件夹下已经生成，请忽略）
`$ ssh-keygen -t rsa -C "youremail@example.com"`
#### 1.5 关联本地和远程仓库
* 将.ssh 文件夹下的pub key添加到gitHub SSH key是中。
#### 1.6 创建远程仓库。获取项目SSH路径。
#### 1.7 关联本地和远程仓库
* `$ git remote add origin git@github.com:yourdom/yourproject.git`
#### 1.8 提交本地仓库代码到远程
* `$ git push -u origin master`
#### 1.9 查看日志
* `$git log` cmd style
* `$gitk` 图形化界面
### 2. Command usage
```cmd
$git rebase //

$git branch -a //view local and remote all branch
```

#### 2.1 
```cmd
$git commit --amend //change the last commit message
$git reflog  //view recently operation record
$git reset --soft HEAD{1} // back to HEAD{1} and not delete change
$git reset --hard HEAD{1} // back to HEAD{1} and delete change
```

### 参考
* [Git教程 - 廖雪峰](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
