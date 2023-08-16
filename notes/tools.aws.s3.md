---
id: nhrpx0bc0ua9pi5uwpjfcen
title: S3
desc: ''
updated: 1691123922382
created: 1690967683005
---

# S3: AWS Simple Storage Service

Resources:
    - https://www.youtube.com/watch?v=2Gln1VDpHCA&list=PLL2hlSFBmWwzSx2Zrmuox_xUAlt5M_dI3&index=4&ab_channel=SoumilShah
    - boto3 for S3: https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html


## Recommended Folder Structure

Why? 
- By partitioning by year/month/day first, we can minimize scanning when trying to query files directly using Athena
  
```md
├── Bucket
│   ├── projectfiles
│   │   ├── year
│   │   │   ├── month
│   │   │   │   ├── day
│   │   │   │   │   ├── folder A
│   │   │   │   │   │   ├── file_1
│   │   │   │   │   │   ├── file_2
│   │   │   │   │   ├── folder B
│   │   │   │   │   │   ├── file_1
│   │   │   │   │   │   ├── file_2
├── projectfiles
│   │   ├── year
│   │   │   ├── month
│   │   │   │   ├── day
│   │   │   │   │   ├── folder A
│   │   │   │   │   │   ├── file_1
│   │   │   │   │   │   ├── file_2
```
## Storage Classes
- **Standard Tier** – The default and most commonly used tier. Great for ‘read after write’ scenarios requiring low latency, high durability and availability guarantees.
- **Intelligent Tier** – A tier that attempts to shuffle around your data into different tiers based on access patterns. Shuffling is done automatically and unbeknowns to the customer.
- **Standard IA** – Infrequent access tier. This is more suitable for older data that doesn’t need to be access often, but when it does, you need low and predictable performance.
- **One Zone IA** – The same as Standard IA, except your data is only persisted in one availability zone instead of a minimum of 3. Has the lowest durability guarantees at 99.5% and comes with lower cost points.
- **Glacier** – Suitable for archive data that needs occasional access. SLA for object retrieval ranges from a few minutes to a few hours. Low cost point.
- **Deep Glacier** – Best for long lasting archival data (i.e. for compliance, regulation, or policy purposes). Object retrieval within 12 hours of request time. Lowest cost point.
- **Outposts** – On premise S3. Essentially emulates Amazon S3 service within on-prem devices and makes the files available from local machines.

## Create S3 Bucket
### Code Implementation
``` py

import boto3

# create S3 client
client = boto3.client(
    's3',
    aws_access_key_id = AWS_ACCESS_KEY,
    aws_secret_access_key = AWS_SECRET_KEY,
    region_name = 'us-east-1'
)

# Creating a bucket
BUCKET_NAME = <ENTER_TEXT>

response = client.create_bucket(
    ACL='private',
    Bucket=BUCKET_NAME,
    CreateBucketConfiguration={
        'LocationConstraint': 'ap-southeast-1'
    }
)
```

### Full response
``` py
    response = client.create_bucket(
        ACL='private'|'public-read'|'public-read-write'|'authenticated-read',
        Bucket='string',
        CreateBucketConfiguration={
            'LocationConstraint': 'af-south-1'|'ap-east-1'|'ap-northeast-1'|'ap-northeast-2'|'ap-northeast-3'|'ap-south-1'|'ap-southeast-1'|'ap-southeast-2'|'ap-southeast-3'|'ca-central-1'|'cn-north-1'|'cn-northwest-1'|'EU'|'eu-central-1'|'eu-north-1'|'eu-south-1'|'eu-west-1'|'eu-west-2'|'eu-west-3'|'me-south-1'|'sa-east-1'|'us-east-2'|'us-gov-east-1'|'us-gov-west-1'|'us-west-1'|'us-west-2'|'ap-south-2'|'eu-south-2'
        },
        GrantFullControl='string',
        GrantRead='string',
        GrantReadACP='string',
        GrantWrite='string',
        GrantWriteACP='string',
        ObjectLockEnabledForBucket=True|False,
        ObjectOwnership='BucketOwnerPreferred'|'ObjectWriter'|'BucketOwnerEnforced'
    )
```

## Deleting Bucket

``` py
import boto3
S3 = boto3.resource(
    's3',
    aws_access_key_id = AWS_ACCESS_KEY,
    aws_secret_access_key = AWS_SECRET_KEY,
    region_name = 'us-east-1'
)
bucket = S3.Bucket(BUCKET_NAME)
# Delete Bucket
bucket.delete()
```

## Get All Buckets
```py
import boto3
S3 = boto3.resource(
    's3',
    aws_access_key_id = AWS_ACCESS_KEY,
    aws_secret_access_key = AWS_SECRET_KEY,
    region_name = 'us-east-1'
)
buckets = [bucket.name for bucket in s3.buckets.all()]
```

## Upload files into bucket

```py

with open("file.pdf", 'rb') as f:
    data = f.read()

client = boto3.client(
     's3', 
     aws_access_key_id = AWS_ACCESS_KEY, 
     aws_secret_access_key = AWS_SECRET_KEY, 
     region_name = 'us-east-1'
)
respone = client,put_object(
    ACL='private',
    Body=data,
    Bucket=BUCKET_NAME,
    Key='PDF/file.pdf'  # name of file
)    
```

## Delete File from bucket
- Deleting large numbers of files from S3 using Glue: https://www.youtube.com/watch?v=QgtjK0JHyRk&list=PLL2hlSFBmWwzSx2Zrmuox_xUAlt5M_dI3&index=10&ab_channel=SoumilShah
```py
# create client object
# delete file
response = client.delete_objects(
    Bucket=BUCKET_NAME,
    Delete={
        "Objects: [
            {
                'Key': 'File.pdf' 
            }
        ]
    }
)
```

## Get All Objects from Bucket

```py
response = client.list_objects(
    Bucket = BUCKET_NAME
)
print(reponse['Contents'])

```

## S3 Paginator Object

```py
paginator = client,get_paginator('list_objects_v2')
pages = paginator.paginate(Bucket=BUCKET_NAME, prefix='PDF/')

tmp = []
for page in pages:
    for obj in page['Contents']:
        tmp.append(obj['Kwy'])

```

## Reading CSV Files from Bucket

## Read Parquet Files from Bucket