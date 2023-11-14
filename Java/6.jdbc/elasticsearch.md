# 前言

[文档](https://cloud.tencent.com/developer/article/1583402)
Elasticsearch 是专门做搜索的

1. Elasticsearch 对模糊搜索非常擅长（搜索速度很快）
2. 从 Elasticsearch 搜索到的数据可以根据评分过滤掉大部分的，只要返回评分高的给用户就好了（原生就支持排序）
3. 没有那么准确的关键字也能搜出相关的结果（能匹配有相关性的记录）
