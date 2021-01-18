# redis中的string结构
redis中的string的应用是很广泛的，他的实现方式也是比较特别的不是使用char数组实现的，而是通过SDS(Simple Dynamic String)实现的
## SDS介绍
SDS：简单动态字符串，使用c语言实现的。