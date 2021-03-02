# go中mysql的引包
在go中使用mysql的时候是需要引入两个包的
```go
import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
)
```

我们可以看到使用_引入了一个包，这个的意思是只会执行这个包的init方法，而不是导入全部的方法。
mysql驱动中引入的就是mysql包中的init方法，这时不能通过包名来调用包中的其他函数。导入时，驱动的初始化函数会调用sql.Register将自己注册在database/sql包的全局变量sql.drivers中
