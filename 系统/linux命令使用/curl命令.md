### 1、向http服务发送参数
curl -X PUT 'HTTP的地址' -d '参数'   // 通过curl向http服务发送put请求


## 2、下载文件
### 2.1 使用curl将服务器的文件下载
curl -o test.txt http://www.test.com/test.txt
curl -O 'http://www.test.com/test.txt'   这样就可以将文件下载到本地

### 2.2 循环下载
curl -O 'http://www.test.com/test[1-5].txt'

### 2.3 下载重名
curl -o #1_#2.text 'http://www.test.com/{hello,bb}/test[1-5].txt'
这样可以使得hello/test1.txt变成hello_test1.txt



