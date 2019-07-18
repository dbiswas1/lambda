# Integrate AWS Lambda, SQS and SNS - a AWS Serverless sample

##  TOC
- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [What is Lambda ?](#what-is-lambda--)
- [What is SQS ?](#what-is-sqs--)
- [What is SNS ?](#what-is-sns--)
- [Setup Details](#setup-details)
  * [Prerequisite](#prerequisite)
  * [Launch the Serverless stack](#launch-the-serverless-stack)
  * [Screen Caps](#screen-caps)
      - [Lambda Function](#lambda-function)
      - [SNS Topic -> Click Publish Message](#sns-topic----click-publish-message)
      - [SNS-Topic -> Sample Input](#sns-topic----sample-input)
      - [SQS-Topic -> View output](#sqs-topic----view-output)
- [Clean the setup](#clean-the-setup)
  
## Overview
This blog will show how to Process a request from SNS using AWS lambda and pass it for further processing via SQS.
These integration can be typically used for infrastructure automation.

1. Lets say you have an event captured in SNS from monitoring system and now want to take action against it. 
2. Depending on the type of alert you will have lambda function to understand which Queue the request to be placed
3. Jenkins job subscribed to the particular Queue will consume data and take appropriate action as defined in the job

## Problem Statement
For this new setup, a JSON blob (input) is to be published to an SNS topic, processed by a Lambda function, and then the
output pushed into an SQS queue.

```
Input: {“text”: “Hello World”, “amount”: 10}
Expected Output: {“Result”: “ Hello World”}
```

## What is Lambda ?
AWS Lambda is a compute service that lets you run code without provisioning or managing servers. AWS Lambda executes your 
code only when needed and scales automatically, from a few requests per day to thousands per second.

REF: https://docs.aws.amazon.com/lambda/latest/dg/welcome.html

## What is SQS ?
Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale 
microservices, distributed systems, and serverless applications

REF: https://aws.amazon.com/sqs/

## What is SNS ?
Amazon Simple Notification Service (SNS) is a highly available, durable, secure, fully managed pub/sub messaging service
that enables you to decouple microservices, distributed systems, and serverless applications. Amazon SNS provides topics
for high-throughput, push-based, many-to-many messaging. Using Amazon SNS topics, your publisher systems can fan out 
messages to a large number of subscriber endpoints for parallel processing.

REF: https://aws.amazon.com/sns/

## Setup Details

### Prerequisite
1. Valid AWS Account with IAM roles for creating LAMBDA, SQS and SNS
2. AWS CLI setup in your local system to run the cloudformation template
> Note: This setup may incur cost

### Launch the Serverless stack
```
~  git clone https://github.com/AVM-Consulting/declarative-infrastructure.git
################################# Output #################################
Cloning into 'declarative-infrastructure'...
remote: Enumerating objects: 41, done.
remote: Counting objects: 100% (41/41), done.
remote: Compressing objects: 100% (34/34), done.
remote: Total 41 (delta 5), reused 36 (delta 2), pack-reused 0
Unpacking objects: 100% (41/41), done.
################################# Output #################################

cd declarative-infrastructure/lambda

~/declarative-infrastructure/lambda  aws cloudformation create-stack --stack-name serverless-stack --capabilities CAPABILITY_IAM --template-body file://serverless.yaml

################################# Output #################################
~/declarative-infrastructure/lambda  aws cloudformation create-stack --stack-name serverless-stack --capabilities CAPABILITY_IAM --template-body file://serverless.yaml
{
    "StackId": "arn:aws:cloudformation:us-west-2:303882392497:stack/serverless-stack/e7d5d520-a971-11e9-82dc-0af0816e310e"
}
################################# Output #################################
```
 What did the above command do ?
 1. Created a SNS TOPIC called **Left-Pad-Topic**
 2. Created a SQS Queue called **serverless-stack-SQSQueue**
 3. Created a Lambda Function **left-Pad-Test1** which function is :
    * to accept input from SNS as a json in the form of {"text":"Hello World","space":"10"}
    * Use the second parameter from the input as a space as publish the output to SQS
    * Expected out put will be  <spaces followed by>  {"text":"Hello World","space":"10"}
>> NOTE: Above is just a hypothetical case main motive is to explain the integration of SQS, SNS with Lambda

### Screen Caps

##### Lambda Function
![LAMBDA1](https://github.com/dbiswas1/lambda/raw/master/01-Lambda.png)

##### SNS Topic -> Click Publish Message
![SNS1](https://github.com/dbiswas1/lambda/raw/master/02-SNS-1.png)

##### SNS-Topic -> Sample Input
![SNS2](https://github.com/dbiswas1/lambda/raw/master/03-SNS-2.png)

##### SQS-Topic -> View output
![SQS1](https://github.com/dbiswas1/lambda/raw/master/04-SQS-1.png)

![SQS2](https://github.com/dbiswas1/lambda/raw/master/05-SQS-2.png)

## Clean the setup
```
~/declarative-infrastructure/lambda  aws cloudformation delete-stack --stack-name serverless-stack

```

