# 本地
第一步肯定是**初始化**啦,没有什么好讲的
```bash
git init
```
然后开始配置你的***用户名***和***邮箱***
```bash
git config --global user.name <name>
git config --global user.email <email>
```
添加暂存区
```bash
git add . // 添加目录下的所有文件
```
提交到本地仓库
```bash
git commit -m <messages>
```
查看提交信息
```bash
git log
```

## 回退版本
有时候发现自己的代码越改越乱怎么办,这个时候就可以采取版本回退
```bash
git log // 看一下提交ID
git reset --hard <ID> //--hard 表示硬重置 

```
- -`hard` : ***撤销工作区中所有未提交***的修改内容，将***暂存区与工作区***都回到上一次版本，并删除之前的所有信息提交
- -`soft` :用于回退到某个版本
- -`mix`:用于***重置暂存区的文件***与上一次的提交(commit)保持一致，**工作区文件内容保持不变**
- `HEAD`命令说明:
	- HEAD 表示当前版本
	
	- HEAD^ 上一个版本
	    
	- HEAD^^ 上上一个版本
	    
	- HEAD^^^ 上上上一个版本

# 分支
上面讲的版本回退确实好用,但是不是把我们暂存区删了(mix),就是把我们工作区删了(hard),那如果我想在版本之间切换,就可以考虑***分支***功能!
## 创建分支
创建0.2分支
```bash
git branch 0.2
```
>[!NOTE]
>当然记得要保存
**通过`git branch`也可以做到查看分支**

切换分支
```bash
git checkout <branch>
```
## 合并分支
```bash
git merge <branchname>
```
>[!CAUTION]
>开始尝试前记得备份一下,除非你不想要你的文件,但是这个功能太重要了
> 
>至于这个有什么用,你想想,如果五个人开发一个项目,还有依次复制,那该多麻烦

形象的比喻: **branch(复制) == 复制 ;; merge(合并) == 粘贴**

## 删除分支

删除本地分支：
```bash
git branch -d <branchname>
```
强制删除未合并的分支：
```bash
git branch -D <branchname>
```
删除远程分支：
```bash
git push origin --delete <branchname>
```
# 上传
其实可以把这些仓库看作一个符合git操作的网盘,常见的有:
- github
- gitee
- gitlab
## ssh
### 创建ssh密钥
**生产ssh密钥**
```bash
cd ~/.ssh
ssh-keygen -t rsa -b 4096
```
**(如果生成过)**生成文件
```bash
输入新的文件名
```
生成的test(密钥)千万保留
生成的test.pub(公钥)复制到`**github`用户**的**ssh设置**里面→**添加公钥**
> 初次创建ssh密钥,到这里就完成了
> (不是初次)进一步配置
```bash
//在.ssh创建config文件,将下列代码添加
# github
Host github.com
HostName github.com
PreferrenAuthentications publickey
IdentifyFile ~/.ssh/test //密钥路径
```
作用:当我问访问 `[github.com](<http://github.com>)` 时指定使用**test密钥**

## 上传仓库
**添加远程仓库链接**
```bash
git remote add origin <url>
```
**推送(上传文件)**
```bash
git push -u origin main
```
**拉取(下载)**
```bash
git pull <url>
```
>之前看视频.建议使用`git pull -rebase`
**查看远程仓库**
```bash
git remote
```
![](https://murmur-1329567155.cos.ap-guangzhou.myqcloud.com/picture/20250324130002356.png)
>上图很显著的理清楚了,Fetch(取),Merge(合并),PULL(拉取的)区别
# 协作
首先fork(复制)别人的仓库
然后拉取源文件 `git clone <url>`
这个时候看一下自己仓库 `git remote`,会发现只有自己的远程仓库,那如何提交代码到别人的仓库呢?
```bash
git remote add upstream <url>
```
就可以连接到别人的仓库了
>**第一次push**记得加上源文件和分支,即`git push -u origin <branch>`


## Pull request

# 详细版