# AntV

## L7

### 原理

场景图层

实际绘图图层



# cp 5

```bash
#安装element
vue add element 
# 会安装vue-cli-plugin-element


#安装echarts
npm i -S echarts
```



# CP8

在vue中使用echarts+百度地图

- 需要引入拓展，才能在options中使用bmap属性

```js
import 'echarts/extension/bmap/bmap'
```



- 出现错误："Error: BMap api is not loaded"，需要在index.html中引入百度地图2.0版本

```html
      <script type="text/javascript" src="https://api.map.baidu.com/api?v=2.0&ak=kXr2yZNoOLeZSoqMg9yuDEKuONiN0pUD"></script>

```



## 水球图插件

```bash
npm i -S echarts-liquidfill
```

## 词云插件

```bash
npm i -S echarts-wordcloud
```

