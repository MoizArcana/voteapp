pipeline {
    agent any

    environment {
        EC2_HOST = "ubuntu@54.89.241.89"
        COMPOSE_FILE = "docker-compose.yml"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/MoizArcana/voteapp.git' 
            }
        }

        stage('Push Code to EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh """
                        # Archive and copy the entire project to EC2
                        tar czf app.tar.gz .
                        scp -i \$SSH_KEY -o StrictHostKeyChecking=no app.tar.gz ${EC2_HOST}:~/

                        # SSH into EC2 and unpack
                        ssh -i \$SSH_KEY -o StrictHostKeyChecking=no ${EC2_HOST} << 'EOF'
                        rm -rf voteappjenkins
                        mkdir voteappjenkins
                        tar -xzf app.tar.gz -C voteappjenkins
                        cd voteappjenkins
                        docker compose down || true
                        docker compose pull || true
                        docker compose up -d --build
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Project deployed to EC2 using Docker Compose successfully.'
        }
        failure {
            echo 'There was an issue during deployment.'
        }
    }
}

