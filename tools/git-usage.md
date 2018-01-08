## Git usage

### 1. Install

#### 1.1 Download [Git Download](https://git-scm.com/downloads)
#### 1.2 一路默认点击安装（验证安装是否成功）
#### 1.3 初始化本地仓库
* `$git init`初始化本地仓库。使用gitCMD进入到需要被git版本控制的目录。
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

### 2. Command usage

### 参考
* [Git教程 - 廖雪峰](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
