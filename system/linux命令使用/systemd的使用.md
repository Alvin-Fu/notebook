systemd是linux中新的系统启动方式，用于代替之前的SysV init的启动方式

disable 是将实例的配置删掉
enable 添加新的配置
mask 注销，让启动脚本变为空  ln -s '/dev/null' '/etc/systemd/system/cups.service'
unmask  取消注销