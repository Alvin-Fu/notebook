**在使用pprof过程中的总结，排查内存泄露**

### pprof的使用
![web界面图](../../../.local/static/2020/8/4/Snipaste_2020-09-03_11-17-28.1599103068236.png)

`http://127.0.0.1:8080/debug/pprof/goroutine?debug=1` 在web界面使用debug=1和debug=2是会有不同的