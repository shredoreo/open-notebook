# 基础

## api

Https://nodejs.org/dist/latest-v10.x/docs/api/

http://nodejs.cn/api



运行hello world

```bash
node helloworld/index.js
# 或
node helloworld
```

### Nodemon自动重启

```bash
npm i nodemon -g
nodemon helloworld
```

### 单元测试Jest

```bash
npm i jest -g

jest index.js --watch#监视测试


```

```js
test('TEST fun', () => {
  const fun = require('../index')
  const ret = fun()
  // expect(ret)
  //  .toBe('test return')
})
```



### 测试代码生成工具

- Fs 同步方法
- path包

### 生成测试文件名



## 异步编程

- jest通过done回调，来控制程序的结束时机。避免异步函数还没执行完，测试已经执行完了