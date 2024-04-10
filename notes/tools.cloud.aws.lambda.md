---
id: qc9ucllmi51v9v2kuz4lra6
title: Lambda
desc: ''
updated: 1691456316485
created: 1691045466050
---



AWS Lambda is a compute service to allow us to run code on cloud without managing servers

## Limitations
- Lambdas maximum runtime is 15 mins
- Max memory limit is 10GB
- Max of 5 layers / packages. Alternatively, can work within Docker containers or zip files
- - Max retries = 3
- Ways lambda can be invoked
  - Synchronously e.g via API Gateway
  - Asynchronously e.g. SNS, S3
  - Poll based e.g. SQS, Kinesis Data Stream
- Lambda destination?

## How code executions works with Lambda
    1. Container spins Up
    2. Code is loaded
    3. Code is executed  
- This entire process is referred to as 'Cold Start' since it takes time to spin up a container and run the code.   
- Multiple of such processes can occur in parallel if needed



## Lambda Chaining
- Calling another lambda using lambda can be done via `boto3`
- However, better tool for this is AWS Step Functions


```py
import boto3
import json

client = boto3.client('lambda')

def lambda_handler(event, context):
  message = {'message': 'Hello from Lambda 1'}
  response = client.invoke(
    FunctionName=<INSERT_LAMBDA_2_ARN>,
    InvocationType='RequestResponse',
    Payload=json.dumps(message)
  )
  print(response)
  return {
    'statusCode': 200,
    'body': json.dumps('Hello from Lambda!')
  }
```


## Loading External Packages
``` bash
# Step 1: `cd` to the current working
cd <current working directory>

# Step 2: install package in current working directory
pip install <package_name> -t .

# Step 3: Zip all the files incl. the  .py file

# Step 4: Upload zip file. If file is too large, upload from S3 bucket
```

## Lambda Layers (free)
- Layers allows for sharing of packages / modules / code with other lambdas functions
- Helper code for creating layer for packages: https://github.com/soumilshah1995/AWS-lambda-Master-Course-Python/blob/main/Labs/Lab4/etl.py

## Using Docker Containers


## Lambda scheduled triggers
- To schedule triggers for lambda jobs, create rule in AWS EventBridge

## Some Considerations when setting up Lambdas
- [Concurrency Configuration](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)
- [Power Tuning (Memory Optimisation)](https://docs.aws.amazon.com/lambda/latest/operatorguide/profile-functions.html)
- [Failure Handling](https://www.youtube.com/watch?v=nqQh2KmHiLY&ab_channel=BeABetterDev)
- [ARM vs x86](https://www.youtube.com/watch?v=EZrAyIr0FJY&list=PLL2hlSFBmWwx5aS9AMYO-NndQITnEnqT4&index=9&ab_channel=SoumilShah)


## Lambda Function URLs

## 