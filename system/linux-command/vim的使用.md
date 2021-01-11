[vim-go的配置](https://learnku.com/articles/24924)
## vim中替换
字符串的替换
语法为：`:[addr]s/src str/dst str/[option]`
全局替换命令为：`:%s/src str/dst str/g`
解释：
[addr]表示检索范围，省略时表示当前行  "1,20":表示从第一行到第二十行， "%"：表示整个文件和"1,$"相同，".,$": 表示从当前行到文件尾
s: 表示替换操作
[option]: 表示操作类型，g表示全局替换，c表示进行确认，p表示替换结果逐行显示(Ctrl+L恢复屏幕)，省略option时仅对每一行第一个字符串替换
特殊字符需要使用"\"转义
[参考](https://www.cnblogs.com/beenoisy/p/4046074.html)