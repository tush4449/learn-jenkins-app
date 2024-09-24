pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
        steps {
            sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
            '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm test
                    if [[ -f /var/jenkins_home/workspace/learn-jenkins-app/build/index.html ]]; then
                        echo "Index file present."
                    else
                        echo "Index file absent"
                    fi
                '''
            }
        }
    }
}
