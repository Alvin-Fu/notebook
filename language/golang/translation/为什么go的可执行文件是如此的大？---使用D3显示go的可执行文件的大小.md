[原文地址](https://dr-knz.net/go-executable-size-visualization-with-d3.html)
## 摘要
我有构建了一些小工具用于提取go的可执行文件的细节，并构建了一个D3的小应用交互式的将这些信息可视化为可缩放的树的映射。
下面是一个截图用于展示这个工具如何展示编译代码所占的大小，这是根据CockroachDB的一个组模块得来的：
![title](../../../.local/static/2020/10/1/size-demo-ss.1606132486318.png)
做这个的原因是，用于帮助我发现并且学习为什么go可执行文件的大小超过了我的预期。感谢这个工具，我对go如何构建可执行文件有了更多的发现。
想知道我发现了什么吗(继续往下看)
[源代码地址]( https://github.com/knz/go-binsize-viz)
## 背景和动机
我和我的同事正在忙着准备发布CockroachDB的最新19.1版本。CockroachDB是作为一个包含了所有功能的可执行文件发布的。
今天，这个最新的版本是123MB大，去掉88MB。这是
