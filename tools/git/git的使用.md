作为最常用的版本管理工具，git是分布式的

## git中常用的命令
### 回滚版本
首先使用git log查看commit的id
git reset --hard id回滚到指定的版本
git checkout -- 文件名 将指定的文件回滚到前一个版本

### git中的submodule的使用
在删除子模块的时候应该注意的地方
	1. rm -rf 子模块目录  `删除子模块的目录及源码`
	2. vim .gitmodules   `删除文件中子模块相关的条目`
	3. vim .git/config   `删除配置项中子模块相关条目`
	4. rm .git/module/*  `删除module目录中的子模块对应entos
	5. 的目录，不要删除别的哟`
在做完上面的4个步骤后,可能会出现的问题：
在使用git submodule的命令时会出现
```
$ git submodulec
fatal: no submodule mapping found in .gitmodules for path 'vendor/github.com/Alvin-Fu/testmod'
出现这个很有可能是因为git中的修改没有提交，在提交以后就会消失
```
如果分支是干净的就不会出现这种问题

## git使用时出现的问题
### 1、 ERROR: Repository not found
在使用的过程会出现远端url的报错
**使用remote解决**
`git remote ser-url origin git@github.com:xxxx/xxx.git` 一般使用url就可以了，主要要加.git
`git remote ser-url origin https://xxx/xxx/xxx.git`

**查看**
`git remote -v`

## 修改commit message
使用git commit --amend可以对上一次的进行修改
也可以使用git reset --soft (commit id) 进行修改

## pull和fetch的区别

**fetch：**将远程仓库的包含的所有分支的最新commit-id，将其记录到.git/FETCH_HEAD中

```text
步骤：
	git fetch origin master:alvinfu（获取最新版本）
	git diff alvinfu（查看不同）
	git merge alvinfu(合并alvinfu分支到本地master分支)
	git branch -d  alvinfu(如果不想保留alvinfu分支可以删除)
```
**pull：**先比对本地的FETCH_HEAD和远端的commit-id，然后通过fetch获取，再利用merge进行合并
可以认为pull是fetch和merge的合并



















