在使用map过程中遇到的问题
```
type Map struct {
	mu Mutex
	// 这个read是原子操作的，因此是并发安全的
	read atomic.Value // readOnly， 这部分内容是并发安全的，无论是否持有锁
	dirty map[interface{}]*entry    // 可以理解为这部分数据是经常被操作的
	misses int			// 用于记录read和dirty之间的关系，即上一次read更新时dirty的负载数量
}

```
