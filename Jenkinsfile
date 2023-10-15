pipeline {
    agent any
    environment {
        DOCKER_CRED = credentials('docker_login')
    }
    stages {
        stage("SCM") {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/candibod/645-hw2-survey'
            }
        }
        stage("Building the war file") {
            steps {
                script {
                    sh 'rm -rf *.war'
                    sh 'jar -cvf pages.war -C /var/lib/jenkins/workspace/645-hw2-deploy .'
                    sh 'echo ${BUILD_TIMESTAMP}'
                }
            }
        }
        stage("Docker login") {
            steps {
                sh 'echo $DOCKER_CRED_PSW | docker login -u $DOCKER_CRED_USR --password-stdin'
            }
        }
        stage("Docker build and push") {
            steps {
                script {
                    sh 'docker build -t 645docker/645-hw2:$BUILD_TIMESTAMP .'
                    sh 'docker push 645docker/645-hw2:$BUILD_TIMESTAMP'
                }
            }
        }
        stage("updating the image of the deployed cluster") {
            steps {
                sh 'kubectl set image deployment/k8deploy container-0=645docker/645-hw2:${BUILD_TIMESTAMP} -n default'
            }
        }
    }
}
