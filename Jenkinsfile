pipeline {
    agent any
    
    tools {
       nodejs 'nodejs'
    }   
 
    environment {
        AWS_REGION = 'ap-south-1'
        BUCKET_NAME = 'mydemoappawsmasterchef'
    }
 
    stages {
        
        stage('Code Pull') {
            steps {
                script {
                    echo "BRANCH_NAME is: ${env.BRANCH_NAME}"            
            git url: "https://github.com/awsmasterchef/react-app.git",
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
                    aws s3 sync build/ s3://${BUCKET_NAME} --delete
                    """
                }
            }
        }
    }  
}
