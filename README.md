# Serverless PyTorch

Sample project to create a Serverless Inference using Lambda + PyTorch

### Setup Local Profile

```
export AWS_DEFAULT_REGION=us-east-1

export AWS_DEFAULT_PROFILE=<your-profile>
```

### Create an ECR repository

```
aws ecr create-repository --repository-name lambda-serverless-inference --image-scanning-configuration scanOnPush=true
```

### Login on ECR

```
aws ecr get-login-password | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
```

### Sam Deploy

```
sam build

sam deploy -g
```

### Testing

```
curl <API_GATEWAY_URL>?url=https://path/to/image/on/web.jpg
```