pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'rm -rf KDT-Frontend KDT-Frontend@tmp'
                dir("./frontend") {
                    nodejs(nodeJSInstallationName: 'NodeJS') {
                        // CI=false 추가 및 npm install에 --legacy-peer-deps 옵션 추가
                        sh 'npm install --legacy-peer-deps && CI=false npm run build'
                    }
                }
            }
        }
        stage('Compression') {
            steps {
                sh '''
                rm -rf /var/lib/jenkins/workspace/BestFriend_KDT-Frontend_jenkins/frontend/node_modules
                sudo tar -cvf /var/lib/jenkins/workspace/BestFriend_KDT-Frontend_jenkins/frontend/build.tar build
                '''
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials: ['ec2-user']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@3.35.110.201 "pwd"
                    scp /var/lib/jenkins/workspace/BestFriend_KDT-Frontend_jenkins/frontend/build.tar ubuntu@3.35.110.201:/home/ubuntu
                    ssh -t ubuntu@3.35.110.201 ./deploy.sh
                    '''
                }
            }
        }
    }
}