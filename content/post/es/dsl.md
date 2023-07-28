---
title: "Dsl"
date: 2023-07-28T14:37:18+08:00
tags: [es]
---


安装 es 后开启在其它机器上的访问
```
xpack.security.transport.ssl.enabled: true  
discovery.seed_hosts: ["0.0.0.0"]
network.host: 0.0.0.0
xpack.security.enabled: true
```

```
1. 经过验证，确实是 kibana 的 ilm 导致dscan 重复的问题。

2. 更正早上的一个错误
alias 相同的 indices，只有其中一个 index 可置为 is_wirte_index = true。

当 is_wirte_index = false, 不等同于 readonly
它只是控制往哪个 index 的写入
```
#### is_write_index
```
PUT %3Cprod-dscan_history-%7Bnow%2Fd%7ByyyyMMdd%7C%2B08%3A00%7D%7D-000001%3E
{
  "aliases": {
    "prod_dscan_history": {
      "is_write_index": true
    }
  }
}


POST _aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "prod-dscan-20200423-000003",
                 "alias" : "prod_dscan",
                 "is_write_index" : false
            }
        }
    ]
}
```

### readonly
```
curl 'http://user001:xazb001@39.104.153.150:9200/index/_settings?pretty';

curl -H "Content-Type: application/json" -XPUT http://user001:xazb001@39.104.153.150:9200/_all/_settings -d \
'{"index.blocks.read_only_allow_delete": null}';

curl http://user001:xazb001@39.104.153.150:9200/index/_alias?pretty;


curl -H "Content-Type: application/json" -XPOST http://user001:xazb001@39.104.153.150:9200/_aliases -d '{
    "actions" : [
        {"remove": {"index": "test1", "alias": "alias2"}}
    ]
}'
```

### es watermark
```
put _cluster/settings
{
    "transient":{
        "cluster":{
            "routing":{
                "allocation.disk.threshold_enabled":true,
                "allocation.disk.watermark.high":"95%",
                "allocation.disk.watermark.low":"90%"
            }
        }
    }
}
```

### delete where updatetime <= now - 6 months
https://blog.csdn.net/weixin_41004350/article/details/85620572
```
curl -H "$H" "http://$ACC_PWD@$host:9200/$idx/_delete_by_query?conflicts=proceed&wait_for_completion=false&scroll_size=5000&slices=5' -d '{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "updatetime": {
              "lt": "now-6M"
            }
          }
        }
      ]
    }
  }
}'
```

### select vendorcn, count(*)
```
{
  "size": 0,
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "taskbatchesid": 17
          }
        },
        {
          "term": {
            "device.primary_type.untouched": "ICS"
          }
        }
      ]
    }
  },
  "aggs": {
    "vendorcn": {
      "terms": {
        "field": "product.vendorcn.untouched",
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}
```

### select distinct(vendorcn)
```
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "taskbatchesid": 17
          }
        },
        {
          "term": {
            "device.primary_type.untouched": "ICS"
          }
        }
      ]
    }
  },
  "collapse": {
    "field": "product.vendorcn.untouched"
  },
  "_source": [
    "product.vendorcn"
  ]
}
```

- _cat/health?v
- bank/_bulk?pretty&refresh
- curl --data-binary "@..."
```
{"index":{"_id":"0"}}
{...}
{"index":{"_id":"1"}}
{...}
```

- /bank/_search?pretty
```
{
  "query": { "match_all": {} },
        // {"match": {"address": "mill lane"}}  // mill or lane
       // {"match_phrase": {"address": "mill lane"}}
  "sort": [
    { "account_number": "asc" }
  ],
  "from": 10,
  "size": 10
}
```

```
{
  "query": {
    "bool": {
      "must": [{"match": {"age": 40}}],
      "must_not": [{"match": {"state": "ID"}}
    }
  }
}
```

# nested
```
{
  "query": {
    "nested": {
      "path": "product",
      "query": {
        "bool": {
          "filter": [
            {
              "term": {
                "product.vendor.untouched": "microsoft"
              }
            }
          ]
        }
      }
    }
  },
  "_source": [
    "product"
  ]
}
```

```
{
  "size": 0,
  "aggs": {
    "vendor": {
      "nested": {
        "path": "product"
      },
      "aggs": {
        "vendor_aggs": {
          "terms": {
            "field": "product.vendor.untouched",
            "size": 10000
          }
        }
      }
    }
  }
}
```

