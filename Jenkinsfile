pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '515b6f1e-35bd-406a-bb9f-911c08d30591'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root:root'
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

        stage('Run Test') {
            parallel {
                stage('Unit Test') {
                            agent {
                                docker {
                                    image 'node:18-alpine'
                                    reuseNode true
                                    args '-u root:root'
                                }
                            }

                            steps {
                                sh '''
                                    test -f build/index.html
                                    npm test
                                '''
                            }
                        }

                        stage('E2E') {
                            agent {
                                docker {
                                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                    reuseNode true
                                    args '-u root:root'
                                }
                            }

                            steps {
                                sh '''
                                    npm install serve
                                    node_modules/.bin/serve -s build &
                                    sleep 10
                                    npx playwright test --reporter=html
                                '''
                            }
                        }
            }
        }
        
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    echo 'Small change'                    
                    npm install netlify-cli -g
                    netlify --version
                    echo "Deploying to production site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --dir=build --prod
                '''
            }
        } 
    }

    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
