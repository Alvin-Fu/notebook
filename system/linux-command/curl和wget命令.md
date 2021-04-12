curl -X PUT 'HTTP的地址' -d '参数'   // 通过curl向http服务发送put请求  curl -X PUT "http://127.0.0.1:15740/v1/config/log-level" -d "info" 
curl -X GET 'HTTP地址'  获取get信息
curl -X DELETE 'HTTP地址' 不能使用-d参数  //curl -X DELETE 'http://127.0.0.1:15740/v1/client/30001212'
使用wget下载prrof文件 wget "http://127.0.0.1:15000/debug/pprof/profile"
