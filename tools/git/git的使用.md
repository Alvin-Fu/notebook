作为最常用的版本管理工具，git是分布式的

## git中常用的命令
### 回滚版本
首先使用git log查看commit的id
git reset --hard id回滚到指定的版本
git checkout -- 文件名 将指定的文件回滚到前一个版本

### git中的submodule的使用
在删除