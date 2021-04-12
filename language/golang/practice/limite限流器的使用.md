# 限流器的使用
经常是需要限制请求的流量，防止顺时大流量请求到服务器导致服务器崩溃

## go的golang.org/x/time/rate包
这是基于令牌桶的实现
```go
type Limiter struct {
	mu     sync.Mutex
	limit  Limit      // 产生令牌的速率
	burst  int        // 桶的大小
	tokens float64
	// last is the last time the limiter's tokens field was updated
	last time.Time
	// lastEvent is the latest time of a rate-limited event (past or future)
	lastEvent time.Time
}
```
在使用的过程中Limiter会按照limit的频率向burst中放令牌直到burst满为止
