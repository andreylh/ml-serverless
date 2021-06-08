# Serverless PyTorch

Sample project to create a Serverless Inference using Lambda + PyTorch

### Create Cloud 9

Create a Cloud 9 environment, access it and expand storage:

```
pip3 install --user --upgrade boto3
export instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
python -c "import boto3
import os
from botocore.exceptions import ClientError 
ec2 = boto3.client('ec2')
volume_info = ec2.describe_volumes(
    Filters=[
        {
            'Name': 'attachment.instance-id',
            'Values': [
                os.getenv('instance_id')
            ]
        }
    ]
)
volume_id = volume_info['Volumes'][0]['VolumeId']
try:
    resize = ec2.modify_volume(    
            VolumeId=volume_id,    
            Size=50
    )
    print(resize)
except ClientError as e:
    if e.response['Error']['Code'] == 'InvalidParameterValue':
        print('ERROR MESSAGE: {}'.format(e))"
if [ $? -eq 0 ]; then
    sudo reboot
fi
```

Create a Role with admin privileges and add it to Cloud9 environment.

Disable *AWS managed temporary credentials* option on Cloud9 env.

Check env is using new role:

```
aws sts get-caller-identity
```

### Install Docker and CDK

Check if docker is installed and CDK:

```
docker --version

cdk --version
```

### Clone this repo

```
git clone https://github.com/evandrofranco/ml-serverless

cd ml-serverless
```

### Setup Local Profile

```
export AWS_DEFAULT_REGION=us-east-1
```

### Create an ECR repository

```
aws ecr create-repository --repository-name lambda-serverless-inference --image-scanning-configuration scanOnPush=true
```

**Tip: store the value of repositoryUri and ECR url on following env vars:**

```
export REPO_URI=<your repo uri>
export ECR_URL=<AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
```

### Login on ECR

```
aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_URL
```

### Sam Deploy

```
sam build

sam deploy -g
```

Whenever asked, enter $REPO_URI information on reporitory info.

### Testing

```
export API_GW_URL=<API_GATEWAY_URL>

curl $API_GW_URL?url=https://path/to/image/on/web.jpg
```