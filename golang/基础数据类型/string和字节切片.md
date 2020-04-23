在平时的使用过程中我们会经常用到string和[]byte之间的转换

### string到[]byte
```
func StringToBytesHeader(str string)[]byte{
	sh := (*reflect.StringHeader)(unsafe.Pointer(&str))
	bh := reflect.SliceHeader{
		Data: sh.Data,
		Len: sh.Len,
		Cap: sh.Len,
	}
	return *(*[]byte)(unsafe.Pointer(&bh))
}

func StringToBytes(str string)[]byte{
	return []byte(str)
}
BenchmarkStringToBytes-8         	30068103	        39.0 ns/op
BenchmarkStringToBytesHeader-8   	160642612	         7.47 ns/op
```


### []byte到string
```
func ByteStringPointer(b []byte) string{
	return *(*string)(unsafe.Pointer(&b))
}

func ByteString(b []byte)string{
	return string(b)
}
BenchmarkByteStringPointer-8     	160850340	         7.47 ns/op
BenchmarkByteString-8            	26140600	        43.5 ns/op

```