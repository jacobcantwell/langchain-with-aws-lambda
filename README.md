# Using LangChain wth AWS Lambda

Add [LangChain](https://www.langchain.com/) LLM orchestration to AWS Lambda

## Prerequisites

* Python 3.11.x
* Pip3
* AWS CLI installed and configured
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

```
mkdir langchain-lambda-layer
cd langchain-lambda-layer
mkdir python
cd python
pip3 install langchain -t .
cd ..
zip -r langchain-layer.zip .  
```

### Publish an AWS Lambda layer from a local zip file

```
aws lambda publish-layer-version \
  --layer-name langchain-layer-v1 \
  --description "LangChain Bedrock orchestration library" \
  --license-info "MIT" \
  --compatible-architectures x86_64 \
  --zip-file fileb://langchain-layer.zip \
  --compatible-runtimes python3.11 python3.12 \
  --profile computer-vision > publish-layer-version.json
```

### Create Lambda IAM role

### Create S3 bucket for lambda function code

### Upload AWS Lambda function code






