---
title: ElasticSearch
linkTitle: ElasticSearch
weight: 1
description: >-
     ElasticSearch
---

## ElasticSearch

```python
import requests
import json
url = ("https://elasticsearch-boeing-ai.apps.kcs-prod-clt.k8s.boeing.com/_cat"
       # "/indices?"
       # "&s=docs.count:desc"
       # "/allocation?"
       "/plugins?"
       "&format=json&pretty&error_trace=true&v=true"   
)

# print(url)
headers = {'Content-Type': 'application/json'}
resp = requests.get(url,headers=headers)
resp.json()

url = ("https://elasticsearch-boeing-ai.apps.kcs-prod-clt.k8s.boeing.com/pdf_documents/_search?"
       "&q=fuselage"
       "&filter_path=hits.hits,-hits.hits._id"  
       "&sort=_score:desc,page_number"
       "&size=5"
       "&format=json&pretty=true&error_trace=true"
)

# print(url)
headers = {'Content-Type': 'application/json'}
resp = requests.get(url,headers=headers)
resp.json()



```
## elasticsearch.yml

```
cluster.name: "docker-cluster"
network.host: 0.0.0.0
discovery.type: single-node
xpack.security.enabled: false
xpack.security.transport.ssl.enabled: false
xpack.security.http.ssl.enabled: false

```
