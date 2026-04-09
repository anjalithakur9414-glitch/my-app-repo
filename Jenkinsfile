pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "468172542998"
        REGION = "ap-south-1"
        REPO_NAME = "my-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/anjalithakur9414-glitch/my-app-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $REPO_NAME:$IMAGE_TAG .'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker tag $REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                docker push $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
stage('Deploy to EKS') {
    steps {
        sh '''
        aws eks update-kubeconfig --region $REGION --name cicd-eks-cluster
        kubectl apply -f https://raw.githubusercontent.com/anjalithakur9414-glitch/my-manifests-repo/main/deployment.yaml
        kubectl apply -f https://raw.githubusercontent.com/anjalithakur9414-glitch/my-manifests-repo/main/service.yaml
        '''
    }
}
