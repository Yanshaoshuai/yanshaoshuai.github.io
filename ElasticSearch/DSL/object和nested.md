## 案例
一个商品(SPU)通常对应多个规格(SKU),在存储商品时我们可以将SKU存为数组,此时有两种方案
### object数组
```json
PUT /products_object
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "skus": { 
        "properties": {
          "color": { "type": "keyword" },
          "size": { "type": "keyword" }
        }
      }
    }
  }
}
```
### nested数组
```json
PUT /products_nested
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "skus": {
        "type": "nested", 
        "properties": {
          "color": { "type": "keyword" },
          "size": { "type": "keyword" }
        }
      }
    }
  }
}
```
### 插入数据
```json
# 分别往两个索引存入同一条数据
PUT /products_object/_doc/1
{
  "title": "极简 T 恤",
  "skus": [
    { "color": "红色", "size": "M" },
    { "color": "蓝色", "size": "L" }
  ]
}

PUT /products_nested/_doc/1
{
  "title": "极简 T 恤",
  "skus": [
    { "color": "红色", "size": "M" },
    { "color": "蓝色", "size": "L" }
  ]
}
```

### obejct查询
```json
GET /products_object/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "skus.color": "红色" } },
        { "term": { "skus.size": "L" } }
      ]
    }
  }
}
#### 结果
```json
{
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 0.723315,
    "hits": [
      {
        "_index": "products_object",
        "_id": "1",
        "_score": 0.723315,
        "_source": {
          "title": "极简 T 恤",
          "skus": [
            {
              "color": "红色",
              "size": "M"
            },
            {
              "color": "蓝色",
              "size": "L"
            }
          ]
        }
      }
    ]
  }
}
```
### nested查询
```json
GET /products_nested/_search
{
  "query": {
    "nested": {
      "path": "skus",
      "query": {
        "bool": {
          "must": [
            { "term": { "skus.color": "红色" } },
            { "term": { "skus.size": "L" } }
          ]
        }
      }
    }
  }
}
```
#### 结果
```json
{
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 0,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  }
}
```

### 对比
#### object扁平化存储
当 skus 是默认的 object 类型时，ES 会把数组里的对象拆散并压平，合并到主文档的各个字段中。
底层实际存储格式：
```json
{
  "title": "极简 T 恤",
  "skus.color": ["红色", "蓝色"],
  "skus.size":  ["M", "L"]
}
```
#### nested独立文档存储
当 skus 定义为 nested 类型时，ES 会将每个数组元素作为一个隐藏的子文档独立存储。
底层实际存储格式：
在 Lucene 索引中，这会被拆成 3 个相互关联的文档：
- 隐藏子文档 1：{ "skus.color": "红色", "skus.size": "M" }
- 隐藏子文档 2：{ "skus.color": "蓝色", "skus.size": "L" }
- 根主文档：{ "title": "极简 T 恤" }
- 后果：每个子文档内部的属性是强绑定的。
- 搜索逻辑：当你使用 nested 查询搜索“红色且L码”时，ES 会进入每一个子文档检查：
  - 子文档 1 是红色但不是 L 码（不匹配）；
  - 子文档 2 是 L 码但不是红色（不匹配）；
  - 最终结论：不匹配。这才是符合业务逻辑的正确结果。
