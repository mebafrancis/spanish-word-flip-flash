pipeline {
    agent none

    options {
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:22-alpine'
                }
            }
            steps {
                sh '''
                    if [ -d node_modules ]; then rm -rf node_modules; fi
                    npm ci --no-audit --no-fund
                    npm run build
                '''
            }
        }

        stage('test') {
            parallel {
                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:22-alpine'
                        }
                    }
                    steps {
                        sh '''
                            if [ -d node_modules ]; then rm -rf node_modules; fi
                            npm ci --no-audit --no-fund
                            npm run test:unit -- --reporter=verbose
                        '''
                    }
                }
                stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                        }
                    }
                    steps {
                        sh '''
                            if [ -d node_modules ]; then rm -rf node_modules; fi
                            npm ci --no-audit --no-fund
                            npm run test:e2e -- --reporter=list
                        '''
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }
    }
}