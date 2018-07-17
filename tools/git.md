## Git使用教程
#### 写在前面的话
网上关于git的教程数不胜数，这篇教程的内容自然避免不了老生常谈，写这篇文章的目的，一来是为了总结刚学习的内容（毕竟“好记性不如烂笔头”），二来是为了提取出一下常用的便于查阅。
#### 工欲善其事必先利其器
是不是以为我要说如何安装Git？大家应该已经都会了，不会的移步[这里](https://blog.csdn.net/clayja/article/details/61420955)
安装完成后，在命令行输入：
```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
注意 git config 命令的 --global 参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
#### 接下来，重点来了（搬出小板凳ing）
##### 简单使用
+ 首先你需要有一个[GitHub账号](https://github.com/)(没有先去注册哦)
+ 登录你的帐号，创建一个仓库
![image](http://upload-images.jianshu.io/upload_images/11218971-c9d7841555a2cdb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/11218971-0cfc9a956591f7c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
(注意，如果没有选择Initialize this repository with a README，你将得到一个空项目)
我建了一个空项目，成功后可以看到这样的页面
![image](http://upload-images.jianshu.io/upload_images/11218971-aef445ca2a942f19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
+ 在本地创建一个目录resume，执行命令
```
git init
```
将该目录下的文件变为可git管理的 ，接下来我就可以创建文件并推送的远程管理了。

+ 对了差点忘了，git怎么知道要上传到哪去呢，还记得我们在GitHub上创建的仓库吗，我们需要将它与本地关联起来
```
git remote add origin git@github.com:clayJa/resume.git
```
+ 接下来，我们执行一系列命令把它推上去吧
```
git add . 
git commit -am "这是一个备注"
git push origin master
```
好了，完美！等等，好像打脸了。。。
![image](http://upload-images.jianshu.io/upload_images/11218971-f42f1033dbc227ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
莫慌，莫慌！！提示说我们少了RSA key啥的，我们就来生成一个
```
ssh-keygen -t rsa -b 4096 -C "ling20hao@gmail.com"   //注意邮箱填自己的哦
```
找到生成目录的id_rsa.pub文件，复制到GitHub就行了
![image](http://upload-images.jianshu.io/upload_images/11218971-05fbe662e3422758.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
[图片上传失败...(image-b28ee5-1525060986975)]
[图片上传失败...(image-f03b00-1525060986975)]
[图片上传失败...(image-1866c2-1525060986975)]
接下来我们再push一下，bingo！
![image](http://upload-images.jianshu.io/upload_images/11218971-f30345c1e14b1204.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### 团队协作
通过刚刚一系列操作，我们已经熟悉了Git的基本操作，同步本地到GitHub已经没有问题，下面我们来看看团队协作的基本命令（只是基本的，高级点的我现在也不太会哈）。
+ 把团队项目克隆下来
```
git clone git@github.com:clayJa/resume.git
```
+ 拉取项目最新代码（在提交之前，一定要先拉一下哦）
```
git pull
```
+ 分支管理（避免污染master代码）
```
git checkout -b branchName    //git checkout命令加上-b参数表示创建并切换，相当于以下两条命令
git branch branchName   //创建分支
git checkout branchName  //切换分支，切换到master，后面分支名改为master即可
git branch  //查看所有分支
git branch -d branchName   //删除分支
git push origin --delete <branchName>  //删除远程分支
git checkout dev origin/dev  //创建本地分支dev，并且和远程origin/dev分支关联
git branch --set-upstream-to=origin/<远程分支名> <本地分支名>   //建立本地分支和远程分支的关联
git branch -m devel develop  //重命名本地分支
```
+ 在分支开发完成后，我们需要进行合并
``` 
git merge branchName   //git merge命令用于合并指定分支到当前分支。所以执行前需要确认当前在哪个分支
```
当前工作没有完成时，但出现冲突，我们需要把当前代码暂存一下
```
git stash  //然后去修复bug
git stash pop  //修复后执行
git stash list  //可用来查看之前存的工作现场
git stash apply stash@{0} //恢复指定stash
```
#### Last but not least
Github还提供了免费的静态网页展示哦，使用起来也很简单
*  在Github上我们可以给不同的project分别创建相应的repository，对于某一个repository，你可以在其中创建一个小网站，向人们展示你的项目
*   在一个repo的**gh-pages**分支中的所有文件将出现在github.io上。
* 使用刚才我们的分支管理操作，去试试吧

推荐文档：
[Git教程](https://lvwzhen.gitbooks.io/git-tutorial/content/)
[如何使用Github Pages免费搭建网站](https://www.jianshu.com/p/6cabb41495c8)






