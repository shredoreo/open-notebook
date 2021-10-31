```bash
# 
show dbs

# 切换到test库，若不存在会在插入数据的时候自动创建
use test

# db代表当前的数据库，不需要建表，直接新增
db.fruits.save({name:'苹果', price:5})

# 查找
db.fruits.find()
db.fruits.find({price:5})


```



```js
//发布订阅模式
const conf = require("./conf");
const MongoClient = require("mongodb").MongoClient;
const EventEmitter = require("events").EventEmitter;

class Mongodb {
  constructor(conf) {
    this.conf = conf;
    this.emitter = new EventEmitter();

    this.client = new MongoClient(conf.url, {
      useNewUrlParser: true,
    });

    this.client.connect((err) => {
      if (err) {
        throw err;
      }
      console.log("连接成功");

      this.emitter.emit("connect");
    });
  }

  col(colName, dbName = conf.dbName) {
    return this.client.db(dbName).collection(colName);
  }

  /**
   * 订阅连接事件
   * @param {*} event
   * @param {*} cb
   */
  once(event, cb) {
    this.emitter.once(event, cb);
  }
}

module.exports = new Mongodb(conf);

```

