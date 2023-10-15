pipeline {
    agent any
    environment {
        DOCKER_PASS = credentials('docker-pass')
    }
    stages {
        stage("Building the Student Survey Image") {
            steps {
                script {
                    checkout scm
                    sh 'rm -rf *.war'
                    sh 'jar -cvf pages.war -C /var/lib/jenkins/workspace/645-hw2-docker/ .'
                    sh 'echo ${BUILD_TIMESTAMP}'
                    sh "cat ~/password.txt | sudo docker login --username 645docker --password-stdin"
                    sh 'echo "Success" ' 
                    def customImage = docker.build("645docker/645-hw2:${BUILD_TIMESTAMP}")
                }
            }
        }
        stage("Pushing Image to DockerHub") {
            steps {
                script {
                    sh 'docker push 645docker/645-hw2:${BUILD_TIMESTAMP}'
                }
            }
        }
        stage("Deploying to Rancher as single pod") {
            steps {
                sh 'kubectl set image deployment/stusurvey-pipeline stusurvey-pipeline=645docker/645-hw2:${BUILD_TIMESTAMP} -n jenkins-pipeline'
            }
        }
        stage("Deploying to Rancher as with load balancer") {
            steps {
                sh 'kubectl set image deployment/studentsurvey645-lb studentsurvey645-lb=645docker/645-hw2:${BUILD_TIMESTAMP} -n jenkins-pipeline'
            }
        }
    }
}
