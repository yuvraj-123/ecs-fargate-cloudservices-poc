pipeline {
    agent any
    parameters {
        string(name: 'ECS_CFTSTACKNAME', defaultValue: 'ecs-fargate-cloudservices-poc', description: 'Enter the AWS ECS CFT Stack Name')

        text(name: 'AWSACCOUNTID', defaultValue: '', description: 'Enter the AWS Account ID, where to push image to ECR')

        text(name: 'REGION', defaultValue: 'us-east-1', description: 'Enter the AWS Region in which ECR exists')

        choice(choices: ['dev' , 'staging'], description: 'Choose a deployment environment.', name: 'DEPLOY_ENV')
    
        string(name: 'REPOSITORYNAME', defaultValue: 'ecs-fargate-cloudservices-poc-repo', description: 'Enter the AWS ECR Repo name to create n push the image')

        string(name: 'ECR_IMAGE_TAG', defaultValue: '', description: 'Enter the AWS ECR image tag')

        string(name: 'CLUSTERNAME', defaultValue: 'ecs-fargate-cloudservices-poc-cluster', description: 'Enter the AWS ECS cluster name')

        string(name: 'TASKDEFNAME', defaultValue: 'ecs-fargate-cloudservices-poc-taskdef', description: 'Enter the AWS ECS Task Definition Name')

        string(name: 'CONTAINERNAME', defaultValue: 'ecs-fargate-cloudservices-poc-container', description: 'Enter the AWS ECS Task Definition Container Name')

        string(name: 'VPC_ID', defaultValue: '', description: 'Enter the AWS VPC ID')

        string(name: 'SUBNET_A', defaultValue: '', description: 'Enter the AWS Subnet_A ID')

        string(name: 'SUBNET_B', defaultValue: '', description: 'Enter the AWS Subnet_B ID')

        string(name: 'SECURITY_GROUP_NAME', defaultValue: 'ECSbatchprocessingSecurityGroup', description: 'Enter the AWS SECURITY_GROUP_NAME')

        string(name: 'ECSEVENTROLENAME', defaultValue: 'ecsRunTaskRole', description: 'Enter the AWS IAM ECS Event Role Name')

        string(name: 'ECSTASKEXECUTIONROLENAME', defaultValue: 'ecsTaskExecutionRole', description: 'Enter the AWS IAM ECS Task Execution Role')

        string(name: 'SERVICE_NAME', defaultValue: 'ecs-fargate-service', description: 'Enter the ECS Service name')
    }
    stages {
        stage('Create ECS Infra stack') {
            when {
                // Check if Dev
                expression { params.DEPLOY_ENV == 'dev' }
            }
            steps {
                sh "aws cloudformation create-stack --stack-name ${params.ECS_CFTSTACKNAME} --template-body file://create-ecs-stack.yml \
                --region 'us-east-1' --parameters \
                ParameterKey=Image,ParameterValue=${params.AWSACCOUNTID}.dkr.ecr.${params.REGION}.amazonaws.com/${params.REPOSITORYNAME}:${params.ECR_IMAGE_TAG} \
                ParameterKey=RepositoryName,ParameterValue=${params.REPOSITORYNAME} \
                ParameterKey=ClusterName,ParameterValue=${params.CLUSTERNAME} \
                ParameterKey=TaskDefinitionName,ParameterValue=${params.TASKDEFNAME} \
                ParameterKey=ContainerName,ParameterValue=${params.CONTAINERNAME} \
                ParameterKey=VPC,ParameterValue=${params.VPC_ID} \
                ParameterKey=SubnetA,ParameterValue=${params.SUBNET_A} \
                ParameterKey=SubnetB,ParameterValue=${params.SUBNET_B} \
                ParameterKey=SGName,ParameterValue=${params.SECURITY_GROUP_NAME} \
                ParameterKey=ecsTaskRoleName,ParameterValue=${params.ECSEVENTROLENAME} \
                ParameterKey=ecsTaskExecutionRoleName,ParameterValue=${params.ECSTASKEXECUTIONROLENAME} \
                ParameterKey=ServiceName,ParameterValue=${params.SERVICE_NAME} \
                --capabilities CAPABILITY_NAMED_IAM"
            }
        }
        stage('Check ECS Infra stack status') {
            steps {
                sh "aws cloudformation describe-stacks --stack-name ${params.ECS_CFTSTACKNAME} --query 'Stacks[0].StackStatus' --output text"
            }
        }
        stage('To wait for CloudFormation to finish creating a ECS Infra stack') {
            steps {
                sh "aws cloudformation wait stack-create-complete --stack-name ${params.ECS_CFTSTACKNAME}"
            }
        }
    }
}