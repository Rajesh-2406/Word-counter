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
                sh 'docker build -t Word-counter .'
            }
        }
        
        stage('Pushing to ECR') {
            steps {
                withAWS(credentials: 'AWS-CREDS', region: 'us-east-1') {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 600222537277.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker tag firstrepo:latest 600222537277.dkr.ecr.us-east-1.amazonaws.com/firstrepo:latest'
                    sh 'docker push 600222537277.dkr.ecr.us-east-1.amazonaws.com/firstrepo:latest'
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