pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello Tushar!!! This is pipeline saying hello....'
                sh 'echo "This is outputted in build number two as sh is being used now."'
                sh 'whoami'
            }
        }
    }
}
