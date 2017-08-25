 



 
 
 
###Git简介，大家可以上网看看**B** and     `Code` text

###从头创建一个仓库
####可以先在github上创建一个仓库，地址为http://...../xxx.git
1. 创建目录 c:/abc
2.切换到abc目录下 cd c:/abc
3. 打开cmd

###开始在电脑中初始化git ,增加一些代码

git init     
git remote add origin https://github.com/linxuhong/showme.github.io.git
git add .
git commit -m "提交代码"
git push   origin master
git push origin  dev_lxh

+解释上术代码
 + `git init` 将用户所有的目录变成一个git仓库，其实就是在abc目录下建立 .get隐藏文件
 + `git remote add origin` 将git hub的一个远程仓库命名为origin，以后本地就可以用origin来指向远程的仓库 
 + `git add .`   将所有的文件，新增，修改的内容添加暂存区
 + `git commit -m "注释：提交代码"`   将add的文件提交到本地仓库中
 + `git push   origin master    推送到github上，github默认有一个master分支
 + `git push   origin dev_lxh    推送到github上，的dev_lxh分支 ，因为github上没有，他会自动创建一个dev_lxh的分支
 
 
##### 

git checkout -b develop origin/dev_lxh
git push origin --delete dev_lxh
  
###
git fetch --all


git reset --hard origin/dev_lxh

git reset --hard HEAD
###################### 



### 
git diff HEAD -- readme.txt 
######只修改了#######
git checkout -- readme.txt
######################
git rm test.txt 
####### 不小心add ##############
git reset HEAD  file

#########################################
git diff 比较的是工作区和暂存区的差别
git diff --cached 比较的是暂存区和版本库的差别
git diff HEAD 可以查看工作区和版本库的差别
#######################################
############# 【git 删除本地分支】
git branch -D br
############# 【git 从远程分支】
git checkout -b develop origin/develop

########重命名远程分支
git push --delete origin develop
git branch -m develop develop_old



Git global setup

git config --global user.name "linxuhong"
git config --global user.email "linxuhong@jd.com"
 
 

  

