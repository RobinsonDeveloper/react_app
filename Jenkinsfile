pipeline {
    agent any

    tools {
        nodejs 'node22'
    }

    environment {
        REACT_EC2 = 'ubuntu@172.31.23.112'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Pulling code from GitHub..."
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to React EC2') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no $REACT_EC2 "sudo mkdir -p /var/www/html/webapp"
                    ssh -o StrictHostKeyChecking=no $REACT_EC2 "sudo rm -rf /var/www/html/webapp/*"
                    ssh -o StrictHostKeyChecking=no $REACT_EC2 "mkdir -p /tmp/webapp"
                    scp -r dist/* $REACT_EC2:/tmp/webapp/
                    ssh -o StrictHostKeyChecking=no $REACT_EC2 "sudo cp -r /tmp/webapp/* /var/www/html/webapp/"
                    ssh -o StrictHostKeyChecking=no $REACT_EC2 "sudo systemctl restart nginx"
                '''
            }
        }
    }

    post {
        success { echo "React App Deployed Successfully!" }
        failure { echo "Build Failed!" }
    }
}