pipeline {
    agent any
    tools {
        gradle 'gradle-8.10.2'
    }
    stages {
        stage('Build') {
            steps {
                sh 'rm -rf KDT-Frontend KDT-Frontend@tmp'
                sh 'gradle clean build'
            }
        }
        stage('Check and Free Port') {
            steps {
                echo 'Checking if port 8080 is in use...'
                script {
                    def portInUse = sh(script: "netstat -tuln | grep ':8080 '", returnStatus: true)
                    if (portInUse == 0) {
                        echo 'Port 8080 is in use. Attempting to free it...'
                        sh(script: 'sudo fuser -k 8080/tcp || true') // 오류를 무시하고 실행
                        echo 'Port 8080 freed successfully.'
                    } else {
                        echo 'Port 8080 is not in use. Proceeding with deployment...'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials: ['ec2-user']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@3.34.100.57 "pwd"
                    scp /var/lib/jenkins/workspace/BestFriend_back-end-bf_jenkins/build/libs/back-end-bf-0.0.1-SNAPSHOT.war ubuntu@3.34.100.57:/home/ubuntu
                    ssh -t ubuntu@3.34.100.57 ./deploy.sh
                    '''
                }
            }
        }
    }
}