pipeline {
    agent any
    environment {
        DOCKER_HOST="localhost:2345"
        STAGE_INSTANCE="ubuntu@18.197.188.53"
    }
    stages {
        stage('Setup SSH tunnel') {
        steps {
                script {
                sh "ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -i ~/.ssh/Jenkins-key-aws.pem -nNT -L ${DOCKER_HOST}:/var/run/docker.sock ${STAGE_INSTANCE} & echo \$! > /tmp/tunnel.pid"
                    // sometimes it's not enough time to make a tunnel, add sleep
                sleep 15
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                sh "DOCKER_HOST=${DOCKER_HOST} docker ps -a"
                    // DOCKER_HOST=${DOCKER_HOST} docker run hello-world 
                }
            }
        }
}
    post {
        always {
            script {
                sh "pkill -F /tmp/tunnel.pid"
            }
        }
    }
}

