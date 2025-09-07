pipeline {
    agent any

    environment {
        AWS_REGION = 'eu-north-1'
        GIT_REPO = 'https://github.com/Sravss11/NewRepo.git'
        PATH = "/usr/local/bin:/opt/homebrew/bin:$PATH"
        S3_BUCKET = 'automation-bucket-poc'
        S3_FOLDER = 'Dev_Bucket'
    }

    triggers {
        pollSCM('* * * * *') // Polling every minute for changes in 'feature' branch
    }

    stages {
        stage('Clone Feature Branch') {
            steps {
                git credentialsId: 'git-creds', branch: 'feature', url: "${env.GIT_REPO}"
                echo "Cloned feature branch successfully."
            }
        }

        stage('Upload to Dev S3 Bucket') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'aws-creds',
                                     usernameVariable: 'AWS_ACCESS_KEY_ID',
                                     passwordVariable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        echo "Uploading files to Dev S3 Bucket..."
                        aws s3 sync . s3://${S3_BUCKET}/${S3_FOLDER}/ --exclude ".git/*" --exclude "Jenkinsfile" --region ${AWS_REGION}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Upload to Dev S3 Bucket completed successfully."
        }
        failure {
            echo "Pipeline execution failed."
        }
    }
}
