# golang源码阅读之map
[go1.11版map源码的位置](https://github.com/Alvin-Fu/readgolangcode/blob/master/map.go)
[golang中的操作符](https://github.com/Alvin-Fu/notebook/blob/master/golang/operator/operator.md)

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
![title](../.local/static/2019/10/2/Snipaste_2019-11-04_15-58-33.1572928191038.png)

结构中 
	1. buckets是一个数组， 每一个后面会有一个bmap的链表，其中每一个bmap中最对只能保存
	2. oldbuckets 只是在扩容的时候使用
	3. extra表示的是在key和value中不包含指针的情况下，并行元素小于128k的时候进行内联，这个时候为了避免gc扫描整个hmap就是使用extra
	4. mapextra这个内部的overflow和oldoverflow还是存放的bmap
注意golang中的map在扩容的过程中，还是一个链表，是以buckets的形式进行扩容的

## 2、创建一个map
就是初始化一个map
```
func makemap(t *maptype, hint int, h *hmap) *hmap {
	if hint < 0 || hint > int(maxSliceCap(t.bucket.size)) {
		hint = 0
	}

	// initialize Hmap
	if h == nil {
		h = new(hmap)
	}
	h.hash0 = fastrand()

	// find size parameter which will hold the requested # of elements
	B := uint8(0)
	// 确定初始化的时候buckets的大小
	for overLoadFactor(hint, B) {
		B++
	}
	h.B = B
	// allocate initial hash table
	// if B == 0, the buckets field is allocated lazily later (in mapassign)
	// If hint is large zeroing this memory could take a while.
	if h.B != 0 {
		var nextOverflow *bmap
		// 给buckets分配内存
		h.buckets, nextOverflow = makeBucketArray(t, h.B, nil)
		if nextOverflow != nil {
			h.extra = new(mapextra)
			h.extra.nextOverflow = nextOverflow
		}
	}

	return h
}

```


## 3、map中对外提供的操作
### 3.1、Get操作
从map中获取一个元素，需要对key进行去hash然后在去找到那个元素，因此go中也是类似的
![title](../.local/static/2019/10/2/Snipaste_2019-11-04_17-41-52.1572928201136.png)
从图中可以看到，
1. 根据hash值的前8位定位桶中的格子
2. hash的后B位是定位在那个bucket中
3. 使用整个hash值判断key是否存在
4. 如果bmap中没有机会去overflow指向的下一个去查找，直到找到或者没有找到
注意tophash的作用就是快速的比较，当前面的几位都不匹配是就可以快速的去后面查找了
代码：
```

```
### 3.2、Put操作
put操作基本就是get操作的逆操作
1. 对key计算出相应的hash值
2. 通过后八位找到bmap
3. 通过前八位找到key是否存在，如果不存在判断bmap中是否有空位，如果没有就进行溢出桶，使用overflow指向，将key和value放到新的bmap中，在放置key的时候是放在第一个出现空位的地方
![title](../.local/static/2019/10/2/Snipaste_2019-11-04_20-14-40.1572928207232.png)
bmap中的空格是由于删除操作造成的


### 3.3、Delete操作
delete的操作和put的操作基本一致，只是delete是将key和value删除

## 4、map的迭代

## 5、map中扩容
扩容的主要目的是为了提高map的查询速度
扩容的条件：
	1. 超过了负载因子（进行二倍扩容）
	2. 有了太多的溢出桶 （桶数量不变的情况下，进行横向扩展）
	3. 当前不是正在扩容
	
















