### 1、向http服务发送参数
curl -X PUT 'HTTP的地址' -d '参数'   // 通过curl向http服务发送put请求


## 2、下载文件
### 2.1 使用curl将服务器的文件下载
curl -O 'http://www.test.com/test.text'   这样就可以将文件下载到本地
循环下载文件也是使用,图片也是类似的
curl -O 'http://www.test.com/test[1-5].text'

### 2.2、
