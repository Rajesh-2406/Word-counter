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
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 600222537277.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker tag word-counter:latest 600222537277.dkr.ecr.us-east-1.amazonaws.com/word-counter:latest'
                    sh 'docker push 600222537277.dkr.ecr.us-east-1.amazonaws.com/word-counter:latest'
                }
            }
        }
          
        stage('K8S Deploy') {
            steps {
                script {
                   {    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                        export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                        export AWS_DEFAULT_REGION=us-east-1
                        sh 'aws eks update-kubeconfig --name k8-cluster --region us-east-1'
                        sh 'kubectl apply -f EKS-Deployment.yaml'
                    }
                }
            }
        }
    }
}