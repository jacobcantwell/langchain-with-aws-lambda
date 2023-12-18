# langchain-with-aws-lambda

Add [LangChain](https://www.langchain.com/) LLM orchestration to AWS Lambda

## Create a LangChain AWS Lambda Layer

### Prerequisites

* Python 3.11.x
* Pip3
* AWS CLI installed and configured

### Create a local .zip file with the LangChain libraries

```
mkdir langchain-lambda-layer
cd langchain-lambda-layer
mkdir python
cd python
pip3 install langchain -t .
cd ..
zip -r langchain-layer.zip .  
```

