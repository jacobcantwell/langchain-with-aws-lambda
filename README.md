# Using LangChain wth AWS Lambda

Add [LangChain](https://www.langchain.com/) LLM orchestration to AWS Lambda

## Prerequisites

* Python 3.12.x
* Pip3
* AWS CLI v2 installed and configured
* [jq](https://stedolan.github.io/jq/) - a lightweight and flexible command-line JSON processor

## AWS CLI

### Installing the AWS CLI v2

Install AWS CLI v2.

Verify that the AWS CLI installed correctly.

```bash
aws --version
```

### Configuring the AWS CLI

The examples below assume an AWS profile being used for the AWS CLI commands.

```bash
aws configure --profile computer-vision
```
Enter the IAM access keys, secret keys, region, and output type. For help configuring see [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

The IAM user must have these IAM permissions:
* [AWS Managed Policy] AWSS3FullAccess

* Enter AWS Access Key ID
* Enter AWS Secret Access Key
* Enter Default region name, e.g. *ap-southeast-2*
* Default output format *json*

## Create a LangChain AWS Lambda Layer

### Create a local zip file with the LangChain libraries

```bash
mkdir langchain-lambda-layer
cd langchain-lambda-layer
mkdir python
cd python
pip3 install langchain -t .
cd ..
zip -r langchain-layer.zip .  
```

### Publish an AWS Lambda layer from a local zip file

```bash
aws lambda publish-layer-version \
  --layer-name langchain-layer-v1 \
  --description "LangChain Bedrock orchestration library" \
  --license-info "MIT" \
  --compatible-architectures x86_64 \
  --zip-file fileb://langchain-layer.zip \
  --compatible-runtimes python3.11 python3.12 \
  --profile computer-vision > lambda-publish-layer-version.json
```

## Step #: Create an IAM Role to be Assumed by AWS Lambda

#### Create a trust policy

Create a trust policy file that grants the AWS Lambda permission to assume a new execution role.

```json
cat > iam-trust-policy-document.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
```

Run the following command in the AWS CLI to create an IAM role with the preceding trust policy.

```bash
aws --profile computer-vision iam create-role --role-name langchain-bedrock-orchestration-v1 --assume-role-policy-document file://iam-trust-policy-document.json > iam-create-role.json
```

To add permissions to the role, use the attach-policy-to-role command. Start by adding the AWSLambdaBasicExecutionRole managed policy.

```bash
aws --profile computer-vision iam attach-role-policy --role-name langchain-bedrock-orchestration-v1 --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

### Create AWS Lambda function code

The following example logs the values of environment variables and the event object.

```bash
cat > index.py <<EOF
import json

def lambda_handler(event, context):
    # TODO implement
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
EOF
```
Create a deployment package.

```bash
zip function.zip index.py
```

### Create AWS Lambda function

```bash
aws lambda create-function \
--profile computer-vision \
--function-name langchain-bedrock-orchestration-v1 \
--zip-file fileb://function.zip \
--handler index.handler \
--runtime python3.12 \
--role $(jq --raw-output '.Role.Arn' iam-create-role.json) \
--description "LangChain Bedrock orchestration function" \
--timeout 60 \
--tags Project=langchain-bedrock \
--layers $(jq --raw-output '.LayerArn' lambda-publish-layer-version.json)
```





