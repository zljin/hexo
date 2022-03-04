---
title: Git
date: 2021-03-07 12:00:45
tags: 技术
categories: 工具
---

> https://learngitbranching.js.org/?locale=zh_CN
> Git是一个分布式版本控制工具,Github是通过Git进行版本控制的软件源代码托管服务平台
> git help [command] 查看命令如何使用
![image](https://cdn.jsdelivr.net/gh/CL0610/codercc-cdn@1.8/img/git/git%E6%93%8D%E4%BD%9C%E9%80%9A%E7%94%A8%E6%B5%81%E7%A8%8B.png)

### git命令行实操总结

#### 如何在github上创建仓库,下载本地到上传
> 可理解为让电脑与你的Github远程仓库建立联系，通过本地生成的公钥配置到远程仓库中，然后就有提交的权限了公钥和私钥的理解：把公钥理解为钥匙,可以打多条钥匙,私钥就是锁孔,有且只有一把

```
1. 在github上配置ssh
    1. 首先将本机的ssh keys(公钥)生成：$ ssh-keygen -t rsa -C '464480515@qq.com'
    2. 将文件内容C:\Users\lenovo\.ssh\id_rsa.pub 复制到github的setting的ssh配置选项,
    添加一行
    3. 添加后进行命令行测试ssh -t git@github.com,
    首次建立连接要求信任主机,输入命令后会自动配置到本地known_hosts.


2. 下载远程仓库的项目，修改并提交
git clone git@github.com:zljin/learnGit.git

git pull origin master
git add .
git commit -m "commit" 
git push origin master -f (默认提交master分支,其他分支请指定)

3. 如果是本地仓库开始为和远程仓库进行连接
# 进入本地仓库
cd /Deskstop/vue-shop
# 若无初始化,则需初始化
git init
-- 添加新的远程仓库 <remoteName>+<url>
git remote add origin git@gitee.com:zou0jin/vue-shop.git
--查看所有远程仓库
-- git remote -v 
```

#### 常用命令行总结

```
# 查看当前状态
git status

# 查看所有分支
git branch -a

# 跳转到master分支
git checkout master

# 创建dev分支
git checkout -b dev

# 把本地login push到origin的dev -u表示同时建立关联，以后再推送到远程只需git push dev
git push -u origin dev 

# 分支合并
git checkout master
git merge dev
git push

# cherry-pick:将一些提交(commit)直接复制到当前所在的位置(head)
# 有两个分支a和b,现在指针指向b分支，想要a分支的C3和C7的提交,直接在b分支cherry-pick即可
git cherry-pick C3 C4 C7

# 向上移动4个提交记录,HEAD是git检出记录的引用,总是指向最近一次提交记录
git checkout HEAD~4

### 撤销变更
# 无视别人的提交，强制回滚
git log -- 查看git所有的版本号
git reset --hard 目标版本号
git push -f -- 强推

# 不影响别人的提交，只回滚自己的提交
#  三个版本,想重做第二个版本,但不想取消第三个版本,revert会取消第二个版本,然后在第三个版本的基础上,新建第四个版本
git revert -n 反做版本号
git revert -m 新版本名
git push
```

### .gitignore
> 提交时设置提交黑名单

```
# 更新后立即生效
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

### git生产使用规则(红线)

```
1. 必须从生产p40拉取新分支,如feature_1
2. 开发完代码后,从新分支feature_1合并到tue环境
3. tue测试通过,再从新分支feature_1合并到pre分支
4. pre测试通过,再从生产p40拉取新分支如feature_2,然后把feature_1合并到feature_2
5. 通过新分支feature_2打tag,发布生产
6. 观察一段时间，上线无异常，把feature_2合并到生产分支p40
```

![](https://note.youdao.com/yws/api/personal/file/WEB74fc7945eece71e842bb0a3d43736d98?method=download&shareKey=f281e6a45ecaaa624d0d20d44507b777)

### github搜索技巧
> 摘自：https://juejin.cn/post/6844904058755481607
> in:description springcloud  stars:>1000 forks:>10 pushed:>2019-09-03

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/8/170234b175c7aa07~tplv-t2oaga2asx-watermark.awebp)

### git gui推荐----gitkraken
> 下载地址：https://www.aliyundrive.com/s/hyp9wCpcvHj

破解方法：
```
vi C:\Users\lingjin.zou\AppData\Roaming\.gitkraken
添加：registration.date属性，配置模板如下

{
	"lastUpgradeScriptRun": "v6.1.0",
	"lastVersion": "6.4.1",
	"lastReleaseNotesDisplayed": "6.4.1",
	"windowSettings": {
		"x": 0,
		"y": 0,
		"width": 1024,
		"height": 1100,
		"fullScreen": false,
		"maximized": true
	},
	"registration": {
		"date": "2050-08-27T14:30:54.130Z",
		"loginType": null,
		"EULA": {
			"status": "has_not_agreed",
			"version": "0.0.0"
		},
		"email": null,
		"name": null,
		"status": "unregistered"
	},
	"appId": "3019f3ff1f62afe5c43fc0ffb257397f9665b50c"
}
```