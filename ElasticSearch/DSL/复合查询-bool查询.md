## 环境准备
```json
PUT /cars_index
{
  "mappings": {
    "properties": {
      "brand": { "type": "keyword" },
      "model": { "type": "text" },
      "price": { "type": "integer" },
      "color": { "type": "keyword" },
      "status": { "type": "keyword" } # normal / damaged
    }
  }
}

POST /cars_index/_bulk
{ "index": { "_id": "1" } }
{ "brand": "Tesla", "model": "Model 3 Performance", "price": 250000, "color": "Red", "status": "normal" }
{ "index": { "_id": "2" } }
{ "brand": "Tesla", "model": "Model Y Long Range", "price": 300000, "color": "White", "status": "normal" }
{ "index": { "_id": "3" } }
{ "brand": "BYD", "model": "Han EV", "price": 220000, "color": "Red", "status": "normal" }
{ "index": { "_id": "4" } }
{ "brand": "Tesla", "model": "Model 3", "price": 180000, "color": "Black", "status": "damaged" }
```
## 查询
```json
GET /cars_index/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "brand": "Tesla" } }
      ],
      "filter": [
        { "term": { "status": "normal" } },
        { "range": { "price": { "gte": 200000, "lte": 400000 } } }
      ],
      "should": [
        { "term": { "color": "Red" } },
        { "match": { "model": "Performance" } }
      ],
      "must_not": [
        { "term": { "color": "Black" } }
      ],
      "minimum_should_match": 0 
    }
  }
}
```
## 结果
```json
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 2.2106245,
    "hits": [
      {
        "_index": "cars_index",
        "_id": "1",
        "_score": 2.2106245,
        "_source": {
          "brand": "Tesla",
          "model": "Model 3 Performance",
          "price": 250000,
          "color": "Red",
          "status": "normal"
        }
      },
      {
        "_index": "cars_index",
        "_id": "2",
        "_score": 0.35667494,
        "_source": {
          "brand": "Tesla",
          "model": "Model Y Long Range",
          "price": 300000,
          "color": "White",
          "status": "normal"
        }
      }
    ]
  }
}
```
## 分析
暂时无法在飞书文档外展示此内容
1. Filter 是性能优化的王道：
    - 尽量把不需要计算相关度的条件放在 filter 里。ES 会缓存这些结果，下次查询瞬时完成。
2. minimum_should_match：
    - 如果 bool 查询中没有 must 或 filter，那么 should 中必须至少匹配一个。
    - 如果有 must 或 filter，那么 should 默认一个都不匹配也能搜出来（只是分数低）。
3. Score (分值) 来源
    - 最终的 _score 是 must 和 should 子句分值的加总。
4. 嵌套能力：
    - bool 里面可以再嵌套 bool。例如：must 里面套一个 should 组合（实现 A AND (B OR C)）。
