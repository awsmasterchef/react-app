pipeline {
    agent any
    triggers {
        githubPush()
      }
 
    tools {
       nodejs 'nodejs'
    }   
 
    environment {
        GITLAB_TOKEN = credentials('FRONTEND_REPO_GITLAB_TOKEN') 
        AWS_REGION = 'ap-south-1'
        BUCKET_NAME = 'pipeline-test-bucket-iot'
    }
 
    stages {
        
        stage('Code Pull') {
            steps {
                script {
                    echo "BRANCH_NAME is: ${env.BRANCH_NAME}"            
            git url: "https://abhishekbhatia:${GITLAB_TOKEN}@git.nagarro.com/NAGCOEIOT/iotplatform-v2/platform-tenant-fe.git",
                branch: "${env.BRANCH_NAME}" 
        }
            }
        }
        
        stage('NPM Install') {
            steps {
                sh "npm install"
            }
        }
        
        stage('NPM Build') {
            steps {
                sh "npm run build"
            }
        }
        
        stage('S3 Deploy') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding', 
                    credentialsId: 'AWS_CREDENTIALS' // Use the correct credentials ID
                ]]) {
                    sh """
                    aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                    aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                    aws configure set region ${AWS_REGION}
                    aws s3 sync dist/ s3://${BUCKET_NAME} --delete
                    aws cloudfront create-invalidation --distribution-id E1FR1B4SU1D9O1 --paths "/*"
                    """
                }
            }
        }
    }  
}
