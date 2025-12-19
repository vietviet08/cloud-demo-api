pipeline {
    agent any
    
    environment {
        EC2_HOST = 'ec2-18-141-170-131.ap-southeast-1.compute.amazonaws.com'
        EC2_USER = 'ubuntu'
        PEM_FILE = '/var/lib/jenkins/jobs/BE/builds/sing.pem'
        PROJECT_DIR = '/home/ubuntu/employee/api'
        COMPOSE_FILE = '/home/ubuntu/employee/docker-compose.yml'
    }
    
    stages {
        stage('SSH and Deploy API') {
            steps {
                script {
                    sh """
                        chmod 400 ${env.PEM_FILE} || true
                        ssh -i "${env.PEM_FILE}" -o StrictHostKeyChecking=no ${env.EC2_USER}@${env.EC2_HOST} '
                            cd ${env.PROJECT_DIR} && \\
                            echo "Pulling latest code from master branch..." && \\
                            git fetch origin && \\
                            git checkout master && \\
                            git pull origin master && \\
                            echo "Building and starting API container..." && \\
                            docker-compose -f ${env.COMPOSE_FILE} stop spring-boot-app || true && \\
                            docker-compose -f ${env.COMPOSE_FILE} rm -f spring-boot-app || true && \\
                            docker-compose -f ${env.COMPOSE_FILE} build spring-boot-app && \\
                            docker-compose -f ${env.COMPOSE_FILE} up -d spring-boot-app && \\
                            echo "API container started successfully" && \\
                            docker-compose -f ${env.COMPOSE_FILE} ps spring-boot-app
                        '
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo 'API deployment succeeded!'
        }
        failure {
            echo 'API deployment failed!'
        }
    }
}
