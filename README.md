
# How to deploy flask app in ECS cluster

## Prerequiste: Confogure AWS CLI with access and secret key for the user

## Run on local
```
sudo docker build -t flask_docker .
sudo docker run -p 5000:5000 -dit flask_docker
curl -k http://127.0.0.1:5000 
curl -k http://127.0.0.1:5000/status
```
## Push Docker Image to ECR
```
aws ecr create-repository --repository-name flask_docker 
aws ecr get-login --no-include-email | sh 
IMAGE_REPO=$(aws ecr describe-repositories --repository-names flask_docker --query 'repositories[0].repositoryUri' --output text)
docker tag flask_docker:latest $IMAGE_REPO:v1
docker push $IMAGE_REPO:v1
```
## Create CloudFormation Stacks
```
aws cloudformation create-stack --template-body file://$PWD/vpc.yml --stack-name vpc
aws cloudformation create-stack --template-body file://$PWD/iam.yml --stack-name iam --capabilities CAPABILITY_IAM
aws cloudformation create-stack --template-body file://$PWD/app-cluster.yml --stack-name app-cluster
aws cloudformation create-stack --template-body file://$PWD/api.yml --stack-name api
```
#### Edit the api.yml to update Image tag/URL under Task > ContainerDefinitions and, <br>

## with a script deply the cloud formation
```
./deploy.sh
```
## for CI/CD pipeline
##### AWS cloud formation plugins is required in Jenkins
Jenkinsfile
