# 01 prepare

1. [].slice.call(lis): 将伪数组转换为真数组

数组的slice()截取数组中指定部分的元素, 生成一个新的数组  [1, 3, 5, 7, 9], slice(0, 3)

lis2 = Array.prototype.slice.call(lis)  // lis.slice()

2. node.nodeType: 得到节点类型

```js
const elementNode = document.getElementById('test')const attrNode = elementNode.getAttributeNode('id')const textNode = elementNode.firstChildconsole.log(elementNode.nodeType, attrNode.nodeType, textNode.nodeType)//1, 2, 3
```





3. Object.defineProperty(obj, propertyName, {}): 给对象添加属性(指定描述符)

**Object.defineProperties()** 方法直接在一个对象上定义新的属性或修改现有属性，并返回该对象。

### 参数



- `obj`

  在其上定义或修改属性的对象。

- `props`

  要定义其可枚举属性或修改的属性描述符的对象。对象中存在的属性描述符主要有两种：数据描述符和访问器描述符（更多详情，请参阅[`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)）。描述符具有以下键：

  `configurable``true` 当且仅当该属性描述符的类型可以被改变并且该属性可以从对应对象中删除。 **默认为 false**`enumerable``true` 当且仅当在枚举相应对象上的属性时该属性显现。 **默认为 false**`value`与属性关联的值。可以是任何有效的JavaScript值（数字，对象，函数等）。 **默认为 undefined.**`writable``true`当且仅当与该属性相关联的值可以用[assignment operator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment_Operators)改变时。 **默认为 false**`get`作为该属性的 getter 函数，如果没有 getter 则为[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。函数返回值将被用作属性的值。 **默认为 undefined**`set`作为属性的 setter 函数，如果没有 setter 则为[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。函数将仅接受参数赋值给该属性的新值。 **默认为 undefined**

`**Object.defineProperty()**` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

- 该属性不兼容IE8，Vue使用了这个属性，所以不支持IE8

```js
  Object.defineProperty(obj, 'fullName2', {
    configurable: false, //是否可以重新define
    enumerable: true, // 是否可以枚举(for..in / keys())
    value: 'A-B', // 指定初始值
    writable: false // value是否可以修改
  })
```



4. Object.keys(obj): 得到对象自身可枚举属性组成的数组





5. obj.hasOwnProperty(prop): 判断prop是否是obj自身的属性





6. DocumentFragment: 文档碎片(高效批量更新多个节点)

 // document: 对应显示的页面, 包含n个elment  一旦更新document内部的某个元素界面更新
  // documentFragment: 内存中保存n个element的容器对象(不与界面关联), 如果更新framgnet中的某个element, 界面不变

```js
/*
  <ul id="fragment_test">
    <li>test1</li>
    <li>test2</li>
    <li>test3</li>
  </ul>
   */
  const ul = document.getElementById('fragment_test')
  // 1. 创建fragment
  const fragment = document.createDocumentFragment()
  // 2. 取出ul中所有子节点取出保存到fragment
  let child
  while(child=ul.firstChild) { // 一个节点只能有一个父亲
    fragment.appendChild(child)  // 先将child从ul中移除, 添加为fragment子节点
  }

  // 3. 更新fragment中所有li的文本
  Array.prototype.slice.call(fragment.childNodes).forEach(node => {
    if (node.nodeType===1) { // 元素节点 <li>
      node.textContent = 'atguigu'
    }
  })

  // 4. 将fragment插入ul
  ul.appendChild(fragment)
```



# 02 数据代理

1. vue数据代理: data对象的所有属性的操作(读/写)由vm对象来代理操作
2. 好处: 通过vm对象就可以方便的操作data中的数据
3. 实现:
  1). 通过Object.defineProperty(vm, key, {})给vm添加与data对象的属性对应的属性
  2). 所有添加的属性都包含get/set方法
  3). 在get/set方法中去操作data中对应的属性

```js
/*
相关于Vue的构造函数
 */
function MVVM(options) {
  // 将选项对象保存到vm
  this.$options = options;
  // 将data对象保存到vm和datq变量中
  var data = this._data = this.$options.data;
  //将vm保存在me变量中
  var me = this;
  // 遍历data中所有属性
  Object.keys(data).forEach(function (key) { // 属性名: name
    // 对指定属性实现代理
    me._proxy(key);
  });

  // 对data进行监视
  observe(data, this);

  // 创建一个用来编译模板的compile对象
  this.$compile = new Compile(options.el || document.body, this)
}

MVVM.prototype = {
  $watch: function (key, cb, options) {
    new Watcher(this, key, cb);
  },

  // 对指定属性实现代理
  _proxy: function (key) {
    // 保存vm
    var me = this;
    // 给vm添加指定属性名的属性(使用属性描述)
    Object.defineProperty(me, key, {
      configurable: false, // 不能再重新定义
      enumerable: true, // 可以枚举
      // 当通过vm.name读取属性值时自动调用
      get: function proxyGetter() {
        // 读取data中对应属性值返回(实现代理读操作)
        return me._data[key];
      },
      // 当通过vm.name = 'xxx'时自动调用
      set: function proxySetter(newVal) {
        // 将最新的值保存到data中对应的属性上(实现代理写操作)
        me._data[key] = newVal;
      }
    });
  }
};
```

