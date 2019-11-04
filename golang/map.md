# golang源码阅读之map

## 1、map的结构
```
type hmap struct {
	count     int // 元素个数
	flags     uint8
	B         uint8  // 2^B表示buckets这个数组的大小
	noverflow uint16 // 
	hash0     uint32 // 哈希种子
	// buckets是一个bucket的数组，每一个bucket中存放的是一个bmap，其中买个bmap中可以存8个key-value
	buckets unsafe.Pointer 
	// 扩容的时候使用，表示的是老的那个buckets
	oldbuckets unsafe.Pointer 
	nevacuate  uintptr        
	// 将包含指针的信息保存起来,使得gc在扫描的时候不会去扫描整个map
	extra *mapextra // optional fields
}

```

```
type mapextra struct {
	// If both key and value do not contain pointers and are inline, then we mark bucket type as containing no pointers.
	// 如果这个key和value是不包含指针或者内联的,我们标记这个桶是不包含指针的
	// This avoids scanning such maps.
	// 避免扫描每一个桶
	// However, bmap.overflow is a pointer. In order to keep overflow buckets alive,
	// we store pointers to all overflow buckets in hmap.extra.overflow and hmap.extra.oldoverflow.
	// overflow and oldoverflow are only used if key and value do not contain pointers.
	// overflow contains overflow buckets for hmap.buckets.
	// oldoverflow contains overflow buckets for hmap.oldbuckets.
	// The indirection allows to store a pointer to the slice in hiter.
	overflow    *[]*bmap // 指向的是后面的溢出桶
	oldoverflow *[]*bmap // 扩容的时候使用

	// nextOverflow holds a pointer to a free overflow bucket.
	// 指向空闲的 overflow bucket的指针
	nextOverflow *bmap
}
type bmap struct {
	// tophash generally contains the top byte of the hash value for each key in this bucket.
	// If tophash[0] < minTopHash, tophash[0] is a bucket evacuation state instead.
	// 这个就限定了一个桶可容纳的键值对的数量
	tophash [bucketCnt]uint8
	// 实际中每一个bmap中都会有一个overflow指向下一个bmap
	// Followed by bucketCnt keys and then bucketCnt values.
	// NOTE: packing all the keys together and then all the values together makes the code a bit more complicated than alternating key/value/key/value/...
	// but it allows us to eliminate padding which would be needed for, e.g., map[int64]int8.
	// Followed by an overflow pointer.
}

```
![map的机构图.png](0)

结构中 
	1. buckets是一个数组， 每一个后面会有一个bmap的链表，其中每一个bmap中最对只能保存
	2. oldbuckets 只是在扩容的时候使用
	3. extra表示的是在key和value中不包含指针的情况下，并行元素小于128k的时候进行内联，这个时候为了避免gc扫描整个hmap就是使用extra
	4. mapextra这个内部的overflow和oldoverflow还是存放的bmap
注意golang中的map在扩容的过程中，还是一个链表，是以buckets的形式进行扩容的

## 2、创建一个map
就是初始化一个map
```

```


## 3、map中的操作
### 3.1、Get操作
### 3.2、Put操作
























       