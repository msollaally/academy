pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET = 'msollaacademy'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo 'Starting pipeline execution...'
                    echo 'Downloading code from GitHub repository...'
                }
                git branch: 'main', 
                url: 'https://github.com/msollaally/academy'
                echo '✓ Code checkout completed successfully'
            }
        }
        
        stage('Validate Files') {
            steps {
                script {
                    echo 'Checking if all required files exist...'
                    if (fileExists('site/index.html')) {
                        echo '✓ index.html found'
                    } else {
                        error('Missing index.html file!')
                    }
                    
                    if (fileExists('site/course.html')) {
                        echo '✓ course.html found'
                    } else {
                        error('Missing course.html file!')
                    }
                    
                    if (fileExists('site/resources.html')) {
                        echo '✓ resources.html found'
                    } else {
                        error('Missing resources.html file!')
                    }
                    echo '✓ All required files are present'
                }
            }
        }
        
        stage('Deploy to S3') {
            steps {
                script {
                    echo 'Starting deployment to AWS S3...'
                    echo "Target: s3://${S3_BUCKET}"
                    echo "Region: ${AWS_REGION}"
                }
                
                withAWS(credentials: 'aws-s3-capstone-creds', region: "${AWS_REGION}") {
                    sh """
                    echo "Syncing files to S3 bucket..."
                    aws s3 sync site/ s3://${S3_BUCKET}/ --acl public-read --delete
                    echo "✓ Files uploaded successfully!"
                    echo "✓ Website should now be live!"
                    """
                }
            }
        }
    }
    
    post {
        success {
            script {
                echo 'PIPELINE EXECUTED SUCCESSFULLY!'
                echo 'Your website is now live at:'
                echo "http://${S3_BUCKET}.s3-website-${AWS_REGION}.amazonaws.com"
            }
        }
        failure {
            script {
                echo 'PIPELINE EXECUTION FAILED!'
                echo 'Check the console output above for errors.'
            }
        }
        always {
            script {
                echo '--- Pipeline execution completed ---'
                echo 'Build URL: ' + env.BUILD_URL
            }
        }
    }
}
