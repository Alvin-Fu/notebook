# redis中的string结构
redis中的string的应用是很广泛的，他的实现方式也是比较特别的不是使用char数组实现的，而是通过SDS(Simple Dynamic String)实现的
## SDS介绍
SDS：简单动态字符串，使用c语言实现的。
```c
struct sdshdr{
    //记录buf数组已使用字节的数量
    //等于SDS所保存字符串的长度
    int length; 
    //记录buf数组未使用字节的数量
    int free;
    //buf数组
    char[] buf;
};
buf中会保留一个字节用于结尾，当free等于0的时候length是实际的buf长度减一，free不等于0的时候
```
