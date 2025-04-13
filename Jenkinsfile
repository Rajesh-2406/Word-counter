pipeline {
    agent any

    stages {
        stage('Configure') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Rajesh-2406/Word-counter']])
            }
        }
        stage('Building image') {
            steps {
                sh 'docker build -t word-counter .'
            }
        }
        
        stage('Pushing to ECR') {
            steps {
                withAWS(credentials: 'AWS-CREDS', region: 'us-east-1') {
                    sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/j0a5v2d2'
                    sh 'docker tag k8-repo:latest public.ecr.aws/j0a5v2d2/k8-repo:latest'
                    sh 'docker push public.ecr.aws/j0a5v2d2/k8-repo:latest'
                }
            }
        }
          
        stage('K8S Deploy') {
            steps {
                script {
                    withAWS(credentials: 'AWS-CREDS', region: 'us-east-1') {
                        sh 'aws eks update-kubeconfig --name k8-project-cluster --region us-east-1'
                        sh 'kubectl apply -f EKS-Deployment.yaml'
                    }
                }
            }
        }
    }
}