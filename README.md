# Using LangChain wth AWS Lambda

Add [LangChain](https://www.langchain.com/) LLM orchestration to AWS Lambda

## Create a LangChain AWS Lambda Layer

### Prerequisites

* Python 3.11.x
* Pip3
* AWS CLI installed and configured

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
  --description "LangChain LLM orchestration library" \
  --license-info "MIT" \
  --compatible-architectures x86_64 \
  --zip-file langchain-layer.zip \
  --compatible-runtimes python3.11 python3.12 \
  --profile default
```




