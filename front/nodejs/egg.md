- 依赖

```bash
# egg-init工具
$ npm i egg-init -g
# 初始化egg项目
$ egg-init egg --type=simple
$ cd egg-example
$ npm i
```





- router层

一般的业务逻辑都使用controller处理，但可能有一些请求 会转发到其他服务器上，所以用router。

