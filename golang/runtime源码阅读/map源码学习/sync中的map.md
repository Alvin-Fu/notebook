在使用map过程中遇到的问题
```type Map struct {
	mu Mutex
	// 这个read是原子操作的，因此是并发安全的
	read atomic.Value // readOnly， 
	dirty map[interface{}]*entry
	misses int
}

```
