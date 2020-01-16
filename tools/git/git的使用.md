作为最常用的版本管理工具，git是分布式的

## git中常用的命令
### 回滚版本
首先使用git log查看commit的id
git reset --hard id回滚到指定的版本
git checkout -- 文件名 将指定的文件回滚到前一个版本

### git中的submodule的使用
在删除子模块的时候应该注意的地方
	1. rm -rf 子模块目录  `删除子模块的目录及源码`
	2. vim .gitmodules `删除文件中子模块相关的条目`
	3. vim .git/config `删除配置项中子模块相关条目`
	4. rm .git/module/* `删除module目录中的子模块对应的目录，不要删除别的哟`
在做完上面的4个步骤后
