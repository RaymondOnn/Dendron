---
id: 745z0nannsprkwe38yteyxs
title: Glue
desc: ''
updated: 1691623939859
created: 1691623605574
---

```py
import json
import boto3

client = boto3.client('glue')

# note:if glue crawler is not ready, invoking glue crawler will not happen

def lamba_handler(event, context):
    # polling of crawler status can be done like this
    # client.get_crawler(<GLUE_CRAWLER_NAME>)
    
    response = client,start_crawler(<GLUE_CRAWLER_NAME>)

```