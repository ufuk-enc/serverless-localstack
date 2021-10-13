# Serverless Localstack POC #

Pre-requisites:

1. [Docker](https://www.docker.com/get-started)  
2. [Localstack Docker image](https://hub.docker.com/r/localstack/localstack)
3. [Serverless CLI](https://www.serverless.com/framework/docs/getting-started/)
4. [AWS CLI](https://aws.amazon.com/cli/)

## Localstack Setup ##

### Download Localstack Docker Image from Docker Hub ##
```
docker pull localstack/localstack
```

### Run Localstack container ###

```
docker run -itd --name localstack --env LAMBDA_EXECUTOR=docker --env DEFAULT_REGION=eu-west-1 -v /var/run/docker.sock:/var/run/docker.sock -p 4566:4566 localstack/localstack:latest
```

## S3 Bucket Setup ##

```
aws --endpoint http://localhost:4566 s3 mb s3://serverless-localstack-poc
```

## Project Setup ##

### Install NPM dependencies ###
```
npm i
```

## Deploy ##

```
sls deploy --stage local
```

On successful compile and deploy, you can see the following:

```
Serverless: Using serverless-localstack
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service serverless-localstack-poc.zip file to S3 (6.76 kB)...
Serverless: Validating template...
Serverless: Skipping template validation: Unsupported in Localstack
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
..............
Serverless: Stack create finished...
Service Information
service: serverless-localstack-poc
stage: local
region: eu-west-1
stack: serverless-localstack-poc-local
resources: 4
api keys:
  None
endpoints:
functions:
  hello: serverless-localstack-poc-local-hello
layers:
  None
```

## Test ##

```
aws --endpoint-url=http://localhost:4566 lambda invoke --function-name arn:aws:lambda:eu-west-1:000000000000:function:serverless-localstack-poc-local-hello --log-type Tail response.json
```

When you read the response.json, output should be something like:
```
{
  "body": {
    "message": "Go Serverless v1.0! Your function executed successfully!",
    "input": {}
  },
  "statusCode": 200
}
```
